---
title: "Executar consultas de análise em vários inquilinos (aplicação SaaS de exemplo com a Base de Dados SQL do Azure) | Microsoft Docs"
description: "Executar consultas de análise em vários inquilinos"
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
ms.openlocfilehash: b512e2f7833be1947ef7674d6e0266879789ac5a
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="run-analytics-queries-against-multiple-tenants"></a>Executar consultas de análise em vários inquilinos

Neste tutorial, vai executar consultas de análise em cada inquilino no catálogo. É criada uma tarefa elástica que executa as consultas. A tarefa obtém os dados e carrega-os numa base de dados de análise separada, criada no servidor de catálogo. Esta base de dados pode ser consultada para extrair as informações que estão escondidas nos dados operacionais diários de todos os inquilinos. Como resultado da tarefa, é criada uma tabela a partir das consultas dos resultados dentro da base de dados de análise de inquilinos.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar a base de dados de análise de inquilinos
> * Criar uma tarefa agendada para obter os dados e preencher a base de dados de análise

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação WTP está implementada. Para implementar em menos de cinco minutos, veja [Implementar e explorar a aplicação SaaS WTP](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Padrão de Análise Operacional de Inquilinos

Uma das grandes possibilidades das aplicações SaaS consiste em utilizar os dados de inquilinos avançados que estão armazenados na cloud. Utilize estes dados para obter informações sobre a operação e a utilização da aplicação e dos inquilinos. Estes dados podem ajudá-lo a desenvolver funcionalidades, a melhorar a usabilidade e a realizar outros investimentos na aplicação e na plataforma. O acesso a estes dados é fácil quando existir uma única base de dados multi-inquilino, mas não será tão fácil quando existir uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem para aceder a estes dados consiste em utilizar as Tarefas elásticas, que permitem consultas com devolução de resultados na execução da tarefa a ser capturada numa tabela e base de dados de exportação.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts da Wingtip Tickets e o código fonte da aplicação estão disponíveis no repositório do github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Implementar uma base de dados dos resultados da análise de inquilinos

Este tutorial requer que tenha uma base de dados implementada para capturar os resultados da execução de scripts da tarefa, que contém as consultas com resultados. Vamos criar uma base de dados chamada tenantanalytics para esta finalidade.

1. Abra ...\\Módulos de Aprendizagem\\Análise Operacional\\Análise de Inquilinos \\*Demo-TenantAnalyticsDB.ps1* no *ISE do PowerShell* e defina o valor seguinte:
   * **$DemoScenario** = **2** *Implementar a base de dados da análise operacional*
1. Prima **F5** para executar o script de demonstração (que chama o script *Deploy-TenantAnalyticsDB.ps1*) que cria a base de dados de análise de inquilinos.

## <a name="create-some-data-for-the-demo"></a>Criar alguns dados para a demonstração

1. Abra ...\\Módulos de Aprendizagem\\Análise Operacional\\Análise de Inquilinos \\*Demo-TenantAnalyticsDB.ps1* no *ISE do PowerShell* e defina o valor seguinte:
   * **$DemoScenario** = **1** *Comprar bilhetes para eventos em todos os locais*
1. Prima **F5** para executar o script e criar o histórico de compra de bilhetes.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Criar uma tarefa agendada para obter a análise sobre as compras de bilhetes dos inquilinos

Este script cria uma tarefa para obter informações de compra de bilhetes de todos os inquilinos. Depois de agregados numa única tabela, pode obter métricas avançadas e esclarecedoras relativas aos padrões de compra de bilhetes de todos inquilinos.

1. Abra o SSMS e ligue-se ao servidor catalog-\<utilizador\>.database.windows.net
1. Abra ...\\Módulos de Aprendizagem\\Análise Operacional\\Análise de Inquilinos\\*TicketPurchasesfromAllTenants.sql*
1. Modifique \<WtpUser\>, utilize o nome de utilizador que usou quando implementou a aplicação WTP na parte superior do script, **sp\_add\_target\_group\_member** e **sp\_add\_jobstep**
1. Clique com o botão direito do rato, selecione **Ligação** e ligue-se ao servidor catalog-\<WtpUser\>.database.windows.net, se ainda não estiver ligado
1. Verifique se está ligado à base de dados **jobaccount** e prima **F5** para executar o script

* **sp\_add\_target\_group** cria o nome do grupo *TenantGroup* de destino. Agora, precisamos adicionar membros de destino.
* **sp\_add\_target\_group\_member** adiciona um tipo de membro de destino de *servidor*, que considera todas as bases de dados dentro desse servidor (tenha em atenção que se trata do servidor customer1-&lt;WtpUser&gt; que contém as bases de dados dos inquilinos) no momento em que a execução da tarefa deve ser incluída na tarefa.
* **sp\_add\_job** cria uma nova tarefa semanal agendada denominada “Compras de Bilhetes de Todos os Inquilinos”
* **sp\_add\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para obter todas as informações das compras de bilhetes de todos os inquilinos e para copiar o resultado das devolução definido numa tabela denominada *AllTicketsPurchasesfromAllTenants*
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Consulte o valor de estado na coluna **ciclo de vida** para monitorizar o estado. A tarefa concluída com êxito significa que foi concluída com êxito em todas as bases de dados de inquilinos e nas duas bases de dados adicionais que contêm a tabela de referência.

A execução com êxito do script deve resultar em resultados semelhantes:

![resultados](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Criar uma tarefa para obter uma contagem resumida das compras de bilhetes de todos os inquilinos

Este script cria uma tarefa para obter a soma de todas compras de bilhetes de todos os inquilinos.

1. Abra o SSMS e ligue -se ao servidor *catalog-&lt;Utilizador&gt;.database.windows.net*
1. Abra o ficheiro ...\\Módulos de Aprendizagem \\Aprovisionar e Catalogar\\Análise Operacional\\Análise de Inquilinos\\*Results-TicketPurchasesfromAllTenants.sql*
1. Modifique &lt;WtpUser&gt;, utilize o nome de utilizador que usou quando implementou a aplicação WTP no script, no procedimento **sp\_add\_jobstep** armazenado
1. Clique com o botão direito do rato, selecione **Ligação** e ligue-se ao servidor catalog-\<WtpUser\>.database.windows.net, se ainda não estiver ligado
1. Verifique se está ligado à base de dados **tenantanalytics** e prima **F5** para executar o script

A execução com êxito do script deve resultar em resultados semelhantes:

![resultados](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** cria uma nova tarefa agendada semanal denominada “ResultsTicketsOrders”

* **sp\_add\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para obter todas as informações das compras de bilhetes de todos os inquilinos e para copiar o resultado das devoluções definido numa tabela denominada CountofTicketOrders

* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Consulte o valor de estado na coluna **ciclo de vida** para monitorizar o estado. A tarefa concluída com êxito significa que foi concluída com êxito em todas as bases de dados de inquilinos e nas duas bases de dados adicionais que contêm a tabela de referência.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implementar a base de dados de análise de inquilinos
> * Criar uma tarefa agendada para obter dados analíticos de todos os inquilinos

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados após a implementação inicial da aplicação Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Tarefas elásticas](sql-database-elastic-jobs-overview.md)
