---
title: "Manutenção preditiva no aeroespacial com o Azure - guia técnico do Cortana Intelligence solução | Microsoft Docs"
description: "Um guia técnico para o modelo de solução com o Microsoft Cortana Intelligence para manutenção preventiva aeroespacial, utilitários e transportes."
services: cortana-analytics
documentationcenter: 
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: fboylu
ms.openlocfilehash: a6d9cd05eb370399fc95cc64bae892e8b5a73fe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guia técnico para o modelo de solução do Cortana Intelligence para manutenção preventiva aeroespacial e outras empresas

## <a name="important"></a>**Importante**
Este artigo foi preterido. As informações estão ainda relevantes para o problema em questão, ou seja, manutenção preditiva no aeroespacial, mas é possível encontrar o artigo mais recente com as informações mais atualizadas [aqui](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace). 

## <a name="acknowledgements"></a>**Confirmações**
Este artigo foi criado pelo cientistas de dados Yan Zhang, Gauher Shaheen, Fidan Boylu Uz e engenheiro de software Dan Grecoe na Microsoft.

## <a name="overview"></a>**Descrição geral**
Modelos de solução foram concebidos para acelerar o processo de criação de uma demonstração de E2E por cima do Cortana Intelligence Suite. Um modelo implementado irá aprovisionar a sua subscrição com componentes do Cortana Intelligence necessários e criar relações entre eles. Também implementa o pipeline de dados com dados de exemplo gerados a partir de uma aplicação de gerador de dados que irá transferir e instalar no seu computador local, depois de implementar o modelo de solução. Os dados gerados a partir do gerador de dimensões serão hydrate o pipeline de dados e iniciar gerar predições do machine learning que podem ser visualizadas no dashboard do Power BI. O processo de implementação irá ajudá-lo através de vários passos para configurar as credenciais da sua solução. Certifique-se de que estas credenciais, tais como o nome da solução, nome de utilizador e palavra-passe, que terá de fornecer durante a implementação de registo.  

O objetivo deste documento é explicam a arquitetura de referência e os diferentes componentes aprovisionados na sua subscrição como parte deste modelo de solução. O documento aborda também como substituir os dados de exemplo com dados reais da sua própria para conseguir ver informações e predições a partir dos seus dados. Além disso, o documento aborda as partes do modelo de solução que teria de ser modificada se pretendesse personalizar a solução com os seus próprios dados. Instruções sobre como criar o dashboard do Power BI para este modelo de solução são fornecidas no final.

> [!TIP]
> Pode transferir e imprimir um [versão PDF deste documento](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="big-picture"></a>**Panorama geral**
![Arquitetura de manutenção preditiva](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Quando a solução for implementada, vários serviços do Azure dentro da Cortana Analytics Suite são ativados (*ou seja,* Hub de eventos, o Stream Analytics, o HDInsight, o Factory de dados, Machine Learning, *etc.*). O diagrama de arquitetura acima mostra como a manutenção preditiva para o modelo de solução aeroespacial é construída a partir das ponto-a-ponto, um nível elevado. Conseguirá investigar estes serviços no portal do azure ao clicar nos mesmos no diagrama de modelo de solução criado com a implementação da solução com a exceção do HDInsight como este serviço é aprovisionado a pedido quando as atividades de pipeline relacionados são necessárias para executar e eliminados posteriormente.
Pode transferir um [versão tamanho completo do diagrama](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As secções seguintes descrevem cada peça.

## <a name="data-source-and-ingestion"></a>**Origem de dados e ingestão**
### <a name="synthetic-data-source"></a>Origem de dados sintético
Para este modelo de origem de dados utilizado é gerada a partir de uma aplicação de ambiente de trabalho que irá transferir e executar localmente após a implementação com êxito. Encontrará as instruções para transferir e instalar esta aplicação na barra de propriedades, ao selecionar o primeiro nó chamado gerador de dados de manutenção preditiva no diagrama de modelo de solução. Esta aplicação feeds o [Hub de eventos do Azure](#azure-event-hub) serviço com pontos de dados ou eventos, que serão utilizados no resto do fluxo de solução. Esta origem de dados é composta por ou derivada de publicamente disponíveis dados do [repositório de dados de NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) utilizando o [conjunto de dados de simulação de degradação motor Turbofan](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

A aplicação de geração de eventos será preenchido o Hub de eventos do Azure apenas enquanto está a executar no seu computador.

### <a name="azure-event-hub"></a>Hub de eventos do Azure
O [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é o destinatário da entrada fornecida pela origem de dados sintético descrito acima.

## <a name="data-preparation-and-analysis"></a>**Preparação de dados e análise**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
O [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço é utilizado para fornecer perto de análise em tempo real no fluxo de entrada do [Hub de eventos do Azure](#azure-event-hub) de serviço e publicar os resultados para um [Power BI](https://powerbi.microsoft.com) dashboard, bem como arquivar todos os eventos de entrada não processados para o [Storage do Azure](https://azure.microsoft.com/services/storage/) serviço para processamento posterior pelo [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço.

### <a name="hd-insights-custom-aggregation"></a>Agregação personalizada HD Insights
O serviço de Azure HD Insight é utilizado para executar [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts (orquestradas pelo Azure Data Factory) para fornecer as agregações nos eventos não processados que foram arquivados utilizando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) é utilizado o serviço (orquestradas pelo Azure Data Factory) para fazer predições nos restantes vida útil (RUL) de um motor de aeronave específico fornecido entradas recebidas.

## <a name="data-publishing"></a>**Publicação de dados**
### <a name="azure-sql-database-service"></a>Serviço de base de dados SQL do Azure
O [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) serviço é utilizado para armazenar (gerida pelo Azure Data Factory) serão as predições recebidas pelo serviço do Azure Machine Learning que será consumido no [Power BI](https://powerbi.microsoft.com) dashboard.

## <a name="data-consumption"></a>**Consumo de dados**
### <a name="power-bi"></a>Power BI
O [Power BI](https://powerbi.microsoft.com) serviço é utilizado para mostrar um dashboard que contém as agregações e os alertas que forneceu o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço, bem como predições RUL armazenadas no [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidos utilizando o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) serviço. Para obter instruções sobre como criar o dashboard do Power BI para este modelo de solução, consulte a secção abaixo.

## <a name="how-to-bring-in-your-own-data"></a>**Como colocar os seus próprios dados**
Esta secção descreve como colocar os seus próprios dados para o Azure e que áreas iriam requerer alterações para dados que trouxer para esta arquitetura.

Não é provável que qualquer conjunto de dados que colocar iria corresponder o conjunto de dados utilizado pelo [conjunto de dados de simulação de degradação motor Turbofan](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) utilizado para este modelo de solução. Compreender os seus dados e os requisitos serão fundamental na forma como modificar este modelo para trabalhar com os seus próprios dados. Se esta for a sua exposição primeiro para o serviço Azure Machine Learning, pode obter uma introdução ao mesmo utilizando o exemplo na [como criar a sua primeira experimentação](../studio/create-experiment.md).

As secções seguintes abordar as secções do modelo que irá necessitar de modificações quando é apresentado um novo conjunto de dados.

### <a name="azure-event-hub"></a>Hub de eventos do Azure
O serviço de Hub de eventos do Azure é muito genérico, que podem ser publicados dados para o hub no formato CSV ou JSON. Nenhum processamento especial ocorre no Hub de eventos do Azure, mas é importante que compreenda os dados que sejam fornecidos para a mesma.

Este documento descreve como para a ingestão de dados, mas pode facilmente enviar eventos ou dados para um Hub de eventos do Azure utilizando a API de Hub de eventos.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
O serviço Azure Stream Analytics é utilizado para fornecer perto de análise em tempo real ao ler a partir de fluxos de dados e exportar dados para qualquer número de origens.

Para a manutenção preditiva para o modelo de solução aeroespacial, a consulta do Azure Stream Analytics consiste em quatro subconsultas, cada eventos de consumo do serviço de Hub de eventos do Azure e ter saídas de quatro localizações distintas. Estes saídas é composto por três conjuntos de dados do Power BI e uma localização de armazenamento do Azure.

Pode encontrar a consulta do Azure Stream Analytics por:

* A iniciar sessão no portal do Azure
* Localizar as tarefas do Stream Analytics ![ícone do Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) que foram gerados quando a solução foi implementada (*por exemplo,*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a solução de manutenção preditiva)
* Selecionar
  
  * ***ENTRADAS*** ver a consulta de entrada
  * ***CONSULTA*** para ver a consulta próprio
  * ***PRODUZ*** para ver as saídas diferentes

Podem encontrar informações sobre a construção de consulta do Azure Stream Analytics no [referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, as consultas de saída três conjuntos de dados com informações de análise em tempo real sobre o fluxo de dados de entrada a um dashboard do Power BI que é fornecido como parte deste modelo de solução quase. Porque não há um conhecimento implícito sobre o formato de dados de entrada, estas consultas teria de ser alterados com base no formato de dados.

A consulta na tarefa de Stream Analytics segundo **maintenancesa02asablob** simplesmente produz todas [Hub de eventos](https://azure.microsoft.com/services/event-hubs/) eventos para [Storage do Azure](https://azure.microsoft.com/services/storage/) e, por conseguinte, não requer nenhuma alteração independentemente do formato de dados uma vez que as informações de eventos completo é transmitida em fluxo para o armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço orquestra o movimento e processamento de dados. Da manutenção preditiva para o modelo de solução aeroespacial fábrica de dados é constituída por três [pipelines](../../data-factory/v1/data-factory-create-pipelines.md) que mova e processar os dados utilizando várias tecnologias.  Pode aceder a fábrica de dados, abrindo o nó de fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implementação da solução. Isto leva-o para a fábrica de dados no portal do Azure. Se observar erros nos conjuntos de dados, pode ignorar os conforme forem devido a fábrica de dados que está a ser implementada antes do gerador de dados foi iniciado. Esses erros, não impedem a fábrica de dados de funcionar.

![Erros de conjunto de dados de fábrica de dados](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta secção descreve o necessário [pipelines](../../data-factory/v1/data-factory-create-pipelines.md) e [atividades](../../data-factory/v1/data-factory-create-pipelines.md) contidas no [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Segue-se a vista de diagrama da solução.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois dos pipelines de dados nesta fábrica contenham [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são utilizados para a partição e agregar os dados. Quando indicado, os scripts estarão localizados no [Storage do Azure](https://azure.microsoft.com/services/storage/) conta criada durante a configuração. A localização será: maintenancesascript\\\\script\\\\hive\\ \\ (ou https://[Your solução name].blob.core.windows.net/maintenancesascript).

Semelhante para o [Azure Stream Analytics](#azure-stream-analytics-1) consultas, o [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts têm um conhecimento implícito sobre o formato de dados de entrada, estas consultas teria de ser alterados com base no formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Isto [pipeline](../../data-factory/v1/data-factory-create-pipelines.md) contém numa única atividade - um [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) atividade utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um [ramo de registo](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para os dados de partição colocar em [Storage do Azure](https://azure.microsoft.com/services/storage/) durante o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) tarefa.

O [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa de criação de partições é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Isto [pipeline](../../data-factory/v1/data-factory-create-pipelines.md) contém várias atividades e cujo resultado final é serão as predições classificadas do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação associada a este modelo de solução.

As atividades contidas neste são:

* [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) atividade utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um [ramo de registo](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para efetuar agregações e funcionalidade engenharia necessária para o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação.
  O [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa de criação de partições é ***PrepareMLInput.hql***.
* [Cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados do [HDInsightHive](../../data-factory/v1/data-factory-hive-activity.md) atividade para um único [Storage do Azure](https://azure.microsoft.com/services/storage/) blob que pode ser acesso pelo [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) atividade.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) atividade que chama o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação que resulta nos resultados que está a ser colocados numa única [Storage do Azure](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Isto [pipeline](../../data-factory/v1/data-factory-create-pipelines.md) contém numa única atividade - um [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados do [Azure Machine Learning](#azure-machine-learning) experimentação do ***MLScoringPipeline*** para o [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) que foi aprovisionado como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação utilizado para este modelo de solução fornece a útil vida remanescente (RUL) de um motor de aeronave. A experimentação é específica para o conjunto de dados consumido e, por conseguinte, irá exigir modificação ou específica para os dados que seja colocados em substituição.

Para obter informações sobre como foi criada a experimentação do Azure Machine Learning, consulte [manutenção preditiva: passo 1 de 3, preparação de dados e de engenharia da funcionalidade](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Monitorize o progresso**
 Depois do gerador de dados é iniciado, o pipeline começa a obter hydrated e os diferentes componentes da sua solução iniciar kicking para a seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas de monitorizar o pipeline.

1. Um da tarefa de Stream Analytics escreve os dados de entrada não processados para o blob storage. Se clicar num componente de armazenamento de BLOBs da sua solução a partir do ecrã, com êxito implementadas a solução e, em seguida, clique em abrir no painel direito, é direcionado para o [portal de gestão](https://portal.azure.com/). Uma vez, clique em Blobs. No painel seguinte, irá ver uma lista de contentores. Clique em **maintenancesadata**. No painel seguinte, irá ver o **rawdata** pasta. Para a pasta de rawdata, verá pastas com nomes como hora = 17, hora = 18 etc. Se vir estas pastas, indica que os dados não processados com êxito a ser gerado no seu computador e armazenados no blob storage. Deverá ver ficheiros de csv que devem ter tamanhos finito MB essas pastas.
2. É o último passo do pipeline de escrever dados (por exemplo, predições do machine learning) na base de dados do SQL Server. Poderá ter de aguardar um máximo de três horas para os dados são apresentados na base de dados do SQL Server. Uma forma para monitorizar a quantidade de dados está disponível na base de dados SQL é através de [portal do azure](https://manage.windowsazure.com/). No painel esquerdo localizar bases de dados SQL ![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) e clique no mesmo. Em seguida, localize a base de dados **pmaintenancedb** e clique na mesma. Na página seguinte na parte inferior, clique em GERIR
   
    ![Gerir o ícone](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    Aqui, pode clicar em nova consulta e a consulta para o número de linhas (por exemplo, selecione Count do PMResult). À medida que aumenta a sua base de dados, deve aumentar o número de linhas na tabela.

## <a name="power-bi-dashboard"></a>**Dashboard do Power BI**
### <a name="overview"></a>Descrição geral
Esta secção descreve como configurar o dashboard do Power BI para visualizar os dados em tempo real do Azure Stream Analytics (caminho quente), bem como os resultados de predição de batch do Azure machine learning (caminho amovíveis).

### <a name="setup-cold-path-dashboard"></a>Dashboard de caminho típico de configuração
No pipeline de dados de caminho típico, o objetivo essencial é obter a RUL preditiva (restantes vida útil) de cada motor de aeronave depois de concluir um voo (ciclo). O resultado de predição é atualizado a cada 3 horas para prever os motores de aeronaves terminar um voo durante as últimos 3 horas.

Power BI liga-se para uma base de dados SQL do Azure como origem de dados, onde estão armazenados os resultados de predição. Nota: 1) após a implementação da sua solução, uma predição real irá aparecer na base de dados dentro de 3 horas.
O ficheiro pbix fornecido com a transferência de gerador contém alguns dados de seed para que pode criar o dashboard do Power BI imediato. 2) neste passo, o pré-requisito é transferir e instalar o software gratuito [ambiente de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Os passos seguintes irão ajudá-lo sobre como ligar o ficheiro pbix na base de dados do SQL Server foi aceleradas no momento da implementação de solução que contém os dados (*por exemplo,*. resultados de predição) para visualização.

1. Obter as credenciais da base de dados.
   
   Terá de **base de dados de nome do servidor, nome de base de dados, nome de utilizador e palavra-passe** antes de passar para os passos seguintes. Eis os passos para ajudá-lo como localizá-las.
   
   * Uma vez **'SQL Database do Azure'** no seu modelo de solução diagrama fica verde e, em seguida, clique em **'Open'**.
   * Verá uma novo separador/janela do browser que apresenta a página do portal do Azure. Clique em **'Grupos de recursos'** no painel esquerdo.
   * Selecione a subscrição que está a utilizar para implementar a solução e, em seguida, selecione **' YourSolutionName\_ResourceGroup'**.
   * No pop de nova saída painel, clique em de ![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ícone para aceder à sua base de dados. O nome de base de dados for junto a este ícone (*por exemplo,*, **'pmaintenancedb'**) e o **nome do servidor de base de dados** está listado na propriedade de nome de servidor e deve ter um aspeto semelhante **YourSoutionName.database.windows.net**.
   * A base de dados **username** e **palavra-passe** são o mesmo nome de utilizador e palavra-passe anteriormente registado durante a implementação da solução.
2. Atualize a origem de dados do ficheiro de relatório caminho típico com o Power BI Desktop.
   
   * Na pasta no computador onde transferiu e unzipped o ficheiro de gerador de dimensões, faça duplo clique o **PowerBI\\PredictiveMaintenanceAerospace.pbix** ficheiro. Se vir quaisquer mensagens de aviso quando abrir o ficheiro, ignorá-las. No topo do ficheiro, clique em **'Editar consultas'**.
     
     ![Editar consultas](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique em ![ícone de definições de consulta](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) junto a **"Source"** em **'Aplicadas passos'** à direita **definições da consulta** painel. Ignore quaisquer mensagens de aviso que aparecem.
   * No pop de fora da janela, substitua **'Server'** e **'Database'** com os seus próprios nomes de servidor e base de dados e, em seguida, clique em **'OK'**. Nome de servidor, certifique-se de que especifica a porta 1433 (**YourSoutionName.database.windows.net, 1433**). Deixar o campo de base de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem no ecrã.
   * No pop seguinte terminar janela, verá duas opções no painel da esquerda (**Windows** e **base de dados**). Clique em **'Database'**, preencha o **'Username'** e **'Password'** (este é o nome de utilizador e palavra-passe que introduziu quando implementou a solução pela primeira vez e criou uma base de dados SQL do Azure). No ***selecione o nível para aplicar estas definições***, verifique a opção de nível de base de dados. Em seguida, clique em **'Connect'**.
   * Clique na segunda tabela **PMResult** , em seguida, clique em ![ícone de navegação](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) junto a **"Source"** em **'Aplicadas passos'** à direita **definições da consulta** painel e atualizar os base de dados de nomes do servidor e como os passos acima e clique em OK.
   * Assim que estiver a orientado regressar à página anterior, feche a janela. Uma mensagem será destaque out - clique em **aplicar**. Por último, clique o **guardar** botão para guardar as alterações. O ficheiro do Power BI tem agora estabelecer ligação ao servidor. Se as visualizações estiverem vazios, certifique-se que desmarcar as seleções na visualizações para visualizar todos os dados clicando no ícone de eraser no canto superior direito de legendas. Utilize o botão de atualização para refletir novos dados em visualizações. Inicialmente, apenas verá os dados de seed no seu visualizações como a fábrica de dados está agendada para atualizar todos os 3 horas. Depois de 3 horas, verá uma nova predições serão refletidas na sua visualizações quando atualizar os dados.
3. (Opcional) Publicar o dashboard de caminho típico para [Power BI online](http://www.powerbi.com/). Tenha em atenção que este passo necessita de uma conta Power BI (ou conta do Office 365).
   
   * Clique em **"Publicar"** e uma janela de alguns segundos mais tarde é apresentada a apresentar "A publicação com êxito do Power BI!" com uma marca de verificação verde. Clique na ligação abaixo "Abrir PredictiveMaintenanceAerospace.pbix no Power BI". Para obter instruções detalhadas, consulte [publicar do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo dashboard: clique o  **+**  junto a assinar o **Dashboards** secção no painel esquerdo. Introduza o nome "Demonstração de manutenção preditiva" para este dashboard novo.
   * Depois de abrir o relatório, clique em ![ícone de PINO](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) para afixar ao dashboard todas as visualizações. Para obter instruções detalhadas, consulte [afixar um mosaico a um dashboard do Power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Aceda à página do dashboard e ajustar o tamanho e localização do seu visualizações e editar os títulos. Para obter instruções detalhadas sobre como editar os seus mosaicos, consulte [editar um mosaico – redimensionamento, move, mudar o nome, pin, eliminar, adicione hyperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Eis um dashboard de exemplo com algumas visualizações de caminho típico afixado ao mesmo.  Consoante o período de tempo executar o gerador de dados, os seus números de visualizações poderão ser diferentes.
     <br/>
     ![Vista final](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, Paire o rato sobre o **PredictiveMaintenanceAerospace** conjunto de dados, clique em ![reticências ícone](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) e, em seguida, escolha **agendar atualização**.
     <br/>
     **Nota:** se vir um massage aviso, clique em **editar credenciais de** e certifique-se de que as credenciais da sua base de dados são os mesmos que os descrito no passo 1.
     <br/>
     ![Agendar a atualização](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expanda o **agendar atualização** secção. Ative "manter os dados atualizados".
     <br/>
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte [de atualização de dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Dashboard de acesso frequente caminho de configuração
Os passos seguintes irão ajudá-lo como visualizar a saída de dados em tempo real de tarefas do Stream Analytics que foram geradas no momento da implementação da solução. A [Power BI online](http://www.powerbi.com/) conta é necessária para efetuar os seguintes passos. Se não tiver uma conta, pode [criar um](https://powerbi.microsoft.com/pricing).

1. Adicione a saída do Power BI no Azure Stream Analytics (ASA).
   
   * Terá de seguir as instruções em [Azure Stream Analytics & Power BI: um dashboard de análise em tempo real de visibilidade em tempo real dos dados de transmissão em fluxo](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar o resultado da tarefa do Azure Stream Analytics como seu dashboard do Power BI.
   * A consulta do ASA tem três saídas que são **aircraftmonitor**, **aircraftalert**, e **flightsbyhour**. Pode ver a consulta, clicando no separador de consulta. Correspondente a cada estas tabelas, terá de adicionar uma saída ao ASA. Ao adicionar a saída primeiro (*por exemplo,* **aircraftmonitor**) certifique-se de que o **Alias de saída**, **nome do conjunto de dados** e **nome da tabela** são os mesmos (**aircraftmonitor**). Repita os passos para adicionar as saídas para **aircraftalert**, e **flightsbyhour**. Depois de ter adicionado todas as três tabelas de saída e iniciou a tarefa do ASA, receberá uma mensagem de confirmação (*por exemplo,*, "Tarefa de início Stream Analytics maintenancesa02asapbi foi concluída com êxito").
2. Inicie sessão no [Power BI online](http://www.powerbi.com)
   
   * No painel esquerdo secção de conjuntos de dados na minha área de trabalho, o ***DATASET*** nomes **aircraftmonitor**, **aircraftalert**, e **flightsbyhour** deve aparecer. Estes são os dados de transmissão em fluxo enviado a partir do Azure Stream Analytics no passo anterior. O conjunto de dados **flightsbyhour** não pode aparecer no mesmo tempo que os outros dois conjuntos de dados devido à natureza da consulta SQL por trás dele. No entanto, este deve aparecer depois de uma hora.
   * Certifique-se de que o ***visualizações*** painel está aberta e é apresentado no lado direito do ecrã.
3. Assim que tiver dados que fluem para Power BI, pode começar a visualizar os dados de transmissão em fluxo. Abaixo está um dashboard de exemplo com algumas visualizações de caminho frequente afixado ao mesmo. Pode criar outros mosaicos do dashboard com base nos conjuntos de dados adequados. Consoante o período de tempo executar o gerador de dados, os seus números de visualizações poderão ser diferentes.

    ![Vista de dashboard](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Seguem-se alguns passos para criar um dos mosaicos acima – "vista Fleet do Sensor 11 vs. Limiar 48.26" mosaico:
   
   * Clique em dataset **aircraftmonitor** no painel esquerdo secção de conjuntos de dados.
   * Clique em de **gráfico de linhas** ícone.
   * Clique em **processados** no **campos** painel, para que as TI mostra em "Eixo" o **visualizações** painel.
   * Clique em "s11" e "s11\_alerta", para que ambos são apresentados em "Valores". Clique na seta pequena junto a **s11** e **s11\_alerta**, altere "Sum" para "Médio".
   * Clique em **guardar** na parte superior e nome do relatório "aircraftmonitor". O relatório com o nome "aircraftmonitor" será apresentado no **relatórios** secção o **navegador** painel à esquerda.
   * Clique em de **Pin Visual** ícone no canto superior direito neste gráfico de linhas. Pode aparecer uma janela de "Pin ao Dashboard" para que possa escolher um dashboard. Selecione "Demonstração de manutenção preditiva", em seguida, clique em "Pin".
   * Paire o rato sobre este mosaico no dashboard, clique no ícone "edit" no canto superior direito para alterar o título para a "vista Fleet do Sensor 11 vs. Limiar 48.26" e subtítulo do como"Média em frota ao longo do tempo".

## <a name="how-to-delete-your-solution"></a>**Como eliminar a sua solução**
Certifique-se de que pare o gerador de dados quando não estiver a utilizar a solução como executar o gerador de dados estará sujeita custos superiores. Elimine a solução se não o estão a utilizar. A eliminar a sua solução irá eliminar todos os componentes aprovisionados na sua subscrição quando implementou a solução. Para eliminar a solução, clique no nome da solução no painel esquerdo do modelo de solução e clique em eliminar.

## <a name="cost-estimation-tools"></a>**Ferramentas de estimativa de custos**
As ferramentas de dois seguintes estão disponíveis para o ajudar a compreender melhor os custos totais envolvidos na execução da manutenção preditiva para o modelo de solução aeroespacial na sua subscrição:

* [Microsoft Azure custo Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de Estimator de custo do Microsoft Azure (ambiente de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)

