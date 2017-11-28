---
title: "Restaurar uma base de dados SQL do Azure numa aplicação SaaS multi-inquilino | Microsoft Docs"
description: "Saiba como restaurar uma base de dados do SQL Server de inquilinos único após a eliminação acidental de dados"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: ee2bc6d8b75b92243c0550db0044895e41c9474b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>Restaurar SQL database do Azure um único inquilino numa aplicação SaaS multi-inquilino

A aplicação de Wingtip bilhetes SaaS baseia-se através de um modelo de base de dados por inquilino, onde cada inquilino tem os seus próprios base de dados. Uma das vantagens deste modelo é que é mais fácil restaurar dados de um único inquilino do isolamento sem afetar outros inquilinos.

Neste tutorial aprende dois padrões de recuperação de dados:

> [!div class="checklist"]

> * Restaurar uma base de dados para uma base de dados em paralelo (side lado a lado)
> * Restaurar uma base de dados no local


|||
|:--|:--|
| **Restaurar inquilino para um ponto anterior no tempo, numa base de dados paralela** | Este padrão pode ser utilizado pelo inquilino para revisão, auditoria, de compatibilidade, etc. A base de dados original permanece inalterado e online. |
| **Restaurar inquilino no local** | Este padrão é normalmente utilizado para recuperar um inquilino para um ponto anterior no tempo, depois de um inquilino elimina acidentalmente a dados. A base de dados original é colocado offline e substituído com a base de dados restaurada. |
|||

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação Wingtip SaaS é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>Introdução ao padrão de restauro de inquilino de SaaS

Para o padrão de restauro do inquilino, existem dois padrões de simples para restaurar dados de um inquilino individual. Uma vez que são isoladas umas das outras bases de dados do inquilino, restaurar um inquilino não tem impacto nos dados de qualquer outro inquilino.

No primeiro padrão, os dados são restaurados para uma nova base de dados. O inquilino, em seguida, é dado acesso a esta base de dados juntamente com os seus dados de produção. Este padrão permite que um administrador de inquilinos rever os dados restaurados e potencialmente utilizá-lo para seletivamente substituir os valores de dados atual. Está a funcionar para o designer de aplicações de SaaS para determinar a sofisticadas tal como as opções de recuperação de dados devem ser. Basta capaz de ver dados no Estado em que estava num determinado ponto no tempo pode ser tudo o que é necessário em alguns cenários. Se a base de dados utiliza [georreplicação](sql-database-geo-replication-overview.md), recomenda-se copiar os dados necessários a partir da cópia restaurada na base de dados original. Se substituir a base de dados original com a base de dados restaurada, terá de reconfigurar e ressincronize a georreplicação.

O padrão de segundo, assume-se de que o inquilino sofra uma perda ou corrupção de dados, a base de dados de produção do inquilino é restaurada para um ponto anterior no tempo. No restauro local padrão, o inquilino seja colocado offline por um breve período de tempo enquanto a base de dados é restaurada e recolocado online. A base de dados original é eliminado, mas pode ainda ser restaurado a partir de se precisar de voltar atrás para um ponto mesmo anterior no tempo. Uma variação deste padrão de poderia mudar o nome da base de dados em vez de eliminá-lo, apesar de mudar o nome da base de dados não oferece nenhuma vantagem adicional em termos de segurança dos dados.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicação Wingtip bilhetes SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um inquilino eliminação acidental de dados

Para demonstrar estes cenários de recuperação, temos de *acidentalmente* eliminar alguns dados de uma das bases de dados do inquilino. Enquanto pode eliminar qualquer registo, o passo seguinte configura a demonstração não obter bloqueado por violações de integridade referencial! Também adiciona alguns dados de compra de pedido de suporte pode utilizar mais tarde no *tutoriais de análise de SaaS Wingtip*.

Executar o script de gerador de permissão e criar dados adicionais. O gerador de permissão intencionalmente não adquira os pedidos de suporte para cada evento do último inquilinos.

1. Abra... \\Learning módulos\\utilitários\\*demonstração TicketGenerator.ps1* no *ISE do PowerShell*
1. Prima **F5** para executar o script e gerar os clientes e permissão de dados de vendas.


### <a name="open-the-events-app-to-review-the-current-events"></a>Abra a aplicação de eventos para rever os eventos atuais

1. Abra o *Hub de eventos* (http://events.wtp.&lt; utilizador&gt;. trafficmanager.net) e clique em **Contoso Concert Hall**:

   ![hub de eventos](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Desloque-se a lista de eventos e tome nota do último evento na lista:

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>Execute o cenário de demonstração para elimina acidentalmente o último evento

1. Abra... \\Learning módulos\\continuidade do negócio e recuperação após desastre\\RestoreTenant\\*demonstração RestoreTenant.ps1* no *ISE do PowerShell*e defina o valor seguinte:
   * **$DemoScenario** = **1**, definida como **1** -eliminar eventos com vendas sem permissão.
1. Prima **F5** para executar o script e eliminar o último evento. Deverá ver uma mensagem de confirmação semelhante ao seguinte:

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Abre a página de eventos da Contoso. Desloque para baixo e certifique-se de que o evento é removido. Se o evento é ainda na lista, clique em Atualizar e certifique-se de que é removido.

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar uma base de dados de inquilino em paralelo com a base de dados de produção

Neste exercício restaura a base de dados de Contoso Concert Hall para um ponto no tempo antes do evento foi eliminado. Depois do evento é eliminado nos passos anteriores, que pretende recuperar e ver os dados eliminados. Não é necessário restaurar a base de dados de produção com o registo foi eliminado, mas tiver de recuperar a base de dados antigo para aceder aos dados antigos por outras razões de negócio.

 O *restauro TenantInParallel.ps1* script cria um inquilino paralelo base de dados e uma catálogo paralelas entrada ambos os com o nome *ContosoConcertHall\_antigo*. Este padrão de restauro é mais adequada para recuperar a partir de uma perda de dados secundárias ou conformidade e auditoria de cenários de recuperação. Também é a abordagem recomendada quando estiver a utilizar [georreplicação](sql-database-geo-replication-overview.md).

1. Concluir o [simular um utilizador acidentalmente eliminar dados](#simulate-a-tenant-accidentally-deleting-data) secção.
1. Abra... \\Learning módulos\\continuidade do negócio e recuperação após desastre\\RestoreTenant\\_demonstração RestoreTenant.ps1_ no *ISE do PowerShell*.
1. Definir **$DemoScenario** = **2**, defina esta opção para **2** para *inquilino de restauro em paralelo*.
1. Prima **F5** para executar o script.

O script restaura a base de dados do inquilino (para uma base de dados paralela) para um ponto no tempo antes de eliminar o evento na secção anterior. -Cria uma segunda base de dados, remove quaisquer metadados de catálogo existente que existe nesta base de dados e adiciona a base de dados para o catálogo sob o *ContosoConcertHall\_antigo* entrada.

O script de demonstração abre a página de eventos no seu browser. Tenha em atenção a partir do URL: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` que isto é possível mostrar dados da base de dados restaurada onde *_old* é adicionada ao nome.

Desloque-se os eventos listados no browser para confirmar que o evento eliminado na secção anterior foi restaurado.

Tenha em atenção que o inquilino restaurado a exposição como um inquilino adicional, com as suas próprias aplicações de eventos para procurar pedidos de suporte, é pouco provável ser como iria fornecer um acesso de inquilino para restaurar dados, mas serve para ilustrar facilmente o padrão de restauro.

Na realidade, provavelmente, seria manter apenas esta base de dados restaurada durante um período de tempo definido. Pode eliminar a entrada de inquilino restaurada depois de terminar, chamando o *remover RestoredTenant.ps1* script.

1. Definir **$DemoScenario** para **4** para selecionar o *remover restaurada inquilino* cenário.
1. **Executar** **utilizando** **F5**
1. O *ContosoConcertHall\_antigo* entrada agora é eliminada do catálogo. Avançar e fechar a página de eventos para este inquilino no seu browser.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um inquilino no local, substituir a base de dados existente do inquilino

Neste exercício restaura o inquilino Contoso Concert Hall para um ponto no tempo antes do evento foi eliminado. O *restauro TenantInPlace* script restaura a base de dados atual do inquilino para uma nova base de dados que aponta para um ponto anterior no tempo e elimina a base de dados original. Este padrão de restauro é mais adequada para recuperar de danos nos dados grave porque poderá haver perda de dados significativas que o inquilino teria para acomodar.

1. Abra **demonstração RestoreTenant.ps1** ficheiro no ISE do PowerShell
1. Definir **$DemoScenario** para **5** para selecionar o *restaurar inquilino local cenário*.
1. Executar utilizando **F5**.

O script restaura a base de dados do inquilino para um ponto de cinco minutos antes do evento foi eliminado. Fazê-lo ao adotando primeiro o inquilino Contoso Concert Hall offline pelo que não são necessárias mais existem atualizações para os dados. Em seguida, uma base de dados paralela é criado para restaurar a partir do ponto de restauro e com o nome com um carimbo para garantir que o nome de base de dados não coincide com o nome de base de dados existente do inquilino. Em seguida, a base de dados do inquilino antigo é eliminado e a base de dados restaurada é mudado para o nome de base de dados original. Por fim, a Contoso Concert Hall seja colocado online, para permitir o acesso de aplicação para a base de dados restaurada.

Restaurar com êxito a base de dados para um ponto no tempo antes do evento foi eliminado. Abre a página Eventos, por isso, confirme o último evento foi restaurado.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Restaurar uma base de dados para uma base de dados em paralelo (side lado a lado)
> * Restaurar uma base de dados no local

[Gerir o esquema de base de dados do inquilino](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que tirar partido da aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Descrição geral da continuidade do negócio com a SQL Database do Azure](sql-database-business-continuity.md)
* [Saiba mais sobre as cópias de segurança de base de dados SQL](sql-database-automated-backups.md)
