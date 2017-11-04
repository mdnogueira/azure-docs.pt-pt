---
title: "Descrição detalhada para prever o estado de funcionamento vehicle e ocasionar hábitos - Azure | Microsoft Docs"
description: "Utilizar as capacidades do Cortana Intelligence para obter informações acerca de preditiva e em tempo real em estado de funcionamento vehicle e ocasionar hábitos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 4050fdc2056df395bbcc37e3783f61eebd90f80a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Playbook de solução de análise de telemetria vehicle: análise detalhada da solução
Isto **menu** ligações para as secções de neste manual de comunicação social: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Esta secção aprofunda em cada uma das fases descritas na arquitetura da solução com instruções e ponteiros para personalização. 

## <a name="data-sources"></a>Origens de Dados
A solução utiliza duas origens de dados diferentes:

* **simulated vehicle sinais e conjunto de dados de diagnóstico** e 
* **catálogo de vehicle**

Um simulador de telematics vehicle é incluído como parte desta solução. Este emite informações de diagnóstico e sinalizar correspondente para o estado do veículo e o padrão despertar num determinado ponto no tempo. Clique em [Vehicle Telematics simulador](http://go.microsoft.com/fwlink/?LinkId=717075) para transferir o **Vehicle Telematics simulador solução do Visual Studio** para personalizações com base nos seus requisitos. O catálogo de vehicle contém um conjunto de dados de referência com um VIN para mapeamento de modelo.

![Simulador de telematics vehicle](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)

*Figura 1 – Vehicle Telematics simulador*

Este é um conjunto de dados formatado em JSON, que contém o esquema seguinte.

| Coluna | Descrição | Valores |
| --- | --- | --- |
| VIN |Número de identificação de Vehicle gerado aleatoriamente |Isto é obtido a partir de uma lista de números de identificação de vehicle gerado aleatoriamente 10 000 principal. |
| Temperatura externa |A temperatura externa onde está a ocasionar o veículo |Número gerado aleatoriamente entre 0-100 |
| Temperatura do motor |A temperatura de motor do veículo |Número gerado aleatoriamente de 0 500 |
| Velocidade |A velocidade do motor no qual está a ocasionar ao veículo |Número gerado aleatoriamente entre 0-100 |
| Fuel |O nível de fuel do veículo |Número gerado aleatoriamente entre 0-100 (indica a percentagem de nível de fuel) |
| EngineOil |O nível de petróleo do motor do veículo |Número gerado aleatoriamente entre 0-100 (indica a percentagem de nível de petróleo motor) |
| Tire pressão |A pressão de tire do veículo |Gerado aleatoriamente número a partir de 0 a 50 (indica a percentagem de nível de pressão tire) |
| Odometer |A leitura de odometer do veículo |Número gerado aleatoriamente entre 0-200000 |
| Accelerator_pedal_position |A posição pedal acelerador do veículo |Número gerado aleatoriamente entre 0-100 (indica a percentagem de nível de acelerador) |
| Parking_brake_status |Indica se o veículo é parqueado ou não |TRUE ou False |
| Headlamp_status |Indica onde o headlamp é sobre ou não |TRUE ou False |
| Brake_pedal_status |Indica se o pedal brake é premido ou não |TRUE ou False |
| Transmission_gear_position |A posição de equipamento de transmissão do veículo |Estados: primeiro, segundo, terceiros, fourth, fifth, sixth, seventh, eighth |
| Ignition_status |Indica se o veículo está em execução ou parado |TRUE ou False |
| Windshield_wiper_status |Indica se o wiper windshield está ativada ou não |TRUE ou False |
| ABS |Indica se ABS é parte ou não |TRUE ou False |
| Timestamp |Timestamp quando o ponto de dados é criado |Data |
| Cidade |A localização do veículo |4 cidades nesta solução: Bellevue, Redmond, Sammamish, Seattle |

O conjunto de dados de referência de modelo vehicle contém VIN para o mapeamento de modelo. 

| VIN | Modelo |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedan |
| 8J0U8XCPRGW4Z3NQE |Híbrido |
| WORG68Z2PLTNZDBI7 |Família Saloon |
| JTHMYHQTEPP4WBMRN |Sedan |
| W9FTHG27LZN1YWO0Y |Híbrido |
| MHTP9N792PHK08WJM |Família Saloon |
| EI4QXI2AXVQQING4I |Sedan |
| 5KKR2VB4WHQH97PF8 |Híbrido |
| W9NSZ423XZHAONYXB |Família Saloon |
| 26WJSGHX4MA5ROHNL |Convertíveis |
| GHLUB6ONKMOSI7E77 |Estação Wagon |
| 9C2RHVRVLMEJDBXLP |Carro compacto |
| BRNHVMZOUJ6EOCP32 |SUV pequeno |
| VCYVW0WUZNBTM594J |Desporto carro |
| HNVCE6YFZSA5M82NY |SUV médio |
| 4R30FOR7NUOBL05GJ |Estação Wagon |
| WYNIIY42VKV6OQS1J |SUV grande |
| 8Y5QKG27QET1RBK7I |SUV grande |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Sedan |
| M4TV6IEALD5QDS3IR |Híbrido |
| VHRA1Y2TGTA84F00H |Família Saloon |
| R0JAUHT1L1R3BIKI0 |Sedan |
| 9230C202Z60XX84AU |Híbrido |
| T8DNDN5UDCWL7M72H |Família Saloon |
| 4WPYRUZII5YV7YA42 |Sedan |
| D1ZVY26UV2BFGHZNO |Híbrido |
| XUF99EW9OIQOMV7Q7 |Família Saloon |
| 8OMCL3LGI7XNCC21U |Convertíveis |
| ……. | |

### <a name="references"></a>Referências
[Solução do Visual Studio vehicle Telematics simulador](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)

## <a name="ingestion"></a>Ingestão
Combinações de Event Hubs do Azure, o Stream Analytics e Data Factory estão maximizadas para inserir os sinais vehicle, os eventos de diagnóstico e em tempo real e análise de lote. Todos estes componentes são criados e configurados como parte da implementação da solução. 

### <a name="real-time-analysis"></a>Análise em tempo real
Os eventos gerados pelo simulador de Telematics Vehicle são publicados para o Hub de eventos utilizando o SDK de Hub de eventos. A tarefa de Stream Analytics ingere estes eventos a partir do Event Hub e processa os dados em tempo real para analisar o estado de funcionamento vehicle. 

![Dashboard de hub de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

*Figura 4 - dashboard de Hub de eventos*

![Fluxo de dados de processamento da tarefa de análise](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Figura 5 - tarefa do Stream Analytics, processamento de dados*

A tarefa de Stream Analytics;

* ingere dados do Hub de eventos 
* efetua uma junção com os dados de referência para mapear o veículo VIN para o modelo correspondente 
* mantém-los para o armazenamento de Blobs do Azure para análise de lote avançado. 

A seguinte consulta do Stream Analytics é utilizada para manter os dados para o armazenamento de Blobs do Azure. 

![Consulta de tarefa do Stream Analytics para ingestão de dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Figura 6 - Stream Analytics tarefa consulta para a ingestão de dados*

### <a name="batch-analysis"></a>Análise de lote
Também iremos geraram um volume de sinais vehicle simulada e conjunto de dados de diagnóstico para a análise de lote mais rico adicional. Isto é necessário para garantir um volume de dados representativo para processamento de batches. Para esta finalidade, iremos estiver a utilizar um pipeline com o nome "PrepareSampleDataPipeline" no fluxo de trabalho do Azure Data Factory para gerar visão de um ano de sinais vehicle simulada e conjunto de dados de diagnóstico. Clique em [atividade personalizada do Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) para transferir a atividade de DotNet personalizada da fábrica de dados solução do Visual Studio para personalizações com base nos seus requisitos. 

![Preparar os dados de exemplo para processar o fluxo de trabalho do batch](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*A figura 7 - preparar dados de exemplo para o fluxo de trabalho de processamento de batch*

O pipeline é composta por um .net ADF personalizado atividade, a mostrar aqui:

![Atividade de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figura 8 - PrepareSampleDataPipeline*

Depois do pipeline seja executada com êxito e conjunto de dados "RawCarEventsTable" está marcado como "Pronto", diagnóstico e de um ano, mas de vehicle simulada sinais dados são produzidos. Consulte a seguinte pasta e ficheiro criado na sua conta de armazenamento sob o contentor "connectedcar":

![Saída de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Figura 9 - PrepareSampleDataPipeline saída*

### <a name="references"></a>Referências
[SDK de Hub de eventos do Azure para ingestão da transmissão](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Capacidades de movimento de dados do Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
[atividade do Azure Data Factory DotNet](../../data-factory/v1/data-factory-use-custom-activities.md)

[Azure Data Factory DotNet atividade solução do visual studio para preparar os dados de exemplo](http://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="partition-the-dataset"></a>O conjunto de dados de partição
O sinais vehicle semiestruturados não processados e o conjunto de dados de diagnóstico são partições no passo de preparação de dados para um formato de ano/mês. A criação de partições promove consulta mais eficiente e de armazenamento a longo prazo dimensionável Ativando falhas a ativação pós-falha da conta de um blob para o seguinte como a primeira conta é preenchida. 

>[!NOTE] 
>Este passo na solução é aplicável apenas ao processamento de batches.

Entrada e saída gestão de dados de dados:

* O **dados de saída** (com a etiqueta *PartitionedCarEventsTable*) está a ser mantidas durante um longo período de tempo, como o formulário fundamentais sobre / "rawest" dos dados no cliente "Data Lake". 
* O **dados de entrada** para este pipeline seria normalmente rejeitadas como os dados de saída tem fidelidade completa para a entrada - apenas é armazenada (particionada) melhor para uma utilização posterior.

![Fluxo de trabalho de eventos de carro de partição](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)

*A figura 10 – fluxo de trabalho de eventos de carro de partição*

Os dados não processados estão particionados com uma atividade do ramo de registo do HDInsight no "PartitionCarEventsPipeline". Os dados de exemplo gerados no passo 1 para um ano particionados ano/mês. As partições são utilizadas para gerar vehicle sinais e dados de diagnóstico de cada mês (totais 12 partições) de um ano. 

![Atividade de PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)

*Figura 11 - PartitionCarEventsPipeline*

***Script de ramo de registo PartitionConnectedCarEvents***

O script de ramo de registo seguinte, com o nome "partitioncarevents.hql", é utilizado para criação de partições e está localizado na pasta "\demo\src\connectedcar\scripts" de zip transferido. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

Assim que o pipeline é executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento sob o contentor "connectedcar".

![Saída particionada](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

*Figura 12 - saída particionada*

Os dados agora está otimizada, é mais fácil de gerir e pronto para processamento adicional para obter informações acerca do batch avançado. 

## <a name="data-analysis"></a>Análise de dados
Nesta secção, pode ver como combinar Azure Stream Analytics, o Azure Machine Learning, o Azure Data Factory e o Azure HDInsight para avançada análise avançada no estado de funcionamento vehicle e ocasionar hábitos. Existem três subsecções aqui:

1. **Machine Learning**: Esta subsecção contém informações sobre a experiência de deteção de anomalias que podemos ter utilizada nesta solução para prever veículos necessidade de manutenção de manutenção e veículos exigir recalls devido a problemas de segurança.
2. **Análise em tempo real**: Esta subsecção contém informações sobre a análise em tempo real, utilizando a linguagem de consulta de análise de fluxo e operacionalizar a experimentação de aprendizagem em tempo real, utilizando uma aplicação personalizada.
3. **Análise de lote**: Esta subsecção contém informações sobre a transformar e o processamento dos dados de lote através do Azure HDInsight e o Azure Machine Learning operacionalizado pelo Azure Data Factory.

### <a name="machine-learning"></a>Machine Learning
O nosso objetivo aqui é prever veículos que necessitam de manutenção ou devolução de chamada com base em certas estatísticas de estado de funcionamento. Vamos efetuar os seguintes pressupostos

* Se uma das três condições seguintes forem verdadeira, os veículos requerem **manutenção manutenção**:
  
  * Tire pressão é baixa
  * Nível de petróleo motor está baixo
  * Temperatura motor é elevada
* Se uma das condições seguintes forem verdadeira, os veículos podem ter um **problema de segurança** e exigir **devolução de chamada**:
  
  * Temperatura motor é elevada, mas a temperatura externa é baixa
  * Temperatura motor é baixa, mas a temperatura externa é elevada

Com base nos requisitos anteriores, criámos dois modelos separados para detetar anomalias, um para a deteção de manutenção vehicle e outro para a deteção de devolução de chamada de vehicle. Em ambos os estes modelos, o algoritmo de análise de componente Principal (PCA) incorporado é utilizado para deteção de anomalias. 

**Modelo de deteção de manutenção**

Se um dos três indicadores - tire pressão, petróleo motor ou temperatura motor - satisfaça a condição correspondentes, o modelo de deteção de manutenção relatórios uma anomalias. Como resultado, precisamos apenas a considerar estes três variáveis na criação do modelo. No nosso experimentação no Azure Machine Learning, utilizamos primeiro um **selecionar colunas no conjunto de dados** módulo, a extrair estas três variáveis. Em seguida, utilize o módulo de deteção de anomalias baseado em PCA para criar o modelo de deteção de anomalias. 

Análise de componente principal (PCA) é uma técnica estabelecida no machine learning que pode ser aplicado a seleção de funcionalidades, classificação e deteção de anomalias. PCA converte um conjunto de maiúsculas e minúsculas com possivelmente correlacionadas variáveis, para um conjunto de valores denominados componentes principais. A chave ideia de modelação baseado em PCA é dados de projeto para um espaço inferior dimensional para que as funcionalidades e anomalias o podem ser identificadas mais facilmente.

Para cada entrada nova para o modelo de deteção, o detector anomalias primeiro calcula a projeção no eigenvectors e, em seguida, calcula o erro reconstruction normalizado. Este erro normalizado é a classificação de anomalias. Quanto maior for o erro, mais anómala é a instância. 

No problema de deteção de manutenção, cada registo pode ser considerado como um ponto num espaço de 3-dimensional definido pelo tire pressão, petróleo motor temperatura motor coordenadas. Para capturar estes anomalias, iremos pode projetar os dados originais no espaço de 3-dimensional para um espaço de 2 dimensional PCA a utilizar. Assim, iremos definir o parâmetro número de componentes para utilizar em PCA ser 2. Este parâmetro desempenha um papel importante na aplicação de deteção de anomalias baseado em PCA. Após a projeção dados utilizando PCA, podermos identificar estes anomalias mais facilmente.

**Modelo de deteção de anomalias de devolução de chamada** no modelo de deteção de anomalias de devolução de chamada, utilizamos o selecionar colunas no conjunto de dados e anomalias baseado em PCA módulos de deteção de forma semelhante. Mais concretamente, podemos primeiro de extrair três variáveis - temperatura motor, fora temperatura e velocidade - utilizando o **selecionar colunas no conjunto de dados** módulo. Também podemos incluir a variável de velocidade, uma vez que a temperatura de motor, normalmente, é correlacionada com a velocidade. Em seguida utilizamos o módulo de deteção de anomalias baseado em PCA para projetar os dados a partir do espaço de 3-dimensional para um espaço de 2 dimensional. Os critérios de devolução de chamada forem satisfeitos e o veículo requer a devolução de chamada quando a temperatura de motor e a temperatura externa são altamente negativamente correlacionados. Utilizar o algoritmo de deteção de anomalias baseado em PCA, iremos pode capturar as anomalias depois de efetuar PCA. 

Quando se prepara o modelo, é necessário utilizar dados normais, que não necessitam de manutenção ou devolução de chamada como os dados de entrada para preparar o modelo de deteção de anomalias baseado em PCA. A experimentação de classificação, utilizamos o modelo de deteção de anomalias preparado para detetar se é ou não o veículo requer manutenção ou devolução de chamada. 

### <a name="real-time-analysis"></a>Análise em tempo real
A seguinte consulta de SQL de análise de fluxo é utilizada para aceder a média de todos os parâmetros de vehicle importantes como velocidade vehicle, nível fuel, temperatura do motor, leitura odometer, tire pressão, nível de petróleo motor e a outras pessoas. As médias são utilizadas para detetar anomalias, emitir alertas e determinar as condições de estado de funcionamento geral de veículos operados na região específica e, em seguida, correlacioná-lo a demografia. 

![Consulta de análise de fluxo para o processamento em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

*Figura 13 – consulta do Stream Analytics para o processamento em tempo real*

Todos os médias são calculadas através de um segundo 3 TumblingWindow. Estamos a utilizar TubmlingWindow neste caso, uma vez que Requeremos intervalos de tempo sem sobreposição e contíguos. 

Para saber mais sobre todas as capacidades de "Modos de janela" Azure Stream Analytics, clique em [modos de janela (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Predição em tempo real**

Uma aplicação é incluída como parte da solução para operacionalizar o modelo de machine learning em tempo real. Esta aplicação chamada "RealTimeDashboardApp" é criada e configurada como parte da implementação da solução. A aplicação efetua o seguinte:

1. Escuta a uma instância de Hub de eventos onde Stream Analytics está a publicar eventos num padrão continuamente. ![Consulta de análise de fluxo para publicar os dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) *figura 14 – consulta do Stream Analytics para publicar os dados à saída de uma instância do Hub de eventos* 
2. Para cada evento que recebe esta aplicação: 
   
   * Processa os dados utilizando o ponto final de Machine Learning resposta-pedido de classificação (RRS). O ponto final RRS é publicado automaticamente como parte da implementação.
   * A saída RRS é publicada para um conjunto de dados do Power BI utilizando push APIs.

Este padrão também é aplicável para cenários em que pretende integrar uma aplicação de linha de negócio (LoB) com o fluxo de análise em tempo real, para cenários tais como alertas, notificações e mensagens.

Clique em [RealtimeDashboardApp transferência](http://go.microsoft.com/fwlink/?LinkId=717078) para transferir a solução de RealtimeDashboardApp Visual Studio para personalizações. 

**Para executar a aplicação de Dashboard em tempo real**
1. Extrair e guarde localmente ![RealtimeDashboardApp pasta](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) *figura 16 – RealtimeDashboardApp pasta*  
2. Executar a aplicação RealtimeDashboardApp.exe
3. Forneça credenciais válidas do Power BI, iniciar sessão e clique em aceitar ![Dashboard em tempo real aplicação iniciar sessão no Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![Concluir a aplicação do dashboard em tempo real de início de sessão no Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figura 17 – RealtimeDashboardApp: Início de sessão no Power BI*

>[!NOTE] 
>Se pretender remover o conjunto de dados do Power BI da cache, execute o RealtimeDashboardApp com o parâmetro de "flushdata": 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Análise de lote
O objetivo aqui é para mostrar como Contoso Motors utiliza as capacidades de computação do Azure para o conjunto de testes macrodados para obter informações valiosas no ocasionar padrão, o comportamento de utilização e o estado de funcionamento vehicle. Isto possibilita a:

* Melhorar a experiência do cliente e torná-lo mais barata fornecendo informações em ocasionar hábitos e comportamentos de despertar eficiente fuel
* Saiba mais proativamente sobre os clientes e os respetivos patters despertar governar as decisões de negócio e fornecer melhores informações de produtos de classe e serviços

Nesta solução, vamos como objetivo as métricas seguintes:

1. **Impulsionar comportamento agressiva**: identifica a tendência dos modelos, localizações, despertar condições e tempo do ano para obter informações sobre os padrões de despertar agressiva. Contoso Motors pode utilizar estas informações para campanhas de marketing, impulsionando baseada na utilização banca e de novas funcionalidades personalizadas.
2. **Fuel eficiente comportamento despertar**: identifica a tendência dos modelos, localizações, despertar condições e tempo do ano para obter informações sobre padrões de despertar eficiente fuel. Contoso Motors pode utilizar estas informações para de marketing campanhas, impulsionando novas funcionalidades e relatórios proativa para os controladores de custo hábitos despertar amigáveis e eficaz o ambiente. 
3. **Recuperar modelos**: identifica os modelos que requerem recalls por operacionalizar a anomalias deteção experimentação do machine learning

Vamos ver para os detalhes de cada uma destas métricas,

**Padrão de despertar agressiva**

A vehicle particionada sinais e os dados de diagnóstico são processados no pipeline com o nome "AggresiveDrivingPatternPipeline" utilizando o Hive para determinar os modelos, localização, vehicle, condições despertar e outros parâmetros que exhibits ocasionar agressiva padrão.

![Agressiva despertar fluxo de trabalho do padrão](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 
*figura 18 – agressiva despertar fluxo de trabalho do padrão*


***Consulta de Hive padrão despertar agressiva***

O script de ramo de registo com o nome "aggresivedriving.hql" utilizado para analisar o padrão de condição despertar agressiva esteja localizado na pasta "\demo\src\connectedcar\scripts" zip transferido. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


Utiliza a combinação de posição de equipamento de transmissão, o estado pedal brake e velocidade do vehicle para detetar comportamentos despertar reckless/agressiva com base no braking padrão de alta velocidade. 

Assim que o pipeline é executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento sob o contentor "connectedcar".

![Saída de AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 

*Figura 19 – AggressiveDrivingPatternPipeline saída*

**Padrão de despertar eficiente Fuel**

A vehicle particionada sinais e os dados de diagnóstico são processados no pipeline com o nome "FuelEfficientDrivingPatternPipeline". Ramo de registo é utilizado para determinar os modelos, localização, vehicle, condições despertar e outras propriedades que apresentem um padrão de despertar eficiente fuel.

![Padrão de despertar Fuel-Efficient](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figura 20 – Fuel-efficient despertar fluxo de trabalho do padrão*

***Fuel eficiente despertar padrão consulta do Hive***

O script de ramo de registo com o nome "fuelefficientdriving.hql" utilizado para analisar o padrão de condição despertar agressiva esteja localizado na pasta "\demo\src\connectedcar\scripts" zip transferido. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


Utiliza a combinação da posição de equipamento de transmissão do vehicle, estado pedal brake, velocidade e acelerador pedal posição para detetar fuel eficiente despertar comportamento, com base no aceleração, braking e velocidade de padrões. 

Assim que o pipeline é executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento sob o contentor "connectedcar".

![Saída de FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figura 21 – FuelEfficientDrivingPatternPipeline saída*

**Recuperar Predições**

A experimentação do machine learning é aprovisionada e publicada como um serviço web como parte da implementação da solução. O ponto final de classificação de lote é utilizado este fluxo de trabalho, registado como um serviço ligado de fábrica de dados e operacionalizado utilizando a atividade de classificação de lote de fábrica de dados.

![Ponto final do Machine Learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

*Figura 22 – do Machine learning endpoint registado como um serviço ligado no factory de dados*

O serviço ligado registado é utilizado a DetectAnomalyPipeline para pontuar os dados utilizando o modelo de deteção de anomalias. 

![Atividade de classificação fábrica de dados de lote de aprendizagem do computador](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png) 

*Figura 23 – atividade do Azure Machine Learning a classificação de lote na fábrica de dados* 

Existem alguns passos neste pipeline de preparação de dados, de modo a que pode ser operationalized com o serviço web de classificação de lote. 

![DetectAnomalyPipeline para prever veículos exigir recalls](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figura 24 – DetectAnomalyPipeline para prever veículos exigir recalls* 

***Consulta de ramo de registo de deteção de anomalias***

Depois da classificação é concluída, uma atividade do HDInsight é utilizada para processar e agregar os dados que são categorizada como anomalias pelo modelo de com uma pontuação de probabilidade de 0.60 ou superior.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Assim que o pipeline é executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento sob o contentor "connectedcar".

![Saída de DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*Figura 25 – DetectAnomalyPipeline saída*

## <a name="publish"></a>Publicar

### <a name="real-time-analysis"></a>Análise em tempo real
Uma das consultas na tarefa de Stream Analytics publica os eventos à saída de uma instância do Hub de eventos. 

![Tarefa do Stream Analytics publica à saída de uma instância do Hub de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figura 26 – Stream Analytics tarefa publica à saída de uma instância do Hub de eventos*

![Consulta de análise de fluxo para publicar o resultado da instância do Hub de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figura 27 – consulta do Stream Analytics para publicar o resultado da instância do Hub de eventos*

Este fluxo de eventos é consumido pelo RealTimeDashboardApp incluído na solução. Esta aplicação tira partido do serviço web Machine Learning resposta-pedido para classificação em tempo real e publica os dados resultantes para um conjunto de dados do Power BI para consumo. 

### <a name="batch-analysis"></a>Análise de lote
Os resultados do batch e processamento em tempo real são publicados as tabelas de SQL Database do Azure para consumo. O servidor de SQL do Azure, base de dados e tabelas são criadas automaticamente como parte do script de configuração. 

![Cópia de resultados ao fluxo de trabalho do data mart de processamento em lote](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figura 28 – processamento cópia de resultados ao fluxo de trabalho do data mart em lote*

![Tarefa do Stream Analytics publica mart de dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figura 29 – Stream Analytics tarefa publica mart de dados*

![Definição de mart de dados na tarefa de Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figura 30 – Data mart na tarefa de Stream Analytics*

## <a name="consume"></a>Consumir
Power BI fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva. 

Clique aqui para obter instruções detalhadas sobre como configurar os relatórios do Power BI e o dashboard. O dashboard final tem o seguinte aspeto:

![Dashboard do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

*Figura 31 - Dashboard do Power BI*

## <a name="summary"></a>Resumo
Este documento contém uma desagregação detalhada da solução de análise de telemetria Vehicle. Isto showcases um padrão de arquitetura de lambda para em tempo real e a análise com predições e ações do batch. Este padrão aplica-se para uma vasta gama de casos de utilização que necessitam de acesso frequente caminho (tempo real) e a análise de caminho típico (batch). 

