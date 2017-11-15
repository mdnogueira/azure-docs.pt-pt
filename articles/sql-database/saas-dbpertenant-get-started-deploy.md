---
title: Tutorial de SaaS multi-inquilino - SQL Database do Azure | Microsoft Docs
description: "Implementar e explorar a Wingtip SaaS multi-inquilino de aplicação, que demonstra padrões de SaaS a utilizar a SQL Database do Azure."
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
ms.openlocfilehash: 6270656ba172f1e85df557d59173ecdcf7d6cbcb
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>Implementar e explorar uma aplicação de multi-inquilino que utiliza a base de dados SQL do Azure - Wingtip SaaS

Neste tutorial, implementar e explorar a aplicação Wingtip SaaS. A aplicação utiliza uma base de dados por inquilino, o padrão da aplicação SaaS, para servir vários inquilinos. A aplicação foi concebida para demonstram as funcionalidades da SQL Database do Azure que simplificam a ativar cenários de SaaS.

Cinco minutos depois de clicar no botão *Implementar no Azure* abaixo, tem uma aplicação SaaS multi-inquilino, a utilizar a Base de Dados SQL, ativa e em execução na cloud. A aplicação é implementada com três inquilinos de exemplo, cada uma com a sua própria base de dados, todos os implementado num agrupamento elástico de SQL. A aplicação é implementada a sua subscrição do Azure, dando-lhe acesso total a explorar e trabalhar com os componentes de aplicações individuais. Os scripts de código e gestão da origem de aplicação estão disponíveis no repositório WingtipSaaS GitHub.


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

## <a name="deploy-the-wingtip-saas-application"></a>Implementar a aplicação Wingtip SaaS

Implementar o Wingtip aplicação SaaS:

1. Ao clicar no **implementar no Azure** botão abre o portal do Azure para o modelo de implementação Wingtip SaaS. O modelo requer dois valores de parâmetro; um nome para um novo grupo de recursos e um nome de utilizador que distingue esta implementação de outras implementações da aplicação Wingtip SaaS. O passo seguinte fornece detalhes para definir estes valores.

   Certifique-se a ter em atenção os valores exatos que utilizar, pois precisará de introduzi-las para um ficheiro de configuração mais tarde.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Introduza os valores dos parâmetros necessários para a implementação:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls do servidor são intencionalmente não seguras para fins de demonstração. **Criar um novo grupo de recursos**e não utilizam grupos de recursos, servidores ou conjuntos existente. Não utilize a aplicação ou a quaisquer recursos cria, para produção. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.

    * **Grupo de recursos** - selecione **criar nova** e fornecer um **nome** para o grupo de recursos. Selecione um **localização** na lista pendente.
    * **Utilizador** -alguns recursos requerem nomes globalmente exclusivos. Para garantir a exclusividade, sempre que implementar a aplicação de fornecer um valor para diferenciar recursos criar, a partir dos recursos criados por qualquer outra implementação da aplicação Wingtip. É recomendado utilizar um nome de **Utilizador** curto, tais como as suas iniciais e um número (por exemplo, *bg1*) e, em seguida, utilizá-lo no nome do grupo de recursos (por exemplo, *wingtip-bg1*). O **utilizador** parâmetro só pode conter letras, números e hífenes (sem espaços). O primeiro e último carateres têm de ser uma letra ou um número (recomenda-se apenas a utilização de minúsculas).


1. **Implemente a aplicação**.

    * Clique para aceitar os termos e condições.
    * Clique em **Comprar**.

1. Monitorize o estado da implementação ao clicar em **Notificações** (o ícone de campainha à direita da caixa de pesquisa). Implementar a aplicação Wingtip SaaS demora cerca de cinco minutos.

   ![implementação concluída com êxito](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Transferir e desbloquear os scripts de Wingtip SaaS

Enquanto a aplicação está a implementar, transfira os scripts de gestão e o código de origem.

> [!IMPORTANT]
> Conteúdo executável (scripts, dlls) pode estar bloqueado pelo Windows quando zip ficheiros são transferidos a partir de uma origem externa e extraiu. Quando extrair os scripts a partir de um ficheiro zip, siga os passos abaixo para desbloquear o ficheiro. zip antes de a extrair. Isto garante que os scripts estão autorizados a executar.

1. Navegue até à [o repositório do github Wingtip SaaS](https://github.com/Microsoft/WingtipSaaS).
1. Clique em **clonar ou transferir**.
1. Clique em **transferir ZIP** e guarde o ficheiro.
1. Clique com botão direito do **WingtipSaaS master.zip** do ficheiro e selecione **propriedades**.
1. No **geral** separador, selecione **desbloqueio**e clique em **aplicar**.
1. Clique em **OK**.
1. Extraia os ficheiros.

Scripts estão localizados no *... \\WingtipSaaS-master\\Learning módulos* pasta.

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualizar o ficheiro de configuração para esta implementação

Antes de executar quaisquer scripts, defina o *grupo de recursos* e *utilizador* valores na **UserConfig.psm1**. Defina estas variáveis para os valores que definiu durante a implementação.

1. Abra ...\\Módulos de Aprendizagem\\*UserConfig.psm1* no *ISE do PowerShell*.
1. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
1. Guarde as alterações!

Definir este aqui simplesmente evita que tenha que que atualizar estes valores específicos da implementação em cada script.

## <a name="run-the-application"></a>Executar a aplicação

A aplicação apresenta os locais, como salas de espetáculos, clubes de jazz, clubes desportivos, onde os eventos são realizados. Venues registar-se como clientes (ou inquilinos) da plataforma Wingtip, para uma forma fácil de listar eventos e propor pedidos de suporte. Cada local obtém uma aplicação Web personalizada para gerir e listar os seus eventos e vender bilhetes de forma independente e isolada dos outros inquilinos. Nos bastidores, a cada inquilino obtém uma base de dados do SQL implementada para um agrupamento elástico de SQL.

Um **Hub de Eventos** central fornece uma lista de URLs de inquilinos específicos da implementação.

1. Abra o _Hub de eventos_ no seu browser: http://events.wtp.&lt; UTILIZADOR&gt;. trafficmanager.net (substituir pelo nome de utilizador da sua implementação):

    ![hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Para controlar a distribuição de pedidos recebidos, as utilizações de aplicação [ *Traffic Manager do Azure*](../traffic-manager/traffic-manager-overview.md). As páginas de eventos, que são específicas do inquilino, requerem que os nomes de inquilino sejam incluídos nos URLs. Todos os URLs de inquilinos incluem o valor específico do *Utilizador* e seguem este formato: http://events.wtp.&lt;UTILIZADOR&gt;.trafficmanager.net/*fabrikamjazzclub*. A aplicação de eventos analisa o nome do inquilino no URL e utiliza-o para criar uma chave para aceder a um catálogo com a [*gestão de mapas de partições horizontais*](sql-database-elastic-scale-shard-map-management.md). O catálogo mapeia a chave para a localização do inquilino da base de dados. O **Hub de eventos** utiliza metadados expandido no catálogo para obter o nome do inquilino associado a cada base de dados para fornecer a lista de URLs.

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

A implementação inicial cria três inquilinos de exemplo, mas vamos criar outro inquilino para ver como este problema afeta a aplicação implementada. O fluxo de trabalho do inquilinos aprovisionamento de Wingtip SaaS está detalhado no [tutorial de aprovisionamento e o catálogo](saas-dbpertenant-provision-and-catalog.md). Neste passo, criar rapidamente um novo inquilino.

1. Abra ...\\Módulos de Aprendizagem\Aprovisionamento e Catálogo \\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell*.
1. Prima **F5** para executar o script (mantenha os valores predefinidos por agora).

   > [!NOTE]
   > Utilizam scripts de Wingtip SaaS muitos *$PSScriptRoot* para permitir a navegação em pastas para chamar as funções em outros scripts. Esta variável só é avaliada quando o script é executado, premindo **F5**.  Realce e em execução uma seleção (**F8**) pode resultar em erros, por isso, prima **F5** quando executar scripts.

A nova base de dados do inquilino é criado num agrupamento elástico de SQL, inicializado e registada no catálogo. Após o aprovisionamento com êxito, o novo inquilino do vender de permissão *eventos* site é apresentada no browser:

![Novo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de eventos* e ao novo inquilino é apresentado na lista.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que já iniciou a execução de carga na coleção de inquilinos, vamos ver alguns dos recursos que foram implementados:

1. No [portal do Azure](http://portal.azure.com), navegue até à sua lista de servidores SQL e abra o **catálogo -&lt;utilizador&gt;**  servidor. O servidor de catálogo contém duas bases de dados. O **tenantcatalog**e o **basetenantdb** (vazio *dourada* ou base de dados do modelo que é copiado para criar novos inquilinos).

   ![bases de dados](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Regressar à sua lista de servidores SQL e abra o **tenants1 -&lt;utilizador&gt;**  servidor que contém as bases de dados do inquilino. Cada base de dados do inquilino é um _elástico Standard_ base de dados num conjunto standard 50 eDTU. Repare também existe uma _Red Maple Racing_ base de dados, a base de dados do inquilino aprovisionados anteriormente.

   ![servidor](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Se o gerador de carga estiver a ser executado há vários minutos, deverão estar disponíveis dados suficientes para começar a ver algumas das outras capacidades de monitorização incorporadas nos conjuntos e nas bases de dados.

1. Navegue para o servidor **tenants1 -&lt;utilizador&gt;**e clique em **Pool1** para ver a utilização de recursos para o conjunto (o gerador de carga foi executado para uma hora nos gráficos seguintes):

   ![monitorização do conjunto](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

Estes dois gráficos ilustram bem como os conjuntos elásticos e a Base de Dados SQL são adequados para as cargas de trabalho da aplicação SaaS. Quatro bases de dados que podem ser expandidas cada uma até um máximo de 40 eDTUs e que são facilmente suportadas num conjunto de 50 eDTUs. Se estes tenham sido aprovisionados como bases de dados autónomo, fariam cada têm de ser um S2 (50 DTU) para suportar os bursts. O custo de 4 bases de dados do autónomo S2 seria quase 3 vezes o preço do conjunto, e o conjunto ainda tem muitos headroom para muitas bases de dados mais. Em situações de mundo real, os clientes de base de dados do SQL Server estão atualmente em execução até 500 bases de dados em 200 conjuntos de eDTU. Para obter mais informações, veja o [tutorial de monitorização do desempenho](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]

> * Como implementar a aplicação Wingtip SaaS
> * Mais informações sobre os servidores, os conjuntos e as bases de dados que constituem a aplicação
> * Como os inquilinos são mapeados para os dados com o *catálogo*
> * Como aprovisionar inquilinos novos
> * Como ver a utilização do conjunto para monitorizar a atividade dos inquilinos
> * Como eliminar os recursos de exemplo para parar a faturação relacionada

Experimente agora o [tutorial de aprovisionamento e o catálogo](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais que criar na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Para saber mais sobre os conjuntos elásticos, veja [ *O que é um conjunto elástico do SQL do Azure?*](sql-database-elastic-pool.md)
* Para saber mais sobre as tarefas elásticas, veja [*Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)*](sql-database-elastic-jobs-overview.md)
* Para saber mais sobre as aplicações SaaS multi-inquilino, veja [*Design patterns for multi-tenant SaaS applications (Padrões de conceção para aplicações SaaS multi-inquilino)*](saas-tenancy-app-design-patterns.md)
