---
title: "Executar consultas de análise em várias bases de dados SQL do Azure | Microsoft Docs"
description: "Extrair dados de bases de dados do inquilino para uma base de dados de análise para a análise offline"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: billgib; sstein
ms.openlocfilehash: 4a96efb15268c56e3625832b0b4d6dd8f6a78614
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="extract-data-from-tenant-databases-into-an-analytics-database-for-offline-analysis"></a>Extrair dados de bases de dados do inquilino para uma base de dados de análise para a análise offline

Neste tutorial, utilize uma tarefa elástica para executar consultas no cada base de dados do inquilino. A tarefa extrai dados de vendas de permissão e carrega-o para uma base de dados de análise (ou do armazém de dados) para análise. A base de dados de análise, em seguida, está a ser consultado para extrair informações de dados operacionais diárias de todos os inquilinos.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar a base de dados de análise de inquilinos
> * Criar uma tarefa agendada para obter os dados e preencher a base de dados de análise

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip SaaS é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação Wingtip SaaS](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Está instalada a versão mais recente do SQL Server Management Studio (SSMS). [Transferir e instalar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Padrão de Análise Operacional de Inquilinos

Uma das grandes possibilidades das aplicações SaaS consiste em utilizar os dados de inquilinos avançados que estão armazenados na cloud. Utilize estes dados para obter informações sobre a operação e a utilização da aplicação e dos inquilinos. Estes dados podem ajudá-lo a desenvolver funcionalidades, a melhorar a usabilidade e a realizar outros investimentos na aplicação e na plataforma. O acesso a estes dados é fácil quando existir uma única base de dados multi-inquilino, mas não será tão fácil quando existir uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem para aceder a estes dados consiste em utilizar as Tarefas elásticas, que permitem consultas com devolução de resultados na execução da tarefa a ser capturada numa tabela e base de dados de exportação.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts de Wingtip SaaS e o código fonte da aplicação, estão disponíveis no [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) repositório do github. [Passos para transferir os scripts de Wingtip SaaS](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

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

1. Abra o SSMS e ligue-se ao servidor catalog-&lt;utilizador&gt;.database.windows.net
1. Abra ...\\Módulos de Aprendizagem\\Análise Operacional\\Análise de Inquilinos\\*TicketPurchasesfromAllTenants.sql*
1. Modificar &lt;utilizador&gt;, utilize o nome de utilizador utilizado quando implementou a aplicação Wingtip SaaS na parte superior do script, **sp\_adicionar\_destino\_grupo\_membro** e **sp\_adicionar\_passo de tarefa**
1. Clique com o botão direito, selecione **ligação**e ligar ao catálogo -&lt;utilizador&gt;. database.windows.net servidor, se ainda não estiver ligado
1. Verifique se está ligado à base de dados **jobaccount** e prima **F5** para executar o script

* **sp\_add\_target\_group** cria o nome do grupo *TenantGroup* de destino. Agora, precisamos adicionar membros de destino.
* **SP\_adicionar\_destino\_grupo\_membro** adiciona um *servidor* tipo de membro, considere todas as bases de dados dentro desse servidor de destino (tenha em atenção de que este é o customer1 -&lt;utilizador&gt; servidor que contém as bases de dados do inquilino) no momento da tarefa de execução deve ser incluída na tarefa.
* **sp\_add\_job** cria uma nova tarefa semanal agendada denominada “Compras de Bilhetes de Todos os Inquilinos”
* **sp\_add\_jobstep** cria o passo da tarefa que contém o texto do comando T-SQL para obter todas as informações das compras de bilhetes de todos os inquilinos e para copiar o resultado das devolução definido numa tabela denominada *AllTicketsPurchasesfromAllTenants*
* As vistas restantes no script mostram a existência dos objetos e monitorizam a execução da tarefa. Consulte o valor de estado na coluna **ciclo de vida** para monitorizar o estado. A tarefa concluída com êxito significa que foi concluída com êxito em todas as bases de dados de inquilinos e nas duas bases de dados adicionais que contêm a tabela de referência.

A execução com êxito do script deve resultar em resultados semelhantes:

![resultados](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Criar uma tarefa para obter uma contagem resumida das compras de bilhetes de todos os inquilinos

Este script cria uma tarefa para obter a soma de todas compras de bilhetes de todos os inquilinos.

1. Abra o SSMS e ligue -se ao servidor *catalog-&lt;Utilizador&gt;.database.windows.net*
1. Abra o ficheiro ...\\Módulos de Aprendizagem \\Aprovisionar e Catalogar\\Análise Operacional\\Análise de Inquilinos\\*Results-TicketPurchasesfromAllTenants.sql*
1. Modificar &lt;utilizador&gt;, utilize o nome de utilizador utilizado quando implementou a aplicação Wingtip SaaS no script, no **sp\_adicionar\_passo** procedimento armazenado
1. Clique com o botão direito, selecione **ligação**e ligar ao catálogo -&lt;utilizador&gt;. database.windows.net servidor, se ainda não estiver ligado
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

* Adicionais [tutoriais tirar partido da aplicação Wingtip SaaS](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Tarefas elásticas](sql-database-elastic-jobs-overview.md)
