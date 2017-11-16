---
title: Tutorial de SaaS multi-inquilino - SQL Database do Azure | Microsoft Docs
description: "Implementar e explorar a Wingtip bilhetes SaaS multi-inquilino de aplicação, que demonstra a base de dados por inquilino e outros padrões de SaaS a utilizar a SQL Database do Azure."
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
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: 9b1ae219eb1278b818e3e1d4237d04fe54c980ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implementar e explorar uma aplicação SaaS multi-inquilino que utiliza a base de dados por padrão do inquilino com a SQL Database do Azure

Neste tutorial, implementar e explorar Wingtip pedidos de suporte de SaaS base de dados por aplicação do inquilino. A aplicação utiliza um padrão de base de dados por inquilino, para armazenar os dados de vários inquilinos. A aplicação foi concebida para demonstram as funcionalidades da SQL Database do Azure que simplificam a ativar cenários de SaaS.

Cinco minutos depois de clicar no botão *Implementar no Azure* abaixo, tem uma aplicação SaaS multi-inquilino, a utilizar a Base de Dados SQL, ativa e em execução na cloud. A aplicação é implementada com três inquilinos de exemplo, cada uma com a sua própria base de dados, todos os implementado num agrupamento elástico de SQL. A aplicação é implementada a sua subscrição do Azure, dando-lhe acesso total a explorar e trabalhar com os componentes de aplicações individuais. Os scripts de código e gestão da origem de aplicação estão disponíveis no repositório GitHub WingtipTicketsSaaS DbPerTenant.


Neste tutorial, ficará a saber:

> [!div class="checklist"]

> * Como implementar a aplicação Wingtip SaaS
> * Onde obter o código fonte da aplicação e os scripts de gestão
> * Mais informações sobre os servidores, os conjuntos e as bases de dados que constituem a aplicação
> * Como os inquilinos estão mapeados para os seus dados com o *catálogo*
> * Como aprovisionar um novo inquilino
> * Como monitorizar a atividade de inquilino na aplicação

Para explorar vários padrões de conceção e de gestão SaaS, está disponível uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) que são criados após esta implementação inicial. Ao percorrer os tutoriais, estudo os scripts fornecidos e examine a forma como são implementados os diferentes padrões SaaS. Analise os scripts em cada tutorial para ter uma compreensão mais aprofundada sobre como implementar as várias funcionalidades da Base de Dados SQL que simplificam o desenvolvimento de aplicações SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implementar a aplicação de Wingtip bilhetes SaaS

Implemente a aplicação:

1. Ao clicar no **implementar no Azure** botão abre Wingtip pedidos de suporte de SaaS base de dados por modelo de implementação de inquilino no portal do Azure. O modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos e um nome de utilizador que distingue esta implementação de outras implementações de Wingtip pedidos de suporte de SaaS base de dados por aplicação do inquilino. O passo seguinte fornece detalhes para definir estes valores.

   Certifique-se a ter em atenção os valores exatos que utilizar, pois precisará de introduzi-las para um ficheiro de configuração mais tarde.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Introduza os valores dos parâmetros necessários para a implementação:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls do servidor são intencionalmente não seguras para fins de demonstração. **Criar um novo grupo de recursos**. Não utilize grupos de recursos, servidores ou conjuntos existente. Não utilize esta aplicação, scripts ou quaisquer recursos implementados para produção. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.

    * **Grupo de recursos** - selecione **criar nova** e fornecer um **nome** para o grupo de recursos. 
    * **Localização** - selecione um **localização** na lista pendente.
    * **Utilizador** -alguns recursos requerem nomes globalmente exclusivos. Para garantir a exclusividade, sempre que implementar a aplicação de fornecer um valor para diferenciar recursos criar, a partir dos recursos criados por qualquer outra implementação da aplicação Wingtip. É recomendado que utilize um curto **utilizador** nome, tal como as suas iniciais e um número (por exemplo, *af1*) e, em seguida, utilize-a no nome do grupo de recursos (por exemplo, *wingtip-af1*). O **utilizador** parâmetro só pode conter letras, números e hífenes (sem espaços). O primeiro e último carateres têm de ser uma letra ou um número (recomenda-se apenas a utilização de minúsculas).


1. **Implemente a aplicação**.

    * Clique para aceitar os termos e condições.
    * Clique em **Comprar**.

1. Monitorize o estado da implementação ao clicar em **Notificações** (o ícone de campainha à direita da caixa de pesquisa). Implementar a aplicação SaaS de bilhetes Wingtip demora cerca de cinco minutos.

   ![implementação concluída com êxito](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Transferir e os scripts de gestão Wingtip pedidos de desbloqueio

Enquanto a aplicação está a implementar, transfira os scripts de gestão e o código de origem.

> [!IMPORTANT]
> Conteúdo executável (scripts, dlls) pode estar bloqueado pelo Windows quando zip ficheiros são transferidos a partir de uma origem externa e extraiu. Quando extrair os scripts a partir de um ficheiro zip, siga os passos abaixo para desbloquear o ficheiro. zip antes de a extrair. Isto garante que os scripts estão autorizados a executar.

1. Navegue para o [repositório do GitHub WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Clique em **clonar ou transferir**.
1. Clique em **transferir ZIP** e guarde o ficheiro.
1. Clique com botão direito do **WingtipTicketsSaaS-DbPerTenant-master.zip** do ficheiro e selecione **propriedades**.
1. No **geral** separador, selecione **desbloqueio**e clique em **aplicar**.
1. Clique em **OK**.
1. Extraia os ficheiros.

Scripts estão localizados no *... \\Mestre de DbPerTenant WingtipTicketsSaaS\\Learning módulos* pasta.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualize o ficheiro de configuração de utilizador para esta implementação

Antes de executar quaisquer scripts, atualize o *grupo de recursos* e *utilizador* valores na **UserConfig.psm1**. Defina estas variáveis para os valores utilizados durante a implementação.

1. No *ISE do PowerShell*, abra... \\Learning módulos\\*UserConfig.psm1* 
1. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
1. Guarde as alterações!

Estes valores são referenciados quase todos os script.

## <a name="run-the-application"></a>Executar a aplicação

A aplicação está direcionada para venues, tais como concert halls, jazz clubs e clubs desporto, que alojam os eventos. Venues registar-se como clientes (ou inquilinos) Wingtip permissões, para uma forma fácil de listar eventos e propor pedidos de suporte. Cada venue obtém um web site personalizado para gerir e listar os respetivos eventos e propor bilhetes, independentes e isolados de outros inquilinos. Nos bastidores, a cada inquilino obtém uma base de dados do SQL implementada para um agrupamento elástico de SQL.

Um centro **Hub de eventos** página fornece uma lista de ligações para os inquilinos na sua implementação.

1. Abra o _Hub de eventos_ no seu browser: http://events.wingtip-dpt.&lt;utilizador&gt;. trafficmanager.net (substituir &lt;utilizador&gt; com o valor de utilizador da sua implementação):

    ![hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Para controlar a distribuição de pedidos recebidos, as utilizações de aplicação [ *Traffic Manager do Azure*](../traffic-manager/traffic-manager-overview.md). As páginas de eventos, que são específicas do inquilino, requerem que os nomes de inquilino sejam incluídos nos URLs. Todos os inquilinos URLs incluem específicos da sua *utilizador* valor e segue este formato: http://events.wingtipp-dpt.&lt;utilizador&gt;.trafficmanager.net/*fabrikamjazzclub*. A aplicação de eventos analisa o nome de inquilino a partir do URL e utiliza-o para criar uma chave para aceder a um catálogo implementado utilizando [ *gestão de mapa de partições horizontais*](sql-database-elastic-scale-shard-map-management.md). O catálogo mapeia a chave para a localização do inquilino da base de dados. O **Hub de eventos** utiliza metadados expandido no catálogo para obter o nome do inquilino associado a cada base de dados para fornecer a lista de URLs.

Num ambiente de produção, criaria normalmente um registo DNS CNAME para [*apontar um domínio de Internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do gestor de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação é implementada, vamos colocá-la para funcionar! O *demonstração LoadGenerator* script do PowerShell inicia uma carga de trabalho em execução em todas as bases de dados do inquilino. A carga do mundo real em muitas aplicações de SaaS é normalmente esporádicas e imprevisíveis. Para simular este tipo de carga, o gerador de dimensões produz uma carga distribuída a todos os inquilinos, com bursts aleatório em cada inquilino ocorrer em intervalos aleatório. Por isso, por este motivo que demora alguns minutos até o padrão de carga ideia, é melhor permitir que o gerador de execução para, pelo menos, três ou quatro minutos antes da carga de monitorização.

1. No *ISE do PowerShell*, abra a... \\Learning módulos\\utilitários\\*demonstração LoadGenerator.ps1* script.
1. Prima **F5** para executar o script e iniciar o gerador de carga (deixe os valores predefinidos do parâmetro por agora).

> [!IMPORTANT]
> Para executar outros scripts, abra uma nova janela do ISE do PowerShell. O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. O *demonstração LoadGenerator.ps1* script arranca, o script de gerador de carga real, que é executado como uma tarefa de primeiro plano e uma série de tarefas de geração de carregamento em segundo plano. Uma tarefa de gerador de carga é invocada para cada base de dados registada no catálogo. As tarefas estão em execução na sessão do PowerShell local, para que fechar a sessão do PowerShell deixa de todas as tarefas. Se suspender o seu computador, a geração de carga está em pausa e será retomada quando reativar o seu computador.

Depois do gerador de carga invoca as tarefas de geração de carga para cada inquilino, a tarefa de primeiro plano permanece no Estado invocar a tarefa, onde inicia tarefas em segundo plano adicionais para os novos inquilinos subsequentemente aprovisionados. Pode utilizar *Ctrl-C* ou prima o *parar* botão para parar a tarefa de primeiro plano, mas as tarefas em segundo plano existentes continuarão a geração de carga em cada base de dados. Se precisar de monitorizar e controlar as tarefas em segundo plano, utilize *Get-Job*, *Receive-Job* e *tarefa de paragem*. Durante a execução da tarefa de primeiro plano não é possível utilizar a mesma sessão do PowerShell para executar outros scripts. Para executar outros scripts, abra uma nova janela do ISE do PowerShell.

Se pretender reiniciar a sessão do gerador de carga, por exemplo, com diferentes parâmetros, pode parar a tarefa de primeiro plano e, em seguida, execute novamente o *demonstração LoadGenerator.ps1* script. Executar novamente *demonstração LoadGenerator.ps1* primeiras paragens qualquer atualmente em execução de tarefas e, em seguida, reinicia o gerador de dimensões, que inicia um novo conjunto de tarefas utilizando os parâmetros atuais.

Por agora, deixe o gerador de carga em execução no estado de invocar a tarefa.


## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

A implementação inicial cria três inquilinos de exemplo, mas vamos criar outro inquilino para ver como este problema afeta a aplicação implementada. O fluxo de trabalho do inquilinos aprovisionamento Wingtip bilhetes SaaS está detalhado no [tutorial de aprovisionamento e o catálogo](saas-dbpertenant-provision-and-catalog.md). Neste passo, criar rapidamente um novo inquilino.

1. No *ISE do PowerShell*, abra... \\Learning Modules\Provision e catálogo\\*demonstração ProvisionAndCatalog.ps1* .
1. Prima **F5** para executar o script (mantenha os valores predefinidos por agora).

   > [!NOTE]
   > Utilizam scripts de Wingtip SaaS muitos *$PSScriptRoot* para navegar pastas para chamar as funções em outros scripts. Esta variável só é avaliada quando o script completo é executado, premindo **F5**.  Realce e em execução uma seleção (**F8**) pode resultar em erros, por isso, prima **F5** quando executar scripts.

A nova base de dados do inquilino é criado num agrupamento elástico de SQL, inicializado e registada no catálogo. Após o aprovisionamento com êxito, ao novo inquilino *eventos* site é apresentada no browser:

![Novo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de eventos* e ao novo inquilino é apresentado na lista.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que já iniciou a execução de carga na coleção de inquilinos, vamos ver alguns dos recursos que foram implementados:

1. No [portal do Azure](http://portal.azure.com), navegue até à sua lista de servidores SQL e abra o **catálogo-dpt -&lt;utilizador&gt;**  servidor. O servidor de catálogo contém duas bases de dados, o **tenantcatalog** e **basetenantdb** (modelo base de dados que é copiado para criar novos inquilinos).

   ![bases de dados](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Regressar à sua lista de servidores SQL e abra o **tenants1-dpt -&lt;utilizador&gt;**  servidor que contém as bases de dados do inquilino. Cada base de dados do inquilino é um _elástico Standard_ base de dados num conjunto standard 50 eDTU. Repare também existe uma _Red Maple Racing_ base de dados, a base de dados do inquilino aprovisionados anteriormente.

   ![servidor](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Se o gerador de carga estiver a ser executado há vários minutos, deverão estar disponíveis dados suficientes para começar a ver algumas das outras capacidades de monitorização incorporadas nos conjuntos e nas bases de dados.

1. Navegue para o servidor **tenants1-dpt -&lt;utilizador&gt;**e clique em **Pool1** para ver a utilização de recursos para o conjunto (o gerador de carga foi executado para uma hora nos gráficos seguintes):

   ![monitorização do conjunto](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

O gráfico superior mostra a utilização de eDTU do conjunto, enquanto o gráfico de inferior mostra utiization eDTU das bases de 5 dados superiores no conjunto.  Estes dois gráficos ilustram bem como os conjuntos elásticos e a Base de Dados SQL são adequados para as cargas de trabalho da aplicação SaaS. Quatro bases de dados que podem ser expandidas cada uma até um máximo de 40 eDTUs e que são facilmente suportadas num conjunto de 50 eDTUs. Se estes tenham sido aprovisionados como bases de dados autónomo, fariam cada têm de ser um S2 (50 DTU) para suportar os bursts. O custo de 4 bases de dados do autónomo S2 seria quase 3 vezes o preço do conjunto, e o conjunto ainda tem muitos headroom para muitas bases de dados mais. Em situações de mundo real, os clientes de base de dados do SQL Server estão atualmente em execução até 500 bases de dados em 200 conjuntos de eDTU. Para obter mais informações, veja o [tutorial de monitorização do desempenho](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]

> * Como implementar a aplicação de Wingtip bilhetes SaaS
> * Mais informações sobre os servidores, os conjuntos e as bases de dados que constituem a aplicação
> * Como os inquilinos são mapeados para os dados com o *catálogo*
> * Como aprovisionar inquilinos novos
> * Como ver a utilização do conjunto para monitorizar a atividade dos inquilinos
> * Como eliminar os recursos de exemplo para parar a faturação relacionada

Experimente agora o [tutorial de aprovisionamento e o catálogo](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais que criar no Wingtip pedidos de suporte de SaaS base de dados por aplicação do inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Para saber mais sobre os conjuntos elásticos, veja [ *O que é um conjunto elástico do SQL do Azure?*](sql-database-elastic-pool.md)
* Para saber mais sobre as tarefas elásticas, veja [*Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)*](sql-database-elastic-jobs-overview.md)
* Para saber mais sobre as aplicações SaaS multi-inquilino, veja [*Design patterns for multi-tenant SaaS applications (Padrões de conceção para aplicações SaaS multi-inquilino)*](saas-tenancy-app-design-patterns.md)
