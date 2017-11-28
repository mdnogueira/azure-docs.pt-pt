---
title: "Executar consultas de análise em bases de dados | Microsoft Docs"
description: "Consultas de análises de inquilino entre utilizando os dados extraídos de várias bases de dados do SQL Database do Azure."
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh; billgib; genemi
ms.openlocfilehash: fb4311f28f55cfeb3f07a441adde18ae95f39e90
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Análise de inquilino entre utilizar dados extraídos

Neste tutorial, percorrer um cenário de análise completa. O cenário demonstra como análise pode ativar a empresas tomar decisões inteligentes. Utilizar dados extraídos a partir de cada base de dados do inquilino, utilize análise para obter informações acerca comportamento de inquilino, incluindo a utilização da aplicação exemplo Wingtip bilhetes SaaS. Este cenário envolve três passos: 

1.  **Extrair dados** de cada base de dados do inquilino para um arquivo de análise.
2.  **Otimizar os dados extraídos** para processamento de análise.
3.  Utilize **Business Intelligence** ferramentas para desenhar horizontalmente as informações úteis, podem guia faz de decisão. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Crie o inquilino análise armazenar para extrair dados no.
> - Utilize as tarefas elásticas para extrair dados de cada base de dados do inquilino para o arquivo de análise.
> - Otimize os dados extraídos (reorganize para um esquema de estrela).
> - Consulte a base de dados de análise.
> - Utilize o Power BI para visualização de dados para realçar as tendências em dados de inquilino e efetuar a recomendação para melhoramentos.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Padrão de análise offline de inquilino

Aplicações de SaaS que desenvolver têm acesso a uma grande quantidade de dados do inquilino armazenados na nuvem. Os dados fornece uma origem avançada das informações sobre a operação e a utilização da sua aplicação e sobre o comportamento dos inquilinos. Estas informações podem ajudá desenvolvimento da funcionalidade, melhoramentos de Facilidade de utilização e outros investimentos na aplicação e plataforma.

Acesso aos dados para todos os inquilinos é simple quando todos os dados apenas uma base de dados do multi-inquilino. Mas o acesso é mais complexo quando distribuídos à escala milhares de bases de dados. É uma forma de tame a complexidade para extrair os dados para uma base de dados de análise ou um armazém de dados. Em seguida, consultar o arquivo de análise para recolher informações dos dados de pedidos de todos os inquilinos.

Este tutorial apresenta um cenário de análise completa para esta aplicação SaaS. Primeiro, elásticas tarefas são utilizadas para agendar a extração de dados de cada base de dados do inquilino. Os dados são enviados para um arquivo de análise. O arquivo de análise pode ser uma base de dados do SQL Server ou um SQL Data Warehouse. Para extração de dados em grande escala, [do Azure Data Factory](../data-factory/introduction.md) é commended.

Em seguida, os dados agregados é shredded para um conjunto de [esquemas de estrela](https://www.wikipedia.org/wiki/Star_schema) tabelas. As tabelas consistem de uma tabela de factos central plus as tabelas de dimensões relacionados:

- A tabela de factos central no esquema de estrela contém dados de pedido de suporte.
- As tabelas de dimensão contém dados sobre venues, eventos, os clientes e as datas de compra.

Em conjunto os central e tabelas ativar eficiente analíticos processamento de dimensão. O esquema de estrela utilizado neste tutorial, é apresentado na imagem seguinte:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Por fim, as tabelas de esquema de estrela são consultadas. Os resultados da consulta são apresentados visualmente para realçar as informações sobre o comportamento de inquilino e a sua utilização da aplicação. Com este esquema de estrela, pode executar consultas que ajudam a detetar os itens como o seguinte:

- Quem é comprar permissões e a partir da qual venue.
- Ocultos padrões e tendências nas seguintes áreas:
    - Vendas de pedidos de suporte.
    - A popularidade relativa de cada venue.

Compreender como consistentemente cada inquilino está a utilizar o serviço fornece uma oportunidade de criar planos de serviço para se adaptar a suas necessidades. Este tutorial fornece exemplos básicos das informações que podem ser obtidas a partir dos dados de inquilino.

## <a name="setup"></a>Configurar

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- A aplicação de Wingtip bilhetes SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md)
- Os scripts de Wingtip bilhetes SaaS da base de dados por inquilino e a aplicação [código fonte](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) são transferidos a partir do GitHub. Consulte o artigo de instruções de transferência. Certifique-se para *desbloquear o ficheiro zip* antes de a extrair o respetivo conteúdo. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.
- Ambiente de trabalho do Power BI está instalado. [Transferir o ambiente de trabalho do Power BI](https://powerbi.microsoft.com/downloads/)
- O lote de inquilinos adicionais tiver sido aprovisionado, consulte o [ **aprovisionar inquilinos tutorial**](saas-dbpertenant-provision-and-catalog.md).
- Uma conta de tarefa e a base de dados de conta de tarefa foram criados. Consulte os passos adequados de [ **tutorial de gestão de esquema**](saas-tenancy-schema-management.md#create-a-job-account-database-and-new-job-account).

### <a name="create-data-for-the-demo"></a>Criar dados de demonstração de

Neste tutorial, análise é executada nos dados de vendas de permissão. O passo atual, gerar dados de pedido de suporte para todos os inquilinos.  Mais tarde são extraídos estes dados para análise. *Certifique-se de que aprovisionou o lote de inquilinos, tal como descrito anteriormente, para que tenha uma quantidade significativa de dados*. Uma suficientemente grande quantidade de dados pode expor um intervalo de permissão diferentes padrões de compra.

1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*e defina o valor seguinte:
    - **$DemoScenario** = **1** para eventos em venues todos os pedidos de suporte de compra
2. Prima **F5** para executar o script e criar pedido de compra do histórico para cada evento em cada venue.  O script é executado durante vários minutos gerar dezenas de milhares de pedidos de suporte.

### <a name="deploy-the-analytics-store"></a>Implementar o arquivo de análise
Muitas vezes, existem várias bases de dados transacionais que em conjunto contêm todos os dados de inquilino. Tem agregar os dados de inquilino de muitas bases de dados transacionais para arquivo de uma análise. A agregação permite consulta eficiente dos dados. Neste tutorial, uma base de dados do SQL Database do Azure é utilizado para armazenar os dados agregados.

Nos passos seguintes, implementar o arquivo de análise, o que é chamado **tenantanalytics**. Também implementar tabelas predefinidas que são povoadas mais tarde no tutorial:
1. No ISE do PowerShell, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Defina a variável de $DemoScenario no script para corresponder à sua escolha de arquivo de análise:
    - Para utilizar a base de dados SQL sem o arquivo de colunas, defina **$DemoScenario** = **2**
    - Para utilizar a base de dados SQL com o arquivo de colunas, defina **$DemoScenario** = **3**  
3. Prima **F5** para executar o script de demonstração (que chama o *implementar TenantAnalytics<XX>. ps1* script) que cria o arquivo de análise de inquilino. 

Agora que implementou a aplicação e preenchido este com dados interessantes do inquilino, utilize [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ligar **tenants1-dpt -&lt;utilizador&gt;**  e **catálogo-dpt -&lt;utilizador&gt;**  servidores utilizando o início de sessão = *programador*, palavra-passe =  *P@ssword1* . Consulte o [tutorial introdutórias](saas-dbpertenant-wingtip-app-overview.md) para obter mais orientações.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

No Object Explorer, execute os seguintes passos:

1. Expanda o *tenants1-dpt -&lt;utilizador&gt;*  servidor.
2. Expanda o nó de bases de dados e ver a lista de bases de dados do inquilino.
3. Expanda o *catálogo-dpt -&lt;utilizador&gt;*  servidor.
4. Certifique-se de que vê o arquivo de análise e a base de dados jobaccount.

Consulte os seguintes itens de base de dados no Explorador de objeto do SSMS, expandindo o nó de arquivo de análise:

- As tabelas **TicketsRawData** e **EventsRawData** conter dados não processados de extraídos das bases de dados do inquilino.
- As tabelas de esquema de estrela são **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, e **dim_Dates** .
- O procedimento armazenado é utilizado para preencher as tabelas de esquema de estrela das tabelas de dados não processados.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extração de dados 

### <a name="create-target-groups"></a>Criar grupos de destino 

Antes de continuar, certifique-se de que implementou a base de dados de conta e jobaccount tarefa. O seguinte conjunto de passos, as tarefas elásticas é utilizada para extrair dados de cada base de dados do inquilino e para armazenar os dados no arquivo de análise. Em seguida, a segunda tarefa shreds os dados e armazena-os em tabelas do esquema de estrela. Estas duas tarefas executar dois grupos de destino diferente, nomeadamente **TenantGroup** e **AnalyticsGroup**. A tarefa de extração é executada relativamente a TenantGroup, que contém todas as bases de dados do inquilino. A tarefa shredding é executada relativamente a AnalyticsGroup, que contém apenas o arquivo de análise. Crie os grupos de destino utilizando os seguintes passos:

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-dpt -&lt;utilizador&gt;.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Modificar o @User variável na parte superior do script, substituindo <User> com o valor de utilizador utilizado quando implementou a aplicação Wingtip SaaS.
4. Prima **F5** para executar o script que cria os grupos de destino de dois.

### <a name="extract-raw-data-from-all-tenants"></a>Extrair dados não processados de todos os inquilinos

As modificações de um vasto conjunto de dados poderão ocorrer com mais frequência para *permissão e cliente* dados que para *eventos e venue* dados. Por conseguinte, considere extrair dados de pedido de suporte e cliente em separado e mais frequentemente do que extrair dados de eventos e venue. Nesta secção, defina e agendar duas tarefas separadas:

- Extrair dados de pedido de suporte e de cliente.
- Extrair dados de eventos e venue.

Cada tarefa extrai os respetivos dados e envia-o para o arquivo de análise. Não existe uma tarefa separada shreds os dados extraídos para o esquema de estrela de análise.

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-dpt -&lt;utilizador&gt; servidor.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modificar @User na parte superior do script e substituir <User> com o nome de utilizador utilizado quando implementou a aplicação Wingtip SaaS 
4. Prima F5 para executar o script que cria e executa a tarefa que extrai dados de pedidos de suporte e os clientes de cada base de dados do inquilino. A tarefa guarda os dados para o arquivo de análise.
5. Consulta a tabela de TicketsRawData na base de dados tenantanalytics, para se certificar de que a tabela é preenchida com as informações de pedidos de todos os inquilinos.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Repita os passos anteriores, exceto substituir este tempo **\ExtractTickets.sql** com **\ExtractVenuesEvents.sql** no passo 2.

Executar com êxito a tarefa preenche a tabela de EventsRawData no arquivo de análise com novos eventos e venues as informações de todos os inquilinos. 

## <a name="data-reorganization"></a>Reorganização de dados

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Dados extraídos shred para povoar tabelas de esquema de estrela

O passo seguinte é shred os dados não processados extraídos para um conjunto de tabelas que estão otimizados para consultas de análise. Um esquema de estrela é utilizado. Uma tabela de factos central contém permissão individuais registos de vendas. Outras tabelas são preenchidas com dados relacionados sobre venues, eventos e os clientes. E não existirem as tabelas de dimensões de hora. 

Nesta secção do tutorial, tem de define e executar uma tarefa que une os dados não processados extraídos com os dados nas tabelas de esquema de estrela. Uma vez concluída a tarefa de intercalação, são eliminados os dados não processados, deixando as tabelas prontas para ser preenchido pelos dados de inquilino seguintes extrair a tarefa.

1. No SSMS, ligue para o **jobaccount** base de dados no catálogo-dpt -&lt;utilizador&gt;.
2. No SSMS, abra *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Prima **F5** executar o script para definir uma tarefa que chama o sp_ShredRawExtractedData do procedimento armazenado no arquivo de análise.
4. Permita tempo suficiente para a tarefa seja executada com êxito.
    - Verifique o **ciclo de vida** coluna da tabela de jobs.jobs_execution para o estado da tarefa. Certifique-se de que a tarefa **com êxito** antes de continuar. Uma execução bem sucedida apresenta dados semelhantes para o gráfico seguinte:

![shredding](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Exploração de dados

### <a name="visualize-tenant-data"></a>Visualizar dados de inquilino

Os dados na tabela de esquema de estrela fornecem todos os permissão vendas dados necessários para a sua análise. Para tornar mais fácil ver as tendências de grandes conjuntos de dados, terá de visualizar-graficamente.  Nesta secção, irá aprender a utilizar **Power BI** para manipular e visualizar os dados de inquilino tiver extraído e organizados.

Utilize os seguintes passos para ligar ao Power BI e, para importar as vistas que criou anteriormente:

1. Inicie o ambiente de trabalho do Power BI.
2. A partir do Friso inicial, selecione **obter dados**e selecione **mais...** no menu.
3. No **obter dados** janela, selecione SQL Database do Azure.
4. Na janela de início de sessão da base de dados, introduza o nome do servidor (catálogo-dpt -&lt;utilizador&gt;. database.windows.net). Selecione **importação** para **modo de conectividade de dados**e, em seguida, clique em OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecione **base de dados** no painel esquerdo, em seguida, introduza o nome de utilizador = *programador*e introduza a palavra-passe =  *P@ssword1* . Clique em **Ligar**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. No **navegador** painel, sob a base de dados de análise, selecione as tabelas de esquema de estrela: fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates. Em seguida, selecione **carga**. 

Parabéns! Carregou com êxito os dados no Power BI. Agora pode começar a explorar visualizações interessantes para o ajudar a obter informações sobre os seus inquilinos. Em seguida guiá como análise pode ativar a fornecer recomendações condicionada por dados para a equipa de negócio Wingtip pedidos de suporte. As recomendações podem ajudar a otimizar a experiência de cliente e modelo de negócio.

Que comece por analisar os dados de vendas permissão para ver a variação na utilização em toda as venues. Selecione as seguintes opções no Power BI para desenhar um gráfico de barras do número total de pedidos de suporte de artigos vendidos por cada venue. Devido a variação aleatória no gerador de permissão, os resultados podem ser diferentes.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

O desenho anterior confirma que o número de pedidos de suporte de artigos vendidos por cada venue varia. Venues propor mais pedidos estão a utilizar o serviço mais fortemente venues propor menos permissões. Pode haver uma oportunidade para personalizar a alocação de recursos de acordo com as necessidades de inquilino diferente.

Ainda pode analisar os dados para ver como vendas permissão variam ao longo do tempo. Selecione as seguintes opções no Power BI para desenhar o número total de pedidos de suporte de artigos vendidos por dia durante um período de 60 dias.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

O gráfico anterior mostra esse pico de pedidos vendas do pedido de suporte para alguns venues. Estes picos de impor a ideia que alguns venues poderão estar a consumir recursos de sistema disproportionately. Até ao momento não é não existe nenhuma padrão óbvios no quando ocorrem os picos de.

Em seguida que pretende continuar a investigar a significância destes dias de venda das horas de ponta. Quando estas picos ocorrer depois de pedidos de suporte, aceda à venda? Para desenhar bilhetes vendidos por dia, selecione as seguintes opções no Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

O desenho anterior mostra que alguns venues propor uma grande quantidade de pedidos de suporte no primeiro dia de venda. Assim que as permissões aceda à venda nestes venues, parece haver um rush mad. Este rajada de atividade por alguns venues poderá afetar o serviço para os outros inquilinos.

Pode explorar os dados novamente para ver se este rush mad é verdadeiro para todos os eventos hospedados estes venues. No rastreia anterior, observados que Contoso Concert Hall sells muitos pedidos e que Contoso também tem um pico de pedidos na vendas de permissão em determinados dias. Reproduzir em torno com opções do Power BI para desenhar vendas permissão cumulativa para Contoso Concert Hall, concentrar-se em tendências de venda para cada um dos seus eventos. Todos os eventos siga o mesmo padrão de venda?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

O desenho anterior para Contoso Concert Hall mostra que o rush mad não acontecer para todos os eventos. Reproduzir em torno com as opções de filtro para ver as tendências de venda para outros venues.

As informações para vender padrões de pedido de suporte poderão originar Wingtip permissões para otimizar o seu modelo de negócio. Em vez de charging todos os inquilinos igualmente, talvez Wingtip deve dar origem a escalões de serviço com níveis de desempenho diferentes. Venues maior do que necessita para vender mais pedidos por dia foi oferecidas um escalão superior com um superior contrato de nível serviço (SLA). Esses venues podem ter as bases de dados colocados em conjunto com limites de recursos por base de dados superiores. Cada escalão de serviço pode ter uma alocação de venda por hora, com taxas adicionais cobradas exceder a alocação. Venues maiores que tenham bursts periódicas de vendas seriam beneficiar os escalões superiores e Wingtip permissões pode monetizar seu serviço de forma mais eficiente.

Entretanto, alguns clientes de bilhetes Wingtip queixarem de que estes dificuldade vender suficiente permissões para indicar o custo do serviço. Talvez nestas informações não há uma oportunidade para melhorar vendas de permissão para underperforming venues. Vendas superiores seriam aumentar o valor do serviço percetível. Clique com o botão direito do rato em fact_Tickets e selecione **nova medida**. Introduza a seguinte expressão da medida nova denominada **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecione as seguintes opções de visualização para desenhar os pedidos de suporte de percentagem vendidos por cada venue para determinar os respetivos êxito relativo.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

O desenho anterior mostra que, apesar da maioria das venues propor mais de 80% dos respetivos bilhetes, alguns são struggling preencher mais de metade de utilizadores individuais. Reproduzir em torno com os valores bem para selecionar a percentagem de máxima ou mínima de permissões vendida para cada venue.

Anteriormente deepened a análise para detetar que vendas permissão tendem a seguir padrões previsíveis. Esta deteção pode permitem ajuda Wingtip bilhetes underperforming venues intensificação permissão vendas por Recomendamos preços dinâmico. Esta deteção pode revelar uma oportunidade para utilizar as técnicas de aprendizagem máquina para prever vendas de pedido de suporte para cada evento. Predições também podem ser feitas para o impacto no receitas de oferta descontos vendas de permissão. Power BI Embedded pode ser integrado uma aplicação de gestão de eventos. A integração foi ajudam a visualizar vendas previstas e o efeito de descontos diferentes. A aplicação pode ajudar a devise um desconto a aplicar diretamente a partir do ecrã análise ideal.

Ter observados tendências em dados de inquilino da aplicação WingTip. Pode contemplate outras formas que a aplicação pode informar decisões de negócio para fornecedores de aplicações SaaS. Os fornecedores podem aparência melhor às necessidades dos respetivos inquilinos. Hopefully neste tutorial tem equipados com as ferramentas necessárias para efetuar a análise nos dados de inquilino para capacitar as empresas para tomar decisões condicionada por dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Implementar uma base de dados de análise de inquilino com tabelas de esquema em estrela predefinido
> - Permite que as tarefas elásticas para extrair dados de todos os o inquilino da base de dados
> - Intercale os dados extraídos a tabelas de um esquema de estrela concebidas para análise
> - Consultar uma base de dados de análise 
> - Utilizar o Power BI para visualização de dados para observar as tendências em dados de inquilino 

Parabéns!

## <a name="additional-resources"></a>Recursos adicionais

- Adicionais [tutoriais tirar partido da aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [As tarefas elásticas](sql-database-elastic-jobs-overview.md).
