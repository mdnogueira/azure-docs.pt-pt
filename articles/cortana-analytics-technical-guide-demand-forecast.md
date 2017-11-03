---
title: "Exigir previsão no guia de energia Technical | Microsoft Docs"
description: "Um guia técnico para o modelo de solução com o Microsoft Cortana Intelligence para a pedido previsão no energia."
services: cortana-analytics
documentationcenter: 
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: ed2a17fd735c1b0e67cbf5d08450d36620d4c857
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guia técnico para o modelo de solução do Cortana Intelligence para a pedido previsão de energia
## <a name="overview"></a>**Descrição geral**
Modelos de solução foram concebidos para acelerar o processo de criação de uma demonstração de E2E por cima do Cortana Intelligence Suite. Um modelo implementado Aprovisiona a sua subscrição com componentes do Cortana Intelligence necessárias e criar relações entre. Também implementa o pipeline de dados com dados de exemplo obter gerados a partir de uma aplicação de simulação de dados. Transferir o simulador de dados a partir da ligação fornecida e instalá-lo no seu computador local, consulte o ficheiro de readme.txt para instruções sobre como utilizar o simulador. Dados gerados a partir do simulador hydrates o pipeline de dados e iniciar geração de predição de aprendizagem, que pode ser visualizada no dashboard do Power BI.

É possível encontrar o modelo de solução [aqui](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

O processo de implementação descreve vários passos para configurar as credenciais da sua solução. Certifique-se de que estas credenciais, tais como o nome da solução, nome de utilizador e palavra-passe, que terá de fornecer durante a implementação de registo.

O objetivo deste documento é explicam a arquitetura de referência e os diferentes componentes aprovisionados na sua subscrição como parte deste modelo de solução. O documento aborda também como substituir os dados de exemplo, com dados reais da sua própria para conseguir ver insights/predições da won dados. Além disso, os talks documento sobre as partes do modelo de solução que teria de ser modificado para personalizar a solução com os seus próprios dados. Instruções sobre como criar o dashboard do Power BI para este modelo de solução são fornecidas no final.

## <a name="details"></a>**Detalhes**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arquitetura explicada
Quando a solução for implementada, vários serviços do Azure dentro da Cortana Analytics Suite são ativados (ou seja, Hub de eventos, Stream Analytics, HDInsight, Data Factory, Machine Learning, *etc.*). O diagrama de arquitetura mostra como o pedido de previsão para o modelo de solução de energia construída a partir das ponto-a-ponto, um nível elevado. Pode investigar estes serviços clicando nos mesmos no diagrama de modelo de solução criado com a implementação da solução. As secções seguintes descrevem cada peça.

## <a name="data-source-and-ingestion"></a>**Origem de dados e ingestão**
### <a name="synthetic-data-source"></a>Origem de dados sintético
Para este modelo, a origem de dados utilizada é gerada a partir de uma aplicação de ambiente de trabalho que transferir e executar localmente após a implementação com êxito. Localizar as instruções para transferir e instalar esta aplicação na barra de propriedades, ao selecionar o primeiro nó chamado simulador de dados de previsão de energia no diagrama de modelo de solução. Esta aplicação feeds o [Hub de eventos do Azure](#azure-event-hub) serviço com pontos de dados ou eventos que são utilizados no resto do fluxo de solução.

A aplicação de geração de eventos preenche o Hub de eventos do Azure apenas enquanto está a executar no seu computador.

### <a name="azure-event-hub"></a>Hub de eventos do Azure
O [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é o destinatário da entrada fornecida pela origem de dados sintético descrito.

## <a name="data-preparation-and-analysis"></a>**Preparação de dados e análise**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
O [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço é utilizado para fornecer perto de análise em tempo real no fluxo de entrada do [Hub de eventos do Azure](#azure-event-hub) de serviço e publicar os resultados para um [Power BI](https://powerbi.microsoft.com) dashboard, bem como arquivar todos os eventos de entrada não processados para o [Storage do Azure](https://azure.microsoft.com/services/storage/) serviço para processamento posterior pelo [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço.

### <a name="hdinsight-custom-aggregation"></a>Agregação do HDInsight personalizadas
O serviço do Azure HDInsight é utilizado para executar [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts (orquestradas pelo Azure Data Factory) para fornecer as agregações nos eventos não processados que foram arquivados utilizando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) é utilizado o serviço (orquestradas pelo Azure Data Factory) para fazer previsões no consumo de energia futuras de uma região específica fornecido entradas recebidas.

## <a name="data-publishing"></a>**Publicação de dados**
### <a name="azure-sql-database-service"></a>Serviço de base de dados SQL do Azure
O [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) serviço é utilizado para armazenar (gerida pelo Azure Data Factory) serão as predições recebidas pelo serviço do Azure Machine Learning que é consumido no [Power BI](https://powerbi.microsoft.com) dashboard.

## <a name="data-consumption"></a>**Consumo de dados**
### <a name="power-bi"></a>Power BI
O [Power BI](https://powerbi.microsoft.com) serviço é utilizado para mostrar um dashboard que contém as agregações fornecidas pelo [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço, bem como a pedido prever resultados armazenados no [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidos utilizando o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) serviço. Para obter instruções sobre como criar o dashboard do Power BI para este modelo de solução, consulte a secção seguinte.

## <a name="how-to-bring-in-your-own-data"></a>**Como colocar os seus próprios dados**
Esta secção descreve como colocar os seus próprios dados para o Azure e que áreas iriam requerer alterações para dados que trouxer para esta arquitetura.

Não é provável que qualquer conjunto de dados que colocar iria corresponder o conjunto de dados utilizado para este modelo de solução. Compreender os seus dados e os requisitos são fundamental na forma como modificar este modelo para trabalhar com os seus próprios dados. Se estiver familiarizado serviço Azure Machine Learning, pode obter uma introdução ao mesmo utilizando o exemplo na [como criar a sua primeira experimentação](machine-learning/studio/create-experiment.md).

As secções seguintes abordam as secções do modelo de que necessita de modificações quando é apresentado um novo conjunto de dados.

### <a name="azure-event-hub"></a>Hub de eventos do Azure
O [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) serviço é genérico, que podem ser publicados dados para o hub no formato CSV ou JSON. Nenhum processamento especial ocorre no Hub de eventos do Azure, mas é importante que compreender os dados que sejam fornecidos para a mesma.

Este documento descreve como para a ingestão de dados, mas um pode enviar facilmente eventos ou dados para um Hub de eventos do Azure, utilizando o [API de Hub de eventos](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
O [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) serviço é utilizado para fornecer perto de análise em tempo real ao ler a partir de fluxos de dados e exportar dados para qualquer número de origens.

Para o pedido de previsão para o modelo de solução de energia, a consulta do Azure Stream Analytics é composta por dois subconsultas, cada consumir eventos a partir do serviço de Hub de eventos do Azure como entradas e ter saídas de duas localizações distintas. Estes saídas é composto por um conjunto de dados do Power BI e uma localização de armazenamento do Azure.

O [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) consulta pode ser encontrada por:

* Iniciar sessão para o [portal do Azure](https://manage.windowsazure.com/)
* Localizar as tarefas do stream analytics ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) que foram gerados quando a solução foi implementada. Um é enviar por push dados para o blob storage (por exemplo, mytest1streaming432822asablob) e o outro é para enviar por push dados para o Power BI (por exemplo, mytest1streaming432822asapbi).
* Selecionar

  * ***ENTRADAS*** ver a consulta de entrada
  * ***CONSULTA*** para ver a consulta próprio
  * ***PRODUZ*** para ver as saídas diferentes

Podem encontrar informações sobre a construção de consulta do Azure Stream Analytics no [referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, a tarefa do Azure Stream Analytics que produz o conjunto de dados com informações de análise em tempo real sobre o fluxo de dados de entrada a um dashboard do Power BI quase é fornecida como parte deste modelo de solução. Porque não há um conhecimento implícito sobre o formato de dados de entrada, estas consultas teria de ser alterados com base no formato de dados.

A outra tarefa do Azure Stream Analytics produz todas [Hub de eventos](https://azure.microsoft.com/services/event-hubs/) eventos [Storage do Azure](https://azure.microsoft.com/services/storage/) e, por conseguinte, não requer nenhuma alteração independentemente do formato de dados uma vez que as informações de eventos completo é transmitida em fluxo para o armazenamento.

### <a name="azure-data-factory"></a>Azure Data Factory
O [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) serviço orquestra o movimento e processamento de dados. No pedido de previsão para o modelo de solução de energia a fábrica de dados é constituída por 12 [pipelines](data-factory/concepts-pipelines-activities.md) que mova e processar os dados utilizando várias tecnologias.

  Pode aceder a fábrica de dados, abrindo o nó de fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implementação da solução. Verá a fábrica de dados no portal do Azure. Se observar erros nos conjuntos de dados, pode ignorar os conforme forem devido a fábrica de dados que está a ser implementada antes do gerador de dados foi iniciado. Esses erros, não impedem a fábrica de dados de funcionar.

Esta secção descreve o necessário [pipelines](data-factory/concepts-pipelines-activities.md) e [atividades](data-factory/concepts-pipelines-activities.md) contidas no [do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Imagem seguinte é a vista de diagrama da solução:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Cinco pipelines de dados nesta fábrica conter [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são utilizados para a partição e agregar os dados. Quando indicado, os scripts localizados no [Storage do Azure](https://azure.microsoft.com/services/storage/) conta criada durante a configuração. A localização é: demandforecasting\\\\script\\\\hive\\ \\ (ou https://[Your solução name].blob.core.windows.net/demandforecasting).

Semelhante para o [Azure Stream Analytics](#azure-stream-analytics-1) consultas, o [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts têm um conhecimento implícito sobre o formato de dados de entrada, estas consultas teria de ser alterados com base no formato de dados e [engenharia da funcionalidade](machine-learning/team-data-science-process/create-features.md) requisitos.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
Isto [pipeline](data-factory/concepts-pipelines-activities.md) contém numa única atividade - um [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) utilizando a atividade um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) criar scripts para dados de pedido de transmissão em fluxo no agregado cada 10 segundos no nível de substation ao nível de região hora a hora e colocada no [Storage do Azure](https://azure.microsoft.com/services/storage/) através da tarefa do Azure Stream Analytics.

O [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa de criação de partições é ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
Isto [pipeline](data-factory/concepts-pipelines-activities.md) contém duas atividades:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script de ramo de registo para agregar os dados de pedido de histórico horária no nível substation ao nível de região hora a hora e colocar no armazenamento do Azure durante a tarefa do Azure Stream Analytics
* [Cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os dados agregados a partir do blob Storage do Azure para a base de dados de SQL do Azure que tenha sido aprovisionado como parte da instalação do modelo de solução.

O [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script para esta tarefa é ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Estes [pipelines](data-factory/concepts-pipelines-activities.md) contêm várias atividades e cujo resultado final é serão as predições classificadas de experimentação do Azure Machine Learning associado a este modelo de solução. São quase idênticos exceto cada um deles só processa a região diferente, o que está a ser efetuada pelo RegionID diferentes passado o pipeline ADF e o script de ramo de registo para cada região.  
As atividades contidas neste pipeline são:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script de ramo de registo para efetuar agregações e funcionalidade engenharia necessária para a experimentação do Azure Machine Learning. Os scripts Hive para esta tarefa são respetivos ***PrepareMLInputRegionX.hql***.
* [Cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados do [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) atividade para um único blob Storage do Azure que pode ser acesso pelo [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) atividade.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) atividade que chama a experimentação do Azure Machine Learning, o que resulta nos resultados que está a ser colocados num único blob Storage do Azure.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Isto [pipeline](data-factory/concepts-pipelines-activities.md) contém numa única atividade - um [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os resultados da experimentação do Azure Machine Learning a partir do respetivo ***MLScoringRegionXPipeline***à base de dados do SQL do Azure foi aprovisionado como parte da instalação do modelo de solução.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Isto [pipeline](data-factory/concepts-pipelines-activities.md) contém numa única atividade - um [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os dados agregados de pedido em curso do ***LoadHistoryDemandDataPipeline*** para o Azure SQL Base de dados que foi aprovisionado como parte da instalação do modelo de solução.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyTopologyDataPipeline CopyRegionDataPipeline, CopySubstationDataPipeline,*
Isto [pipeline](data-factory/concepts-pipelines-activities.md) contém numa única atividade - um [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) atividade que move os dados de referência de região/Substation/Topologygeo que são carregados para o blob Storage do Azure como parte do modelo de solução instalação da base de dados do SQL do Azure foi aprovisionado como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentação utilizado para este modelo de solução fornece a predição de procura da região. A experimentação é específica para o conjunto de dados consumido e, por conseguinte, requer modificação ou específica para os dados que seja colocados em substituição.

## <a name="monitor-progress"></a>**Monitorize o progresso**
Depois do gerador de dados é iniciado, o pipeline começa a obter hydrated e os diferentes componentes da sua solução iniciar kicking para a seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas de monitorizar o pipeline.

1. Verifique os dados do Blob Storage do Azure.

    Uma das tarefas do Stream Analytics escreve os dados de entrada não processados para o blob storage. Se clicar em **Blob Storage do Azure** componente da sua solução a partir do ecrã implementado com êxito a solução e, em seguida, clique em **abra** no painel correto, que demora ao [Azure Portal](https://portal.azure.com). Uma vez, clique em **Blobs**. No painel seguinte, é apresentada uma lista de contentores. Clique em **"energysadata"**. No painel seguinte, consulte o **"demandongoing"** pasta. Para a pasta de rawdata, verá que as pastas com nomes como data = 2016-01-28 etc. Se vir estas pastas, indica que os dados não processados com êxito a ser gerado no seu computador e armazenados no blob storage. Deverá ver os ficheiros que devem ter tamanhos finito em MB nessas pastas.
2. Verifique os dados da SQL Database do Azure.

    É o último passo do pipeline de escrever dados (por exemplo, predições do machine learning) na base de dados do SQL Server. Poderá ter de esperar um horas oftwo máximo para os dados são apresentados na base de dados do SQL Server. Uma forma para monitorizar a quantidade de dados está disponível na base de dados SQL é através de [portal do Azure](https://manage.windowsazure.com/). No painel esquerdo localizar bases de dados SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) e clique no mesmo. Em seguida, localize a base de dados (ou seja, demo123456db) e clique no mesmo. Na página seguinte em **"Ligar à base de dados"** secção, clique em **"Transact-SQL de executar consultas da base de dados do SQL Server"**.

    Aqui, pode clicar em nova consulta e a consulta para o número de linhas (por exemplo, "selecione Count do DemandRealHourly)" à medida que aumenta a sua base de dados, deve aumentar o número de linhas na tabela.)
3. Verifique os dados a partir do dashboard do Power BI.

    Pode configurar o dashboard do Power BI caminho frequente para monitorizar os dados de entrada não processados. Siga as instruções na secção "Dashboard do Power BI".

## <a name="power-bi-dashboard"></a>**Dashboard do Power BI**
### <a name="overview"></a>Descrição geral
Esta secção descreve como configurar o dashboard do Power BI para visualizar os seus dados em tempo real de análise do Azure stream (caminho quente), bem como prever resultados do Azure machine learning (caminho amovíveis).

### <a name="setup-hot-path-dashboard"></a>Dashboard de acesso frequente caminho de configuração
Os seguintes passos guiá-lo como visualizar a saída de dados em tempo real de tarefas do Stream Analytics que foram geradas no momento da implementação da solução. A [Power BI online](http://www.powerbi.com/) conta é necessária para efetuar os seguintes passos. Se não tiver uma conta, pode [criar um](https://powerbi.microsoft.com/pricing).

1. Adicione a saída do Power BI no Azure Stream Analytics (ASA).

   * Tem de seguir as instruções em [Azure Stream Analytics & Power BI: um dashboard de análise em tempo real de visibilidade em tempo real dos dados de transmissão em fluxo](stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar o resultado da tarefa do Azure Stream Analytics como seu dashboard do Power BI .
   * Localize a tarefa do stream analytics na sua [portal do Azure](https://manage.windowsazure.com). O nome da tarefa deve ser: YourSolutionName + "streamingjob" + número aleatório + "asapbi" (ou seja, demostreamingjob123456asapbi).
   * Adicione uma saída de PowerBI para a tarefa do ASA. Definir o **Alias de saída** como **'PBIoutput'**. Definir o **nome de Dataset** e **nome de tabela** como **'EnergyStreamData'**. Depois de adicionar a saída, clique em **"Start"** na parte inferior da página para iniciar a tarefa de Stream Analytics. Deve obter uma mensagem de confirmação (por exemplo, "Iniciar tarefa de stream analytics myteststreamingjob12345asablob foi concluída com êxito").
2. Inicie sessão no [Power BI online](http://www.powerbi.com)

   * No painel esquerdo, secção de conjuntos de dados na minha área de trabalho, deve ser capaz de ver um novo conjunto de dados que mostra no painel esquerdo do Power BI. Estes são os dados de transmissão em fluxo enviado a partir do Azure Stream Analytics no passo anterior.
   * Certifique-se de que o ***visualizações*** painel está aberta e é apresentado no lado direito do ecrã.
3. Crie o mosaico "A pedido pelo Timestamp":

   * Clique em dataset **'EnergyStreamData'** no painel esquerdo secção de conjuntos de dados.
   * Clique em **"Gráfico de linhas"** ícone ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Clique em 'EnergyStreamData' no **campos** painel.
   * Clique em **"Timestamp"** e certifique-se mostra na "Eixo". Clique em **"Carga"** e certifique-se mostra na "Valores".
   * Clique em **guardar** na parte superior e nome do relatório como "EnergyStreamDataReport". O relatório com o nome "EnergyStreamDataReport" é apresentado na secção de relatórios no painel de navegador no lado esquerdo.
   * Clique em **"Pin Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ícone no canto superior direito do gráfico linha, uma janela de "Pin ao Dashboard" poderá aparecer para escolher um dashboard. Selecione "EnergyStreamDataReport", em seguida, clique em "Pin".
   * Paire o rato sobre este mosaico no dashboard, clique em "Editar" ícone no canto superior direito para alterar o título dele como "Pedido pelo Timestamp"
4. Crie outros mosaicos do dashboard com base nos conjuntos de dados adequados. A vista de final dashboard:![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Dashboard de caminho típico de configuração
No pipeline de dados de caminho típico, o objetivo essencial é obter a previsão de pedido de cada região. Power BI liga-se para uma base de dados SQL do Azure como origem de dados, onde estão armazenados os resultados de predição.

> [!NOTE]
> 1) Demora algumas horas para recolher os resultados suficiente previsão para o dashboard. Recomendamos que inicie este processo 2 a 3 horas após lunch o gerador de dados. 2) neste passo, o pré-requisito é transferir e instalar o software gratuito [ambiente de trabalho do Power BI](https://powerbi.microsoft.com/desktop).
>
>

1. Obter as credenciais da base de dados.

   Terá de **base de dados de nome do servidor, nome de base de dados, nome de utilizador e palavra-passe** antes de passar para os passos seguintes. Eis os passos para ajudá-lo como localizá-las.

   * Uma vez **"SQL Database do Azure"** no seu modelo de solução diagrama fica verde e, em seguida, clique em **"Abrir"**. Será guiado para o portal do Azure e abrir a página de informações da base de dados bem.
   * Na página, pode encontrar uma secção de "Base de dados". Lista fora da base de dados que criou. O nome da base de dados deve ser **"O nome da solução + número aleatório + 'db'"** (por exemplo, "mytest12345db").
   * Clique em sua base de dados, no pop nova saída painel, pode encontrar o nome do servidor de base de dados na parte superior. O nome do servidor de base de dados deve ser `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (por exemplo, "mytest12345.database.windows.net,1433").
   * A base de dados **username** e **palavra-passe** são o mesmo nome de utilizador e palavra-passe anteriormente registado durante a implementação da solução.
2. Atualizar a origem de dados do ficheiro do Power BI de caminho típico

   * Certifique-se de que instalou a versão mais recente do [ambiente de trabalho do Power BI](https://powerbi.microsoft.com/desktop).
   * No **"DemandForecastingDataGeneratorv1.0"** pasta que transferiu, faça duplo clique o **'Power BI Template\DemandForecastPowerBI.pbix'** ficheiro. Iniciais visualizações baseadas nos dados fictícias. **Nota:** se vir uma mensagem de erro, certifique-se de que instalou a versão mais recente do Power BI Desktop.

     Depois de aberto, na parte superior do ficheiro, clique em **'Editar consultas'**. No pop de fora da janela, faça duplo clique **"Source"** no painel direito.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * No pop de fora da janela, substitua **"Server"** e **"Base de dados"** com os seus próprios nomes de servidor e base de dados e, em seguida, clique em **"OK"**. Nome de servidor, certifique-se de que especifica a porta 1433 (**YourSolutionName.database.windows.net, 1433**). Ignore as mensagens de aviso que aparecem no ecrã.
   * No pop seguinte terminar janela, verá duas opções no painel da esquerda (**Windows** e **base de dados**). Clique em **"Base de dados"**, preencha o **"Nomedeutilizador"** e **"Password"** (este é o nome de utilizador e palavra-passe que introduziu quando implementou a solução pela primeira vez e criou uma base de dados SQL do Azure). No ***selecione o nível para aplicar estas definições***, verifique a opção de nível de base de dados. Em seguida, clique em **"Ligar"**.
   * Assim que estiver a orientado regressar à página anterior, feche a janela. Um pops de mensagem de saída - clique em **aplicar**. Por último, clique o **guardar** botão para guardar as alterações. O ficheiro do Power BI tem agora estabelecer ligação ao servidor. Se as visualizações estiverem vazios, certifique-se que desmarcar as seleções na visualizações para visualizar todos os dados clicando no ícone de eraser no canto superior direito de legendas. Utilize o botão de atualização para refletir novos dados em visualizações. Inicialmente, vê apenas os dados de seed no seu visualizações como a fábrica de dados está agendada para atualizar todos os 3 horas. Depois de 3 horas, verá predições novo refletidas na sua visualizações quando atualizar os dados.
3. (Opcional) Publicar o dashboard de caminho típico para [Power BI online](http://www.powerbi.com/). Tenha em atenção que este passo necessita de uma conta Power BI (ou conta do Office 365).

   * Clique em **"Publicar"** e uma janela de alguns segundos mais tarde é apresentada a apresentar "A publicação com êxito do Power BI!" com uma marca de verificação verde. Clique na ligação seguinte "Demoprediction.pbix abrir no Power BI". Para obter instruções detalhadas, consulte [publicar do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo dashboard: clique o  **+**  junto a assinar o **Dashboards** secção no painel esquerdo. Introduza o nome "Demonstração de previsão a pedido" para este dashboard novo.
   * Depois de abrir o relatório, clique em ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) para afixar ao dashboard todas as visualizações. Para obter instruções detalhadas, consulte [afixar um mosaico a um dashboard do Power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Aceda à página do dashboard e ajustar o tamanho e localização do seu visualizações e editar os títulos. Para obter instruções detalhadas sobre como editar os seus mosaicos, consulte [editar um mosaico – redimensionamento, move, mudar o nome, pin, eliminar, adicione hyperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Eis um dashboard de exemplo com algumas visualizações de caminho típico afixado ao mesmo.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Opcional) Agendar a atualização da origem de dados.

   * Para agendar a atualização dos dados, Paire o rato sobre o **EnergyBPI Final** conjunto de dados, clique em ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) e, em seguida, escolha **agendar atualização**.
     **Nota:** se vir um massage aviso, clique em **editar credenciais de** e certifique-se de que as credenciais da sua base de dados são os mesmos que os descrito no passo 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Expanda o **agendar atualização** secção. Ative "manter os dados atualizados".
   * Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte [de atualização de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Como eliminar a sua solução**
Certifique-se de que pare o gerador de dados quando não estiver a utilizar a solução como executar o gerador de dados implica custos superiores. Elimine a solução se não o estão a utilizar. A eliminar a sua solução elimina todos os componentes aprovisionados na sua subscrição quando implementou a solução. Para eliminar a solução, clique no nome da solução no painel esquerdo do modelo de solução e clique em eliminar.

## <a name="cost-estimation-tools"></a>**Ferramentas de estimativa de custos**
As ferramentas de dois seguintes estão disponíveis para o ajudar a compreender melhor os custos totais envolvidos na executar o pedido de previsão para o modelo de solução de energia na sua subscrição:

* [Microsoft Azure custo Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta de Estimator de custo do Microsoft Azure (ambiente de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Confirmações**
Este artigo foi criado pelo scientist dados Yijing Chen e engenheiro de software Qiu Min na Microsoft.
