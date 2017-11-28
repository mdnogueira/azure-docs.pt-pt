---
title: "Executar consultas ad hoc de relatórios em várias bases de dados SQL do Azure | Microsoft Docs"
description: "Execute consultas ad hoc de relatórios em várias bases de dados do SQL Server um exemplo de aplicação multi-inquilino."
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: ddad47ccac57ddbb9387709ababbc5be6bad3462
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Executar consultas de análises ad hoc através de várias bases de dados SQL do Azure

Neste tutorial, executar consultas distribuídas entre o conjunto completo de inquilino bases de dados para ativar o relatório interativo ad hoc. Estas consultas podem extrair insights buried dos dados operacionais quotidianas da aplicação SaaS de bilhetes Wingtip. Para tal, implementar uma base de dados de análise adicional para o servidor de catálogo e utilize elástico consulta para ativar as consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]

> * Sobre as vistas globais em cada base de dados, estas vistas ativar consultar eficiente entre inquilinos
> * Como implementar uma base de dados de relatórios ad hoc
> * Como executar consultas distribuídas em todas as bases de dados do inquilino



Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:


* A aplicação de Wingtip bilhetes SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) está instalado. Para transferir e instalar o SSMS, veja [transferir SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Padrão de relatórios ad hoc

![padrão de relatórios ad hoc](media/saas-tenancy-adhoc-analytics/adhocreportingpattern_dbpertenant.png)

Um das oportunidades excelente com aplicações de SaaS é a capacidade de utilizar a grande quantidade de dados do inquilino centralmente armazenados na nuvem para obter informações acerca da operação e a utilização da sua aplicação. Estas informações podem ajudá desenvolvimento da funcionalidade, melhoramentos de Facilidade de utilização e outros investimentos nas suas aplicações e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é utilizar [consulta elástico](sql-database-elastic-query-overview.md), que lhe permite consultar através de um conjunto distribuído de bases de dados com o esquema comum. Estas bases de dados podem ser distribuídos por subscrições e grupos de recursos diferente, mas precisam de partilhar um início de sessão comuns. Consulta elástica utiliza um único *head* base de dados em que são definidas as tabelas externas que espelha tabelas ou vistas nas bases de dados distribuída (inquilino). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. Consulta elástica utiliza o mapa de partições horizontais na base de dados de catálogo para determinar a localização de todas as bases de dados do inquilino. A configuração e a consulta são simples através de padrão [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)e suporta consultas ad hoc de ferramentas como o Excel e o Power BI.

Por distribuição consultas entre as bases de dados do inquilino, consulta elástico fornece informações imediatas sobre os dados de produção em direto. No entanto, como elástico consulta obtém dados a partir potencialmente muitas bases de dados, latência de consulta pode, por vezes, ser ou superior para consultas equivalentes submetidas a uma única base de dados do multi-inquilino. Lembre-se de que a estrutura de consultas para minimizar os dados que são devolvidos. Consulta elástica frequentemente melhor é adequada para pequenas quantidades de dados em tempo real, por oposição a criação utilizada frequentemente ou consultas de análises complexas ou relatórios a consultar. Se não efetuar a consultas bem, observe o [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta ter sido feito o push para baixo para a base de dados remota e a quantidade de dados está a ser devolvido. As consultas que requerem processamento analítico complexo poderá ser melhor servido em alguns casos, a extrair dados do inquilino para uma base de dados dedicado ou do armazém de dados otimizadas para consultas de análise. Este padrão é explicado no [tutorial de análise do inquilino](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicação Wingtip bilhetes SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de permissão

Para executar consultas contra um conjunto de dados mais interessante, crie dados de vendas da permissão, executando o gerador de permissão.

1. No *ISE do PowerShell*, abra a... \\Learning módulos\\análise operacional\\Adhoc Reporting\\*demonstração AdhocReporting.ps1* criar scripts e defina os seguintes valores:
   * **$DemoScenario** = 1, **comprar permissões para eventos em todos os venues**.
2. Prima **F5** para executar o script e gerar vendas de permissão. Enquanto estiver a executar o script, continue os passos neste tutorial. Os dados de pedido de suporte são consultados no *executar consultas ad-hoc distribuída* secção, por isso, aguarde que o gerador de permissão concluir.

## <a name="explore-the-global-views"></a>Explore as vistas globais

Na aplicação Wingtip bilhetes SaaS da base de dados por inquilino, cada inquilino é atribuído uma base de dados. Assim, os dados contidos nas tabelas de base de dados tem um âmbito a perspetiva de um único inquilino. No entanto, quando consultar em todas as bases de dados, é importante que a consulta elástico pode tratar os dados, como se faz parte de uma única base de dados lógica em partição horizontal por inquilino. 

Para simular este padrão, um conjunto de vistas 'globais' são adicionados à base de dados do inquilino desse projeto um ID de inquilino em cada uma das tabelas que são consultadas global. Por exemplo, o *VenueEvents* vista adiciona um calculada *VenueId* para as colunas projetadas do *eventos* tabela. Da mesma forma, o *VenueTicketPurchases* e *VenueTickets* vistas adicionar um calculada *VenueId* coluna projetado das respetivas tabelas. Estas vistas são utilizadas pela consulta elástico para parallelize consultas e push-los para o inquilino remoto adequado da base de dados quando um *VenueId* coluna está presente. Isto reduz significativamente a quantidade de dados que são devolvidos e resulta num aumento significativo no desempenho para várias consultas. Estas vistas globais tem sido previamente criadas em todas as bases de dados do inquilino.

1. Abra o SSMS e [ligar ao tenants1 -&lt;utilizador&gt; servidor](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Expanda **bases de dados**, faça duplo clique **contosoconcerthall**e selecione **nova consulta**.
3. Execute as seguintes consultas para explorar a diferença entre as tabelas de inquilino único e as vistas globais:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Destas vistas, o *VenueId* é calculada como um valor hash do nome de Venue, mas qualquer abordagem poderia ser utilizado para introduzir um valor exclusivo. Esta abordagem é semelhante para a forma como a chave de inquilino é calculada para utilização no catálogo.

Para examinar a definição do *Venues* vista:

1. No **Object Explorer**, expanda **contosoconcerthall** > **vistas**:

   ![vistas](media/saas-tenancy-adhoc-analytics/views.png)

2. Clique com botão direito **dbo. Venues**.
3. Selecione **Script vista como** > **criar para** > **nova janela do Editor de consultas**

Script de qualquer um dos outros *Venue* vistas para ver como adicionar o *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implementar a base de dados utilizado para consultas distribuídas ad hoc

Neste exercício implementa o *adhocreporting* base de dados. Esta é a base de dados principal que contém o esquema utilizado para consultas em todas as bases de dados do inquilino. A base de dados é implementado o servidor de catálogo existente, o que é o servidor utilizado para todas as bases de dados relacionadas com a gestão na aplicação de exemplo.

1. Abra... \\Learning módulos\\análise operacional\\Adhoc Reporting\\*demonstração AdhocReporting.ps1* no *ISE do PowerShell* e defina o seguintes valores:
   * **$DemoScenario** = 2, **Implementar a base de dados Analytics Ad-hoc**.

2. Prima **F5** para executar o script e criar o *adhocreporting* base de dados.

Na secção seguinte, adicione esquema para a base de dados para que possam ser utilizado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar a base de dados 'head' para a execução de consultas distribuídas

Neste exercício adiciona esquema (a origem de dados externa e definições de tabela externa) na base de dados de análise ad hoc que lhe permite consultar em todas as bases de dados do inquilino.

1. Abra o SQL Server Management Studio e ligue à base de dados de relatórios ad hoc que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Abrir ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *inicializar AdhocReportingDB.sql* no SSMS.
3. Reveja o script SQL e tenha em atenção:

   Consulta elástica utiliza uma credencial com âmbito de base de dados para aceder a cada uma das bases de dados do inquilino. Esta credencial tem de estar disponível em todas as bases de dados e devem normalmente concedidos direitos mínimos necessários para ativar estas consultas ad hoc.

    ![Criar credencial](media/saas-tenancy-adhoc-analytics/create-credential.png)

   Ao utilizar a base de dados de catálogo como a origem de dados externos, consultas são distribuídas para todas as bases de dados registadas no catálogo, quando a consulta é executada. Porque os nomes dos servidores são diferentes para cada implementação, este script de inicialização obtém a localização da base de dados do catálogo por obter o servidor atual (@@servername) onde o script é executado.

    ![Criar origem de dados externa](media/saas-tenancy-adhoc-analytics/create-external-data-source.png)

   As tabelas externas que façam referência as vistas globais descrito na secção anterior e definido com **distribuição = SHARDED(VenueId)**. Porque cada *VenueId* mapeado para uma base de dados individual, Isto melhora o desempenho de muitos cenários, como mostrado na secção seguinte.

    ![criar tabelas externas](media/saas-tenancy-adhoc-analytics/external-tables.png)

   A tabela local *VenueTypes* que é criado e preenchido. Esta tabela de dados de referência é comum em todas as bases de dados de inquilino, pelo que pode ser representado aqui como uma tabela local e preenchidos com os dados comuns. Para algumas consultas, isto pode reduzir a quantidade de dados movidos entre as bases de dados do inquilino e a *adhocreporting* base de dados.

    ![Criar tabela](media/saas-tenancy-adhoc-analytics/create-table.png)

   Se incluir as tabelas de referência desta forma, não se esqueça de atualizar o esquema da tabela e os dados sempre que atualizar as bases de dados do inquilino.

4. Prima **F5** para executar o script e inicializar o *adhocreporting* base de dados. 

Agora pode executar consultas distribuídas e recolher informações em todos os inquilinos!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas distribuídas ad hoc

Agora que o *adhocreporting* base de dados está a configurar, avançar e executar algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão sobre onde está a acontecer o processamento da consulta. 

Quando inspecionar o plano de execução, coloque o cursor sobre os ícones de plano para obter mais detalhes. 

Importante a ter em atenção, é essa definição **distribuição = SHARDED(VenueId)** quando a origem de dados externo é definida melhora o desempenho de muitos cenários. Como cada *VenueId* mapeado para uma base de dados individual, filtragem é facilmente efetuada remotamente, devolver apenas os dados necessários.

1. Abra... \\Learning módulos\\análise operacional\\Adhoc Reporting\\*demonstração AdhocReportingQueries.sql* no SSMS.
2. Certifique-se de que está ligado ao **adhocreporting** base de dados.
3. Selecione o **consulta** menu e **incluem real planear de execução**
4. Realce o *que venues estão atualmente registados?* consulta e prima **F5**.

   A consulta devolve a lista de venue completo, que ilustra como rápido, e é fácil consultar em todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e vê que o custo total de consulta remota porque cada base de dados do inquilino processa a sua própria consulta e devolve as informações de venue.

   ![SELECIONAR * de dbo. Venues](media/saas-tenancy-adhoc-analytics/query1-plan.png)

5. Selecione o seguinte consulta e prima **F5**.

   Esta consulta associa a dados a partir do local e as bases de dados do inquilino *VenueTypes* tabela (local, dado que é uma tabela *adhocreporting* base de dados).

   Inspecione o plano e vê que a maioria dos custos de consulta remota. Cada base de dados do inquilino devolve as respetivas informações de venue e efetua uma associação local com o local *VenueTypes* tabela para apresentar o nome amigável.

   ![Associação de dados remota e local](media/saas-tenancy-adhoc-analytics/query2-plan.png)

6. Selecione agora o *no qual o dia foram mais pedidos de suporte de artigos vendidos?* consulta e prima **F5**.

   Esta consulta funciona associar um pouco mais complexas e a agregação. O que é importante ter em atenção é que a maior parte do processamento seja efetuada remotamente e novamente devolve apenas as linhas for necessário, uma única linha para permissão agregado venda da contagem cada venue por dia.

   ![consulta](media/saas-tenancy-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implementar uma base de dados de relatórios ad hoc e adicionar-lhe para executar consultas distribuídas esquema.


Experimente agora o [tutorial de análise de inquilino](saas-tenancy-tenant-analytics.md) para explorar extrair dados para uma base de dados de análise separada para o processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais tirar partido da aplicação Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta Elástica](sql-database-elastic-query-overview.md)
