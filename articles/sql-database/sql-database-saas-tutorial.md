---
title: "Implementar e explorar a aplicação Wingtip Tickets Platform (WTP) (aplicação de exemplo SaaS com a Base de Dados SQL do Azure) | Microsoft Docs"
description: "Implementar e explorar uma aplicação SaaS de exemplo que utiliza a Base de Dados SQL do Azure"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Implementar e explorar uma Aplicação SaaS multi-inquilino que utiliza a Base de Dados SQL do Azure

Neste tutorial, vai implementar e explorar a aplicação SaaS Wingtip Tickets Platform (WTP). A aplicação utiliza uma base de dados por inquilino, o padrão da aplicação SaaS, para servir vários inquilinos. A aplicação foi concebida para demonstrar as funcionalidades da Base de Dados SQL do Azure que permitem cenários SaaS e padrões de conceção e gestão SaaS.

Cinco minutos depois de clicar no botão *Implementar no Azure* abaixo, tem uma aplicação SaaS multi-inquilino, a utilizar a Base de Dados SQL, ativa e em execução na cloud. A aplicação é implementada com três inquilinos de exemplo, cada um com as suas próprias bases de dados, todas implementadas num conjunto elástico do SQL. A aplicação é implementada para a sua subscrição Azure, concedendo-lhe acesso total para inspecionar e trabalhar com os componentes individuais da aplicação.

Neste tutorial, ficará a saber:

> [!div class="checklist"]

> * Como implementar a aplicação WTP
> * Mais informações sobre os servidores, os conjuntos e as bases de dados que constituem a aplicação
> * Como os inquilinos são mapeados para os dados com o *catálogo*
> * Como aprovisionar inquilinos novos
> * Como ver a utilização do conjunto para monitorizar a atividade dos inquilinos
> * Como eliminar os recursos de exemplo para parar a faturação relacionada

Para explorar vários padrões de conceção e de gestão SaaS, está disponível uma [série de tutoriais relacionados](sql-database-wtp-overview.md) que são criados após esta implementação inicial. Ao percorrer os tutoriais, estudo os scripts fornecidos e examine a forma como são implementados os diferentes padrões SaaS. Analise os scripts em cada tutorial para ter uma compreensão mais aprofundada sobre como implementar as várias funcionalidades da Base de Dados SQL que simplificam o desenvolvimento de aplicações SaaS.

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>Implementar a aplicação SaaS Wingtip Tickets Platform (WTP)

Implemente a Wingtip Tickets Platform em menos de cinco minutos:

1. Clique para implementar:

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Introduza os valores dos parâmetros necessários para a implementação:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls do servidor são intencionalmente não seguras para fins de demonstração. **Crie um novo grupo de recursos** e não utilize grupos de recursos, servidores ou conjuntos existentes nem utilize esta aplicação nem quaisquer recursos por ela criados para produção. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.

    * **Grupo de recursos** – Selecione **Criar novo** e forneça um **Nome** e **Localização**.
    * **Utilizador** – Alguns recursos exigem nomes exclusivos em todas as subscrições do Azure. Para garantir a exclusividade, forneça um valor para diferenciar os recursos criados por si dos recursos criados por outros utilizadores a implementar a aplicação Wingtip. É recomendado utilizar um nome de **Utilizador** curto, tais como as suas iniciais e um número (por exemplo, *bg1*) e, em seguida, utilizá-lo no nome do grupo de recursos (por exemplo, *wingtip-bg1*). O parâmetro **Utilizador** só pode conter letras, números e hífenes. O primeiro e último carateres têm de ser uma letra ou um número (recomenda-se apenas a utilização de minúsculas).

     ![modelo](./media/sql-database-saas-tutorial/template.png)

1. **Implemente a aplicação**.

    * Se aceitar os termos e condições, marque a caixa.
    * Selecione **Afixar ao dashboard**.
    * Clique em **Comprar**.

1. Monitorize o estado da implementação ao clicar em **Notificações** (o ícone de campainha à direita da caixa de pesquisa). A implementação da aplicação WTP demora cerca de quatro minutos.

   ![implementação concluída com êxito](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>Explorar a aplicação

A aplicação apresenta os locais, como salas de espetáculos, clubes de jazz, clubes desportivos, onde os eventos são realizados. Os locais registam-se como clientes (ou inquilinos) da Wingtip Tickets Platform (WTP), para obter uma forma fácil de listar os eventos e vender bilhetes. Cada local obtém uma aplicação Web personalizada para gerir e listar os seus eventos e vender bilhetes de forma independente e isolada dos outros inquilinos. Nos bastidores, cada inquilino obtém uma Base de Dados SQL implementada num conjunto elástico do SQL.

Um **Hub de Eventos** central fornece uma lista de URLs de inquilinos específicos da implementação. Todos os URLs de inquilinos incluem o valor específico do *Utilizador* e seguem este formato: http://events.wtp.&lt;UTILIZADOR&gt;.trafficmanager.net/*fabrikamjazzclub*. 

1. Abra o _Hub de Eventos_: http://events.wtp.&lt;UTILIZADOR&gt;.trafficmanager.net (substitua pelo seu nome de utilizador):

    ![hub de eventos](media/sql-database-saas-tutorial/events-hub.png)

1. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/sql-database-saas-tutorial/fabrikam.png)

1. Clique em **Bilhetes** e explore a compra de bilhetes para um evento.

A aplicação WTP utiliza o [*Gestor de Tráfego do Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para controlar a distribuição do tráfego recebido. As páginas de eventos, que são específicas do inquilino, requerem que os nomes de inquilino sejam incluídos nos URLs. A aplicação de eventos analisa o nome do inquilino no URL e utiliza-o para criar uma chave para aceder a um catálogo com a [*gestão de mapas de partições horizontais*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). O catálogo mapeia a chave para localização da base de dados do inquilino. O **Hub de Eventos** utiliza metadados expandidos no catálogo para obter o nome do inquilino associado a cada base de dados.

Num ambiente de produção, criaria normalmente um registo DNS CNAME para [*apontar um domínio de Internet da empresa*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) para o perfil do gestor de tráfego.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts da Wingtip Tickets e o código fonte da aplicação estão disponíveis no repositório do github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

A implementação inicial cria três inquilinos de exemplo, mas é necessário criar mais inquilinos para uma experiência otimizada do tutorial. Neste passo, vai criar rapidamente um inquilino novo. Posteriormente, deve aprofundar os detalhes do aprovisionamento de novos inquilinos no [Tutorial de aprovisionamento e catalogação](sql-database-saas-tutorial-provision-and-catalog.md), onde poderá ver como é simples implementar um componente de registo na aplicação e aprovisionar automaticamente inquilinos à medida que os clientes se inscrevem.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>Inicializar o ficheiro de configuração de utilizadores da implementação

Uma vez que este é o primeiro script que vai executar após a implementação da aplicação WTP inicial, deve atualizar o ficheiro de configuração de utilizadores. Atualize as variáveis com os valores específicos da implementação.

   1. Abra ...\\Módulos de Aprendizagem\\*UserConfig.psm1* no *ISE do PowerShell*.
   1. Modifique _$userConfig.ResourceGroupName_ para o _grupo de recursos_ definido quando implementou a aplicação.
   1. Modifique _$userConfig.Name_ para o nome de _Utilizador_ definido quando implementou a aplicação.

### <a name="provision-the-new-tenant"></a>Aprovisionar o novo inquilino

1. Abra ...\\Módulos de Aprendizagem\Aprovisionamento e Catálogo \\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell*.
1. Prima **F5** para executar o script (deixe os valores predefinidos do script por agora).

O novo inquilino é registado no catálogo. A base de dados é criada e adicionada ao conjunto elástico do SQL. Depois do aprovisionamento com êxito, o site de venda de bilhetes do novo inquilino é apresentado no browser:

![Novo inquilino](./media/sql-database-saas-tutorial/red-maple-racing.png)

Atualize o Hub de Eventos e verifique se o novo inquilino está na lista.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que temos várias bases de dados de inquilinos, vamos pô-las a trabalhar! É fornecido um script do PowerShell que simula uma carga de trabalho em execução em todas as bases de dados de inquilinos. Por predefinição, a carga é executada durante 60 minutos. A Wingtip Tickets é uma aplicação SaaS e a carga no contexto real de uma aplicação SaaS é, normalmente, esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários alguns minutos para que o padrão surja, pelo que deve deixar o gerador de carga ser executado cerca de 5 a 10 minutos antes de tentar monitorizar a carga das secções seguintes.

1. Abra ...\\Módulos de Aprendizagem\\Utilitários\\*Demo- LoadGenerator.psm1* no **ISE do PowerShell**.
1. Prima **F5** para executar o script e iniciar o gerador de carga (deixe os valores predefinidos do parâmetro por agora).

> [!IMPORTANT]
> O gerador de carga é executado como uma série de tarefas na sessão local do PowerShell, por isso mantenha a sessão do PowerShell aberta! Se fechar o PowerShell ou o separador onde o gerador de carga foi iniciado ou se suspender o computador, as tarefas serão interrompidas.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que explorou a aplicação, aprovisionou um novo inquilino e começou a executar uma carga na coleção de inquilinos, vamos ver alguns dos recursos que foram implementados.

1. No [portal do Azure](http://portal.azure.com), abra o servidor **catalog-&lt;UTILIZADOR&gt;** . O servidor de catálogo contém duas bases de dados. A **tenantcatalog**e a **basetenantdb** (uma base de dados *dourada* vazia que é copiada para criar novos inquilinos).

   ![bases de dados](./media/sql-database-saas-tutorial/databases.png)

1. Abra o servidor **tenants1-&lt;UTILIZADOR&gt;** , que contém as bases de dados de inquilinos. Tenha em atenção que cada base de dados de inquilinos é uma base de dados de _Padrão Elástico_ num conjunto padrão de 50 eDTUs. Repare também que existe uma base de dados _Red Maple Racing_, o inquilino aprovisionado anteriormente.

   ![servidor](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Se o gerador de carga estiver a ser executado há vários minutos, deverão estar disponíveis dados suficientes para começar a ver algumas das outras capacidades de monitorização incorporadas nos conjuntos e nas bases de dados.

1. Navegue até ao servidor *tenants1-&lt;UTILIZADOR&gt;* clique em **Pool1** para ver a utilização de recursos do conjunto:

   ![monitorização do conjunto](./media/sql-database-saas-tutorial/monitor-pool.png)

Estes dois gráficos ilustram bem como os conjuntos elásticos e a Base de Dados SQL são adequados para as cargas de trabalho da aplicação SaaS. Quatro bases de dados que podem ser expandidas cada uma até um máximo de 40 eDTUs e que são facilmente suportadas num conjunto de 50 eDTUs. Se cada base de dados fosse aprovisionada como uma base de dados autónoma, cada uma delas precisaria de ser uma S2 (50 DTUs) para suportar a expansão, mas o custo de quatro bases de dados S2 seria aproximadamente 3 vezes o preço do conjunto. E o conjunto ainda tem bastante espaço para muitas mais bases de dados. Em situações reais, os clientes executam atualmente até 500 bases de dados em conjuntos de 200 eDTUs. Para obter mais informações, veja o [tutorial de monitorização do desempenho](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="deleting-the-resources-created-with-this-tutorial"></a>Eliminar os recursos criados com este tutorial

Quando tiver terminado de explorar e trabalhar com a aplicação WTP, navegue para o grupo de recursos da aplicação no portal e elimine-o para parar toda a faturação relacionada com esta implementação. Se tiver utilizado algum dos tutoriais fornecidos, também serão eliminados com a aplicação todos os recursos criados.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]

> * Como implementar a aplicação WTP
> * Mais informações sobre os servidores, os conjuntos e as bases de dados que constituem a aplicação
> * Como os inquilinos são mapeados para os dados com o *catálogo*
> * Como aprovisionar inquilinos novos
> * Como ver a utilização do conjunto para monitorizar a atividade dos inquilinos
> * Como eliminar os recursos de exemplo para parar a faturação relacionada

Agora experimente o [Tutorial de aprovisionamento e catalogação](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados após a implementação inicial da aplicação Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* Para saber mais sobre os conjuntos elásticos, veja [ *O que é um conjunto elástico do SQL do Azure?*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Para saber mais sobre as tarefas elásticas, veja [*Managing scaled-out cloud databases (Gerir bases de dados de escalamento horizontal na cloud)*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Para saber mais sobre as aplicações SaaS multi-inquilino, veja [*Design patterns for multi-tenant SaaS applications (Padrões de conceção para aplicações SaaS multi-inquilino)*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

