---
title: "Uma descrição profunda como prever o estado de funcionamento vehicle e ocasionar hábitos - Azure | Microsoft Docs"
description: "Utilizar as capacidades do Cortana Intelligence para obter informações acerca de preditiva e em tempo real em estado de funcionamento vehicle e ocasionar hábitos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev
ms.openlocfilehash: a21316ef6ab05918f07a09243b5ce04950ecd9dc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Manual de comunicação social vehicle solução de análise de telemetria: avançada aprofundar a solução
Hiperligações este menu a secções deste manual de comunicação social: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Este documento aprofunda em cada uma das fases descritas na arquitetura da solução. As instruções e ponteiros para personalização estão incluídos. 

## <a name="data-sources"></a>Origens de dados
A solução utiliza duas origens de dados diferentes:

* Vehicle simulada sinais e conjunto de dados de diagnóstico
* Catálogo de vehicle

Um simulador de telematics vehicle é incluído como parte desta solução, conforme mostrado na captura de ecrã seguinte. -Emite informações de diagnóstico e sinais que correspondem ao estado do veículo e o padrão despertar num determinado ponto no tempo. Para transferir o Vehicle Telematics simulador solução do Visual Studio para personalizações com base nos seus requisitos, visite o [simulador de telematics Vehicle](http://go.microsoft.com/fwlink/?LinkId=717075) página Web. O catálogo de vehicle contém um conjunto de dados de referência que mapeia os números de identificação vehicle (VINs) para modelos.

![Simulador de telematics vehicle](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Este conjunto de dados formatado em JSON contém o esquema seguinte.

| Coluna | Descrição | Valores |
| --- | --- | --- |
| VIN |VIN gerado aleatoriamente |Obtidos a partir de uma lista de 10 000 VINs gerados aleatoriamente principal |
| Temperatura externa |A temperatura externa onde está a ocasionar o veículo |Número gerado aleatoriamente entre 0 e 100 |
| Temperatura do motor |A temperatura de motor do veículo |Número gerado aleatoriamente entre 0 e 500 |
| Velocidade |A velocidade do motor no qual está a ocasionar ao veículo |Número gerado aleatoriamente entre 0 e 100 |
| Fuel |O nível de fuel do veículo |Gerado aleatoriamente o número de 0 a 100 (indica a percentagem de nível de fuel) |
| EngineOil |O nível de petróleo do motor do veículo |Gerado aleatoriamente o número de 0 a 100 (indica a percentagem de nível de petróleo motor) |
| Tire pressão |A pressão de tire do veículo |Gerado aleatoriamente o número de 0 a 50 (indica a percentagem de nível de pressão tire) |
| Odometer |A leitura de odometer do veículo |Número gerado aleatoriamente entre 0 e inferior 200.000 |
| Accelerator_pedal_position |A posição pedal acelerador do veículo |Gerado aleatoriamente o número de 0 a 100 (indica a percentagem de nível de acelerador) |
| Parking_brake_status |Indica se o veículo é parqueado ou não |TRUE ou False |
| Headlamp_status |Indica se o headlamp é sobre ou não |TRUE ou False |
| Brake_pedal_status |Indica se o pedal brake é premido ou não |TRUE ou False |
| Transmission_gear_position |A posição de equipamento de transmissão do veículo |Estados: primeiro, segundo, terceiros, fourth, fifth, sixth, seventh, eighth |
| Ignition_status |Indica se o veículo está em execução ou parado |TRUE ou False |
| Windshield_wiper_status |Indica se o wiper windshield está ativada ou não |TRUE ou False |
| ABS |Indica se ABS é parte ou não |TRUE ou False |
| Carimbo de data/hora |O carimbo de hora quando o ponto de dados é criado |Data |
| Cidade |A localização do veículo |Quatro cidades nesta solução: Bellevue, Redmond, Sammamish, Seattle |

O conjunto de dados de referência de modelo vehicle mapeia VINs para modelos. 

| VIN | Modelo |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedan |
| 8J0U8XCPRGW4Z3NQE |Híbrido |
| WORG68Z2PLTNZDBI7 |Família saloon |
| JTHMYHQTEPP4WBMRN |Sedan |
| W9FTHG27LZN1YWO0Y |Híbrido |
| MHTP9N792PHK08WJM |Família saloon |
| EI4QXI2AXVQQING4I |Sedan |
| 5KKR2VB4WHQH97PF8 |Híbrido |
| W9NSZ423XZHAONYXB |Família saloon |
| 26WJSGHX4MA5ROHNL |Convertíveis |
| GHLUB6ONKMOSI7E77 |Estação wagon |
| 9C2RHVRVLMEJDBXLP |Carro compacto |
| BRNHVMZOUJ6EOCP32 |SUV pequeno |
| VCYVW0WUZNBTM594J |Desporto carro |
| HNVCE6YFZSA5M82NY |SUV médio |
| 4R30FOR7NUOBL05GJ |Estação wagon |
| WYNIIY42VKV6OQS1J |SUV grande |
| 8Y5QKG27QET1RBK7I |SUV grande |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Sedan |
| M4TV6IEALD5QDS3IR |Híbrido |
| VHRA1Y2TGTA84F00H |Família saloon |
| R0JAUHT1L1R3BIKI0 |Sedan |
| 9230C202Z60XX84AU |Híbrido |
| T8DNDN5UDCWL7M72H |Família saloon |
| 4WPYRUZII5YV7YA42 |Sedan |
| D1ZVY26UV2BFGHZNO |Híbrido |
| XUF99EW9OIQOMV7Q7 |Família saloon |
| 8OMCL3LGI7XNCC21U |Convertíveis |
| ……. | |

## <a name="ingestion"></a>Ingestão
Combinações de Event Hubs do Azure, Azure Stream Analytics e do Azure Data Factory são utilizadas para a ingestão de sinais vehicle, os eventos de diagnóstico e em tempo real e análise de lote. Todos estes componentes são criados e configurados como parte da implementação da solução. 

### <a name="real-time-analysis"></a>Análise em tempo real
Os eventos gerados pelo simulador de telematics vehicle são publicados para o hub de eventos utilizando o SDK do hub de eventos.  

![Dashboard de hub de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

A tarefa de Stream Analytics ingere estes eventos a partir do event hub e processa os dados em tempo real para analisar o estado de funcionamento vehicle.

![Fluxo de dados de processamento da tarefa de análise](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


A tarefa de Stream Analytics:

* Ingere dados do hub de eventos.
* Efetua uma junção com os dados de referência para mapear o veículo VIN para o modelo correspondente. 
* Mantém-los para o armazenamento de Blobs do Azure para análise de lote avançado. 

A seguinte consulta do Stream Analytics é utilizada para manter os dados para o armazenamento de BLOBs: 

![Consulta de tarefa do Stream Analytics para ingestão de dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Análise de lote
Um volume de sinais vehicle simulada e conjunto de dados de diagnóstico adicional também é gerado para análise de lote mais rico. Este volume adicional é necessário para assegurar um volume de dados representativo para processamento de batches. Para esta finalidade, PrepareSampleDataPipeline é utilizado o fluxo de trabalho do Data Factory para gerar visão de um ano de sinais vehicle simulada e conjunto de dados de diagnóstico. Para transferir a atividade de .NET personalizada da fábrica de dados solução do Visual Studio para personalizações com base nos seus requisitos, visite o [atividade personalizada do Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) página Web. 

Este fluxo de trabalho mostra preparados para processamento em lote de dados de exemplo.

![Preparado para o fluxo de trabalho do batch processamento de dados de exemplo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


O pipeline é composta por uma atividade personalizada do .NET de fábrica de dados.

![Atividade de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Depois do pipeline é executada com êxito, e o conjunto de dados RawCarEventsTable está marcada como "Pronto", compreendendo de um ano de sinais vehicle simulado e os dados de diagnóstico são produzidos. Consulte a seguinte pasta e ficheiro criado na sua conta de armazenamento no recipiente do connectedcar:

![Saída de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>O conjunto de dados de partição
No passo de preparação de dados, o conjunto de dados de diagnóstico e sinais de vehicle semiestruturados não processados são partições para um formato de ano/mês. A criação de partições promove consulta mais eficiente e de armazenamento a longo prazo dimensionável Ativando a ativação pós-falha de falhas. Por exemplo, como a primeira conta do blob é preenchida,-falhas durante a seguinte conta. 

>[!NOTE] 
>Este passo na solução aplica-se apenas ao processamento de batches.

Entrada e saída gestão de dados:

* **Dados de saída** (PartitionedCarEventsTable identificado) é mantida por um longo período de tempo, como o formulário fundamentais sobre / "rawest" dos dados no lake de dados do cliente. 
* **Dados de entrada** para este pipeline normalmente rejeitados porque os dados de saída tem fidelidade completa para a entrada. Este é armazenado (particionada) melhor para uma utilização posterior.

O partição carro eventos fluxo de trabalho.

![Fluxo de trabalho de eventos de carro de partição](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Os dados não processados particionados com uma atividade do ramo de registo do Azure HDInsight no PartitionCarEventsPipeline, conforme mostrado na captura de ecrã seguinte. Os dados de exemplo gerados para um ano num passo de preparação de dados estão particionados ano/mês. As partições são utilizadas para gerar vehicle sinais e dados de diagnóstico de cada mês (total de 12 partições) de um ano. 

![Atividade de PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Script de ramo de registo PartitionConnectedCarEvents**

O partitioncarevents.hql de script de ramo de registo é utilizado para criação de partições. Está localizado na pasta \demo\src\connectedcar\scripts do ficheiro zip transferido. 
    
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

Depois do pipeline executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento no recipiente do connectedcar:

![Saída particionada](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Os dados agora são otimizados, mais fácil de gerir e pronto para processamento adicional para obter informações acerca do batch avançado. 

## <a name="data-analysis"></a>Análise de dados
Nesta secção, pode ver como combinar o Stream Analytics, o Azure Machine Learning, o Data Factory e o HDInsight para avançada análise avançada no estado de funcionamento vehicle e ocasionar hábitos.

### <a name="machine-learning"></a>Machine learning
O objetivo aqui é para prever veículos que necessitam de manutenção ou devolução de chamada com base em certas estatísticas de estado de funcionamento, com base em pressupostos os seguintes:

* Se um dos três seguintes condições for VERDADEIRO, os veículos necessitam de manutenção de manutenção:
  
  * A pressão de tire é baixa.
  * O nível de petróleo motor é baixa.
  * A temperatura de motor é elevada.

* Se uma das seguintes condições for verdadeira, os veículos poderão ter um problema de segurança e requerer a devolução de chamada:
  
  * A temperatura de motor é elevada, mas a temperatura externa é baixa.
  * A temperatura de motor é baixa, mas a temperatura externa é elevada.

Com base nos requisitos anteriores, dois modelos separados detetam anomalias. É um modelo para a deteção de manutenção vehicle, não sendo um modelo para a deteção de devolução de chamada de vehicle. Em ambos os modelos, o algoritmo de análise (PCA) incorporadas componente principal é utilizado para deteção de anomalias. 

#### <a name="maintenance-detection-model"></a>**Modelo de deteção de manutenção**

Se um de três indicadores – tire pressão, petróleo motor ou temperatura motor – satisfaça a condição correspondentes, o modelo de deteção de manutenção relatórios uma anomalias. Como resultado, estes três variáveis tem de considerar na criação do modelo. Na experimentação no machine learning, o **selecionar colunas no conjunto de dados** módulo é utilizado para extrair estas três variáveis. Em seguida, o módulo de deteção de anomalias baseado em PCA é utilizado para criar o modelo de deteção de anomalias. 

PCA é uma técnica estabelecida no machine learning que pode ser aplicado a seleção de funcionalidades, classificação e deteção de anomalias. PCA converte um conjunto de cenários que contêm variáveis correlacionadas possivelmente para um conjunto de valores denominados componentes principais. A chave ideia de modelação baseado em PCA é dados de projeto para um espaço inferior dimensional para identificar mais facilmente as funcionalidades e anomalias.

Para cada entrada nova para o modelo de deteção, o detector anomalias calcula primeiro sua projecção do eigenvectors. Em seguida, calcula o erro reconstruction normalizado. Este erro normalizado é a classificação de anomalias: quanto maior for o erro, mais anómalos a instância. 

No problema de deteção de manutenção, cada registo é considerado como um ponto num espaço three-dimensional definido pelos tire pressão, petróleo motor e a temperatura de motor coordenadas. Para capturar estes anomalias, PCA é utilizado para projetar os dados originais no espaço de three-dimensional para um espaço two-dimensional. Assim, o número de parâmetro de componentes para utilizar em PCA está definido para os dois. Este parâmetro desempenha um papel importante na aplicação de deteção de anomalias baseado em PCA. Depois de utilizar PCA para dados de projeto, estes anomalias são identificadas mais facilmente.

#### <a name="recall-anomaly-detection-model"></a>**Recuperar o modelo de deteção de anomalias**

No modelo de deteção de anomalias de devolução de chamada, o **selecionar colunas no conjunto de dados** e módulos de deteção de anomalias baseado em PCA são utilizados de forma semelhante. Especificamente, três variáveis – motor de temperatura, fora temperatura e velocidade – são extraídos pela primeira vez ao utilizar o **selecionar colunas no conjunto de dados** módulo. A variável de velocidade também está incluída, porque a temperatura de motor normalmente está correlacionada com a velocidade. Em seguida, o módulo de deteção de anomalias baseado em PCA é utilizado para projetar os dados a partir do espaço de three-dimensional para um espaço two-dimensional. Os critérios de devolução de chamada são satisfeitos. O veículo requer devolução de chamada quando a temperatura de motor e a temperatura externa são altamente negativamente correlacionados. Depois de PCA for executado, o algoritmo de deteção de anomalias baseado em PCA é utilizado para captar as anomalias. 

Quando se prepara o modelo, os dados normais são utilizados como os dados de entrada para preparar o modelo de deteção de anomalias baseado em PCA. (Dados normais não necessitam de manutenção ou devolução de chamada.) A experimentação de classificação, o modelo de deteção de anomalias treinado é utilizado para detetar se o veículo requer manutenção ou devolução de chamada. 

### <a name="real-time-analysis"></a>Análise em tempo real
A seguinte consulta SQL de análise de fluxo é utilizada para aceder a média de todos os parâmetros de vehicle importantes. Estes parâmetros incluem velocidade vehicle, nível fuel, temperatura motor, leitura odometer, tire pressão, ao nível do motor petróleo e outras pessoas. As médias são utilizadas para detetar anomalias, emitir alertas e determinar as condições de estado de funcionamento geral de veículos operados numa região específica. As médias, em seguida, são correlacionadas com demografia. 

![Consulta de análise de fluxo para o processamento em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Todos os médias são calculadas numa janela em cascata de três segundo. Uma janela em cascata é utilizada porque intervalos de tempo sem sobreposição e contíguo são necessários. 

Para saber mais sobre as funcionalidades em modo de janela no Stream Analytics, consulte [modos de janela (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Predição em tempo real**

Uma aplicação é incluída como parte da solução para operacionalizar o modelo de machine learning em tempo real. A aplicação RealTimeDashboardApp é criada e configurada como parte da implementação da solução. A aplicação:

* Escuta a uma instância do hub de eventos onde Stream Analytics publica eventos num padrão continuamente.

    ![Consulta de análise para publicar os dados de sequência](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Recebe eventos. Para cada evento que recebe esta aplicação: 
   
   * Os dados são processados através da utilização de uma ponto de final pedido-resposta classificação (RRS) de aprendizagem. O ponto final RRS é publicado automaticamente como parte da implementação.
   * A saída RRS é publicada para um conjunto de dados do Power BI utilizando o push de APIs.

Este padrão também é aplicável para cenários em que pretende integrar uma aplicação de linha de negócio com o fluxo de análise em tempo real. Estes cenários incluem alertas, notificações e mensagens.

Para transferir a solução de RealtimeDashboardApp Visual Studio para personalizações, consulte o [RealtimeDashboardApp transferência](http://go.microsoft.com/fwlink/?LinkId=717078) página Web. 

#### <a name="execute-the-real-time-dashboard-application"></a>**Executar a aplicação de dashboard em tempo real**
1. Extraia o RealtimeDashboardApp e guarde-o localmente.

    ![Pasta RealTimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Execute a aplicação RealtimeDashboardApp.exe.

3. Introduza as credenciais válidas do Power BI e selecione **sessão**.  

    ![Janela de início de sessão da aplicação de dashboard em tempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Selecione **aceitar**.

    ![Dashboard em tempo real aplicação janela final da sessão](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Se pretender remover o conjunto de dados do Power BI da cache, execute RealtimeDashboardApp com o parâmetro de "flushdata". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Análise de lote
O objetivo aqui é para mostrar como Contoso Motors utiliza as capacidades de computação do Azure para o conjunto de testes macrodados. Estes dados revela informações valiosas nos padrões de despertar, o comportamento de utilização e o estado de funcionamento vehicle. Estas informações possibilita a:

* Melhorar a experiência do cliente e torná-lo mais barata fornecendo informações em ocasionar hábitos e comportamentos despertar fuel-efficient.
* Saiba mais proativamente sobre os clientes e os seus padrões de despertar governar as decisões de negócio e fornecer a melhor na classe produtos e serviços.

Nesta solução, destinam-se as métricas seguintes:

* **Impulsionar comportamento agressiva**: identifica a tendência dos modelos, localizações, despertar condições e tempo do ano para obter informações sobre os padrões de despertar agressiva. Contoso Motors pode utilizar estas informações para campanhas de marketing para introduzir novas funcionalidades personalizadas e insurance baseada na utilização.
* **Comportamento de despertar Fuel-Efficient**: identifica a tendência dos modelos, localizações, despertar condições e tempo do ano para obter informações sobre os padrões de despertar fuel-efficient. Contoso Motors pode utilizar estas informações para campanhas de marketing para introduzir novas funcionalidades e relatórios proativa para controladores hábitos despertar económicos e compatível com o ambiente.
* **Recuperar modelos**: identifica os modelos que exigem recalls por operacionalizar a anomalias deteção experimentação do machine learning.

Vamos ver para os detalhes de cada uma destas métricas.

#### <a name="aggressive-driving-behavior-patterns"></a>**Agressiva despertar padrões de comportamento**

A vehicle particionada sinais e os dados de diagnóstico são processados em AggresiveDrivingPatternPipeline, conforme mostrado no seguinte fluxo de trabalho. Ramo de registo é utilizado para determinar os modelos, localização, vehicle, condições despertar e outros parâmetros que apresentem agressiva padrões despertar.

![Agressiva despertar fluxo de trabalho do padrão](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Consulta de Hive padrão despertar agressiva***

O aggresivedriving.hql de script de ramo de registo é utilizada para analisar padrões de condição despertar agressiva. Está localizado na pasta \demo\src\connectedcar\scripts do ficheiro zip transferido. 

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


O script utiliza a combinação de posição de equipamento de transmissão, o estado pedal brake e velocidade um vehicle para detetar comportamentos despertar reckless/agressiva com base no braking padrões de alta velocidade. 

Depois do pipeline executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento no recipiente do connectedcar:

![Saída de AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Fuel-Efficient despertar padrões de comportamento**

A vehicle particionada sinais e os dados de diagnóstico são processados em FuelEfficientDrivingPatternPipeline, conforme mostrado no seguinte fluxo de trabalho. Ramo de registo é utilizado para determinar os modelos, localização, vehicle, condições despertar e outras propriedades que apresentem fuel-efficient padrões despertar.

![Padrões despertar Fuel-Efficient](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Consulta de Hive padrão despertar Fuel-Efficient***

O fuelefficientdriving.hql de script de ramo de registo é utilizada para analisar padrões de condição despertar fuel-efficient. Está localizado na pasta \demo\src\connectedcar\scripts do ficheiro zip transferido. 

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


O script utiliza a combinação da posição de equipamento de transmissão de um vehicle, estado pedal brake, velocidade e acelerador pedal posição para detetar comportamento despertar fuel-efficient com base nos aceleração, braking e velocidade de padrões. 

Depois do pipeline executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento no recipiente do connectedcar:

![Saída de FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Recuperar predições do modelo**

A experimentação do machine learning é aprovisionada e publicada como um serviço web como parte da implementação da solução. O ponto final de classificação de lote é utilizado neste fluxo de trabalho. Tem registado como um serviço ligado de fábrica de dados e operacionalizado, utilizando o lote de fábrica de dados da classificação de atividade.

![Ponto final do Machine learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

O serviço ligado registado é utilizado na DetectAnomalyPipeline para pontuar os dados utilizando o modelo de deteção de anomalias. 

![Batch do Machine learning atividade de classificação fábrica de dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Alguns passos são efetuados neste pipeline de preparação de dados, de modo a que pode ser operationalized com o serviço web de classificação de lote. 

![DetectAnomalyPipeline para predição de devolução de chamada](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Consulta de ramo de registo de deteção de anomalias***

Depois de concluída a classificação, uma atividade do HDInsight processa e agrega os dados que o modelo categorizado como anomalias. O modelo utiliza uma pontuação de probabilidade de 0.60 ou superior.

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


Depois do pipeline executado com êxito, consulte as seguintes partições geradas na sua conta de armazenamento no recipiente do connectedcar:

![Saída de DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publicar

### <a name="real-time-analysis"></a>Análise em tempo real
Uma das consultas na tarefa de Stream Analytics publica os eventos de uma instância de hub de eventos de saída. 

![Tarefa do Stream Analytics publicados para uma instância de hub de eventos de saída](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

A seguinte consulta do Stream Analytics é utilizada para publicar para a instância de hub de eventos de saída:

![Consulta de análise de fluxo para publicar para a instância de hub de eventos de saída](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Este fluxo de eventos é consumido pelo RealTimeDashboardApp que está incluído na solução. Esta aplicação utiliza a serviço web de resposta-pedido para classificação em tempo real de aprendizagem. Publica os dados resultantes para um conjunto de dados do Power BI para consumo. 

### <a name="batch-analysis"></a>Análise de lote
Os resultados do batch e processamento em tempo real são publicados em tabelas de SQL Database do Azure para consumo. O SQL server, a base de dados e tabelas são criadas automaticamente como parte do script de configuração. 

Os resultados de processamento de lote são copiados para o fluxo de trabalho do data mart.

![Processamento de resultados copiados para o fluxo de trabalho do data mart em lote](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

A tarefa de Stream Analytics é publicada para o data mart.

![Tarefa do Stream Analytics publicado mart de dados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

O data mart é a tarefa de Stream Analytics.

![Definição de mart de dados na tarefa de Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Consumir
Power BI fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva. 

O dashboard final aspeto neste exemplo:

![Dashboard do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Resumo
Este documento contém uma desagregação detalhada da solução de análise de telemetria Vehicle. O padrão de arquitetura de lambda é utilizado para em tempo real e a análise com predições e ações do batch. Este padrão aplica-se para uma vasta gama de casos de utilização que necessitam de acesso frequente caminho (tempo real) e a análise de caminho típico (batch). 

### <a name="references"></a>Referências

* [Solução do Visual Studio vehicle Telematics simulador](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [SDK de Hubs de eventos do Azure para ingestão da transmissão](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Capacidades de movimento de dados do Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Atividade de .NET de fábrica de dados do Azure](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Atividade de .NET de fábrica de dados do Azure utilizada para preparar os dados de exemplo de solução do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717077) 
