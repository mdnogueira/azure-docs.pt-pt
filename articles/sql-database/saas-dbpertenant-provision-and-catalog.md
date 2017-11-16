---
title: "Aprovisionar novos inquilinos numa aplicação multi-inquilino que utiliza a SQL Database do Azure | Microsoft Docs"
description: "Saiba como aprovisionar e novos inquilinos na aplicação de SaaS SQL Database do Azure multi-inquilino de catálogo"
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
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 909be4e9d0586a71d6ebbf55c3d62400513b6228
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="learn-how-to-provision-and-catalog-new-tenants-and-register-them-in-the-catalog"></a>Saiba como aprovisionar novos inquilinos do catálogo e registe-os no catálogo

Neste tutorial, pode saber mais sobre os padrões de aprovisionamento e o catálogo de SaaS, e como são implementados na aplicação Wingtip SaaS. Criar e inicializar novas bases de dados do inquilino e registe-os no catálogo de inquilino da aplicação. O catálogo é uma base de dados que mantém o mapeamento entre muitos inquilinos da aplicação SaaS e os respetivos dados. O catálogo desempenha uma função importante instruir os pedidos de aplicações para a base de dados correto.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Aprovisionar um novo inquilino único, incluindo avance através da forma como esta se encontra implementada
> * Aprovisionar um lote de inquilinos adicionais


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação Wingtip SaaS é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de Catalogação SaaS

Uma cópia de base de dados multi-inquilino aplicação SaaS, é importante saber onde estão armazenadas informações para cada inquilino. O padrão de catálogo de SaaS, uma base de dados do catálogo é utilizado para conter o mapeamento entre cada inquilino e onde os dados são armazenados. A aplicação Wingtip SaaS utiliza um inquilino único por arquitetura de base de dados, mas o padrão básico do armazenamento de mapeamento de inquilino-para-base de dados no catálogo se aplica se é utilizada uma base de dados multi-inquilino ou único inquilino.

Cada inquilino é atribuído uma chave que identifica-los no catálogo e que está mapeado para a localização da base de dados adequada. Na aplicação Wingtip SaaS, a chave tem o formato de um hash do nome do inquilino. Isto permite que o inquilino parte do nome do URL da aplicação a ser utilizado para construir a chave. É possível utilizar outros esquemas de chave de inquilino.  

O catálogo permite que o nome ou localização da base de dados para ser alterados com um impacto mínimo na aplicação.  No modelo de base de dados do multi-inquilino, isto também permite a 'Mover' um inquilino entre bases de dados.  O catálogo também pode ser utilizado para indicar se um inquilino ou a base de dados está offline para manutenção ou outras ações. Isto é explorou no [restaurar tutorial de inquilino único](saas-dbpertenant-restore-single-tenant.md).

Além disso, o catálogo, que está em vigor uma base de dados de gestão para uma aplicação SaaS, pode armazenar adicionais inquilinos ou de metadados de base de dados, como a edição da base de dados, ou camada de versão do esquema, o plano de serviço ou SLAs oferecidos aos inquilinos e outras informações que lhe permite gestão de aplicações, o suporte ao cliente ou processos de devops.  

Para além da aplicação SaaS, o catálogo pode ativar as ferramentas de base de dados.  No exemplo Wingtip SaaS, o catálogo é utilizado para ativar consulta de inquilino em vários locais, explorou no [tutorial análise ad hoc](saas-tenancy-adhoc-analytics.md). Gestão de tarefas entre bases de dados é explorou no [gestão esquema](saas-tenancy-schema-management.md) e [inquilino análise](saas-tenancy-tenant-analytics.md) tutoriais. 

Na aplicação Wingtip SaaS, o catálogo é implementado utilizando às funcionalidades de gestão de partições horizontais o [biblioteca de cliente de base de dados elásticas (EDCL)](sql-database-elastic-database-client-library.md). O EDCL ativa uma aplicação criar, gerir e utilize um mapa de partições horizontais cópias de base de dados. Um mapa de partições horizontais contém uma lista de partições horizontais (bases de dados) e o mapeamento entre as bases de dados e de chaves (inquilinos).  Funções EDCL podem ser utilizadas de aplicações ou scripts do PowerShell durante o aprovisionamento para criar as entradas no mapa de partições horizontais do inquilino e a partir de aplicações de forma eficiente ligar à base de dados correto. EDCL coloca em cache as informações da ligação para minimizar o tráfego para a base de dados do catálogo e acelerar a aplicação.  

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis na base de dados do catálogo, mas *não deverá editá-los*! Edite os dados de mapeamento apenas com as APIs da Biblioteca de Clientes da Base de Dados Elástica. Se manipular diretamente os dados de mapeamento arrisca-se a danificar o catálogo, além de não ser suportado.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de aprovisionamento de SaaS

Quando um novo inquilino numa aplicação SaaS que utiliza um modelo de base de dados de inquilino único uma nova base de dados do inquilino de integração têm de ser aprovisionada.  Tem de ser criado na localização adequada e escalão de serviço, inicializado com o esquema apropriado e dados de referência e, em seguida, registados no catálogo sob a chave de inquilino adequado.  

Diferentes abordagens podem ser utilizadas para base de dados de aprovisionamento, o que pode incluir executar scripts de SQL, implementar um bacpac ou copiar uma base de dados do modelo 'dourada'.  

A abordagem de aprovisionamento que utiliza tem de ser compreender na sua estratégia de gestão de esquema geral, o que tem de garantir que as novas bases de dados são aprovisionados com o esquema mais recente.  Isto é explorou no [tutorial de gestão de esquema](saas-tenancy-schema-management.md).  

Os Wingtip SaaS aplicação Aprovisiona novos inquilinos ao copiar uma base de dados dourada denominada basetenantdb, implementados no servidor de catálogo.  Aprovisionamento pode ser integrado na aplicação como parte de uma experiência de inscrição, e/ou suportado offline com scripts. Este tutorial explorar aprovisionamento através do PowerShell. Os scripts de aprovisionamento copiar basetenantdb para criar uma nova base de dados do inquilino num conjunto elástico, em seguida, inicializá-lo com as informações de inquilino específico e registá-lo no mapa de partições horizontais de catálogo.  Na aplicação de exemplo, as bases de dados são fornecidos nomes com base no nome do inquilino, mas não se trata de uma parte crucial do padrão – a utilização do catálogo permite que qualquer nome a atribuir à base de dados. + 


## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts de Wingtip SaaS e o código fonte da aplicação, estão disponíveis no [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) repositório do github. [Passos para transferir os scripts de Wingtip SaaS](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).


## <a name="provision-and-catalog-detailed-walkthrough"></a>Instruções detalhadas sobre o aprovisionamento e a catalogação

Para compreender a forma como a aplicação de Wingtip implementa novo inquilino aprovisionamento, adicione um ponto de interrupção e passo através do fluxo de trabalho ao aprovisionamento de um inquilino:

1. Abra... \\Learning módulos\\ProvisionAndCatalog\\_demonstração ProvisionAndCatalog.ps1_ e defina os seguintes parâmetros:
   * **$TenantName** = o nome do novo local (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos venue predefinidos: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = **1**, definida como **1** para *aprovisionar um único inquilino*.

1. Adicionar um ponto de interrupção, colocando o cursor em qualquer lugar na linha 48, a linha que diz: *inquilino novo '*e prima **F9**.

   ![ponto de interrupção](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Para executar a prima script **F5**.

1. Depois de parar a execução do script, o ponto de interrupção, prima **F11** para o passo no código.

   ![ponto de interrupção](media/saas-dbpertenant-provision-and-catalog/debug.png)



Execução do script de rastreio utilizando a **depurar** opções de menu - **F10** e **F11** para o passo ao longo de ou para as funções de chamada. Para obter mais informações sobre a depuração de scripts do PowerShell, consulte [sugestões sobre a trabalhar com e a depuração de scripts do PowerShell como](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Seguem-não se passos a seguir explicitamente, mas a obter uma explicação sobre o fluxo de trabalho, siga os passos durante a depuração do script:

1. Módulo **Importar o SubscriptionManagement.psm1**, que contém as funções para iniciar sessão no Azure e selecionar a subscrição do Azure com a qual está a trabalhar.
1. Módulo **Importar o CatalogAndDatabaseManagement.psm1**, que fornece um catálogo e uma abstração de nível de inquilino sobre as funções de [Gestão de Partições Horizontais](sql-database-elastic-scale-shard-map-management.md). Este é um módulo importante, que contém grande parte do padrão de catalogação e que vale a pena explorar.
1. **Obter os detalhes de configuração**. Avance para o Get-configuração (F11) e ver como a configuração de aplicação é especificada. Os nomes dos recursos, nem como os outros valores específicos da aplicação, são definidos aqui. No entanto, não altere nenhum destes valores até estar familiarizado com os scripts.
1. **Obter o objeto de catálogo**. Avance para o Get-catálogo que composes e devolve um objeto de catálogo que é utilizado no script de nível mais elevado.  Esta função utiliza funções de gestão de partições horizontais que são importadas a partir **AzureShardManagement.psm1**. O objeto do catálogo é composto pelo seguinte:
   * $catalogServerFullyQualifiedName é construído com o radical padrão e o nome de utilizador: _catalog-\<utilizador\>.database.windows.net_.
   * $catalogDatabaseName é obtido a partir da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado a partir da base de dados do catálogo.
   * O objeto $shardMap é inicializado a partir do mapa de partições horizontais *tenantcatalog* na base de dados do catálogo.
   Um objeto de catálogo é composto, devolvido e utilizado no script de nível mais elevado.
1. **Calcular a nova chave de inquilino**. É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
1. **Verificar se a chave de inquilino existe**. O catálogo é verificado para garantir que a chave está disponível.
1. **A base de dados do inquilino é aprovisionada com New-TenantDatabase**. Utilize **F11** passo na e ver como a base de dados é aprovisionado com um [modelo Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

O nome de base de dados é construído a partir do nome de inquilino para ficar claro quais as partições horizontais que pertencem a que inquilino. (Facilmente pode ser utilizadas outras estratégias para atribuir nomes a base de dados.) + Um modelo do Resource Manager é utilizado para criar uma base de dados do inquilino ao copiar um dourada da base de dados (baseTenantDB) no servidor de catálogo. Uma abordagem alternativa foi possível criar uma base de dados em branco e, em seguida, inicializá-lo através da importação de um bacpac ou para executar um script de inicialização a partir de uma localização conhecida.  

O modelo do Resource Manager é a pasta de Modules\Common\ ...\Learning: *tenantdatabasecopytemplate.json*

Depois da base de dados do inquilino é criada, em seguida, é mais **inicializado com o nome de venue (inquilino) e o tipo de venue**. Também pode utilizar outra inicialização aqui.

O **base de dados de inquilino está registado no catálogo** com *adicionar TenantDatabaseToCatalog* utilizando a chave de inquilino. Utilize **F11** para avançar para os detalhes:

* A base de dados do catálogo é adicionada ao mapa de partições horizontais (a lista de bases de dados conhecidas).
* É criado o mapeamento que liga o valor da chave à partição horizontal.
* Dados meta adicionais (nome do venue) sobre o inquilino são adicionados à tabela inquilinos no catálogo.  A tabela de inquilinos não faz parte do esquema ShardManagement e não está instalada pelo EDCL.  Esta tabela ilustra a forma como a base de dados do catálogo pode ser expandido para suportar dados específicos da aplicação adicionais.   


Depois de ter concluído o aprovisionamento, execução devolve para original *demonstração ProvisionAndCatalog* script, que abre o **eventos** página para o novo inquilino no browser:

   ![eventos](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionar um lote de inquilinos

Neste exercício aprovisiona um lote de 17 inquilinos. Recomenda-se que aprovisionar este lote de inquilinos antes de iniciar os outros tutoriais de Wingtip SaaS, pelo que não existe mais do que alguns bases de dados para trabalhar com.

1. Abra... \\Learning módulos\\ProvisionAndCatalog\\*demonstração ProvisionAndCatalog.ps1* no *ISE do PowerShell* e altere o *$ DemoScenario* parâmetro para 3:
   * **$DemoScenario** = **3**, altere para **3** para *aprovisionar um lote de inquilinos*.
1. Prima **F5** e execute o script.

O script implementa um lote de inquilinos adicionais. Utiliza um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e, em seguida, delega o aprovisionamento de cada base de dados a um modelo ligado. Utilizar modelos desta forma permite que o Azure Resource Manager funcione como o mediador no processo de aprovisionamento do script. Os modelos aprovisionam as bases de dados em paralelo sempre que podem e, se necessário, processam as tentativas ao otimizar o processo geral. O script é idempotent, por isso, se esta falha ou interrompe por qualquer motivo, execute-o novamente.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificar o lote de inquilinos implementados com êxito

* Abra o *tenants1* servidor ao navegar para a lista de servidores no [portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**e certifique-se de que o lote de 17 bases de dados adicionais estão agora no lista:

   ![lista de bases de dados](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões de aprovisionamento não incluídos neste tutorial incluem:

**Pré-aprovisionamento de bases de dados.** O padrão de pré-aprovisionamento exploits de facto de que as bases de dados num conjunto elástico não adicionar custos adicionais associados. Faturação é para o conjunto elástico, não as bases de dados, e as bases de dados Inativas consumam não existem recursos. Pré-aprovisionar um conjunto de bases de dados e atribuir-lhes quando for necessário, tempo de integração de inquilino pode ser significativamente reduzido. O número de bases de dados previamente aprovisionadas pode ser ajustado conforme necessário para manter uma memória intermédia adequada para a taxa de aprovisionamento previsível.

**Aprovisionamento automático.** O padrão de aprovisionamento automático de um serviço de aprovisionamento dedicada é utilizado para aprovisionar automaticamente servidores, os conjuntos e as bases de dados conforme necessário – incluindo as bases de dados previamente aprovisionamento no conjuntos elásticos, se assim o desejar. E, se as bases de dados forem desativadas e eliminadas, os espaços nos conjuntos elásticos poderão ser preenchidos pelo serviço de aprovisionamento, conforme desejado. Tal serviço pode ser simples ou complexas – por exemplo, processamento aprovisionar em várias localizações geográficas e pode configurar a georreplicação automaticamente se a estratégia de que está a ser utilizada para recuperação após desastre. Com o padrão de aprovisionamento automático, uma aplicação cliente ou script poderia submeter um pedido de aprovisionamento a uma fila para ser processado pelo serviço de aprovisionamento e, em seguida, poderia consultar o serviço para determinar se tinha sido concluído. Se for utilizado a pré-aprovisionamento, os pedidos poderiam ser processados rapidamente com o serviço de gestão do aprovisionamento de uma base de dados de substituição em execução em segundo plano.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Aprovisionar um inquilino individual novo
> * Aprovisionar um lote de inquilinos adicionais
> * Avance para os detalhes de aprovisionamento de inquilinos e registá-los para o catálogo

Repita o [tutorial de monitorização de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos Adicionais

* Adicionais [tutoriais tirar partido da aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
* [How to Debug Scripts in Windows PowerShell ISE (Como Depurar os Scripts do ISE do Windows PowerShell)](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
