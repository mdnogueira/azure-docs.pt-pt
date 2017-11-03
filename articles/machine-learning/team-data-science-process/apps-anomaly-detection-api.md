---
title: "Do Azure Machine Learning a deteção de anomalias API | Microsoft Docs"
description: "API de deteção de anomalias é um exemplo criado com o Microsoft Azure Machine Learning que Deteta anomalias nos dados de séries de tempo com valores de numérico que têm o espaçamento uniformemente no tempo."
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok;rotimpe
ms.openlocfilehash: cd7dab8514b41d930d01fd134229cc9da48b18fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning a deteção de anomalias API
## <a name="overview"></a>Descrição geral
[API de deteção de anomalias](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) é um exemplo criado com o Azure Machine Learning que Deteta anomalias nos dados de séries de tempo com valores de numérico que têm o espaçamento uniformemente no tempo.

Esta API pode detetar os seguintes tipos de anómalos padrões nos dados de séries de tempo:

* **As tendências positivos e negativos**: por exemplo, quando a monitorização de utilização de memória em informática uma tendência ascendente poderá de interesse enquanto poderá ser indicativa de uma fuga de memória
* **As alterações no intervalo de valores dinâmica**: por exemplo, quando a monitorização de exceções acionadas por um serviço em nuvem, quaisquer alterações no intervalo de valores dinâmica pode indicar instabilidade no estado de funcionamento do serviço, e
* **Picos e Dips**: por exemplo, ao monitorizar o número de falhas de início de sessão num serviço ou o número de checkouts um site de comércio eletrónico, picos ou dips pode indicar comportamento anormal.

Estes detetores de aprendizagem máquina controlam essas alterações valores ao longo do tempo e o relatório de alterações contínuas nos respetivos valores como pontuações de anomalias. Não necessitam de otimização de limiar adhoc e as pontuações das classificações podem ser utilizadas para controlar falsa taxa positiva. A deteção de anomalias API é útil em vários cenários, como o serviço de monitorização pelo controlo KPIs ao longo do tempo, a monitorização de utilização através de métricas, tais como o número de procura, número de cliques, monitorização do desempenho através dos contadores, como memória, CPU, lê o ficheiro, etc. ao longo do tempo.

A oferta de deteção de anomalias inclui ferramentas úteis para ajudar a começar.

* O [aplicação web](http://anomalydetection-aml.azurewebsites.net/) ajuda-o a avaliar e visualizar os resultados da deteção de anomalias APIs nos seus dados.

> [!NOTE]
> Tente **soluções de TI anomalias Insights** com tecnologia [esta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
> 
> Para obter esta solução ponto a ponto implementada a sua subscrição do Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **comece por aqui >**</a>
> 
>

## <a name="api-deployment"></a>Implementação de API
Para utilizar a API, terá de a implementar a sua subscrição do Azure onde será alojado como um serviço web do Azure Machine Learning.  Pode fazê-do [galeria da Cortana Intelligence](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Isto irá implementar dois serviços Web do AzureML (e os respetivos recursos relacionados) à sua subscrição do Azure - uma para deteção de anomalias com deteção de sazonalidade e um sem deteção de sazonalidade.  Depois de concluída a implementação, poderá gerir as suas APIs do [serviços web do AzureML](https://services.azureml.net/webservices/) página.  Nesta página, poderá encontrar as localizações de ponto final, chaves de API, bem como código de exemplo para chamar a API.  Estão disponíveis instruções mais detalhadas [aqui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>A API de dimensionamento
Por predefinição, a sua implementação terá um plano de faturação do programador/teste gratuito que inclui 1.000 transações/mês e horas de computação 2/mês.  Pode atualizar para outro plano de acordo com as suas necessidades.  Estão disponíveis detalhes sobre os preços dos planos diferentes [aqui](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) em "Preços da API de Web de produção".

## <a name="managing-aml-plans"></a>Gerir AML planos 
Pode gerir o seu plano de faturação [aqui](https://services.azureml.net/plans/).  O nome do plano será baseado no nome de grupo de recursos que escolheu durante a implementação da API, mais uma cadeia que é exclusiva para a sua subscrição.  Estão disponíveis instruções sobre como atualizar o seu plano [aqui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-manage-new-webservice) na secção "Gerir planos de faturação".

## <a name="api-definition"></a>Definição da API
O serviço web fornece uma API baseada em REST através de HTTPS, que pode ser utilizada de diversas formas, incluindo um web ou aplicação móvel, R, Python, Excel, etc.  Enviar os dados de séries de tempo para este serviço através de uma chamada de REST API e é executada uma combinação de tipos de três anomalias descrito abaixo.

## <a name="calling-the-api"></a>A chamar a API
Para chamar a API, terá de conhecer a localização de ponto final e a chave de API.  Ambos, juntamente com o código de exemplo para chamar a API, estão disponíveis a partir de [serviços web do AzureML](https://services.azureml.net/webservices/) página.  Navegue para a API pretendida e, em seguida, clique no separador de "Consume" localizá-las.  Tenha em atenção que pode chamar a API como uma API do Swagger (ou seja, com o parâmetro de URL `format=swagger`) ou como um não - API do Swagger (ou seja, sem o `format` parâmetro de URL).  O código de exemplo utiliza o formato de Swagger.  Segue-se um exemplo de pedido e resposta no formato Swagger não.  Estes exemplos são para o ponto final de sazonalidade.  O ponto final não sazonalidade é semelhante.

### <a name="sample-request-body"></a>Corpo do pedido de exemplo
O pedido contém dois objetos: `Inputs` e `GlobalParameters`.  O pedido de exemplo abaixo, alguns parâmetros são enviados explicitamente enquanto outros não estão (desloque para baixo para uma lista completa de parâmetros para cada ponto final).  Os parâmetros que não são enviados explicitamente no pedido irão utilizar os valores predefinidos indicados abaixo.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Pedido de Resposta
Tenha em atenção que, para ver o `ColumnNames` campo, tem de incluir `details=true` como um parâmetro de URL do seu pedido.  Consulte as tabelas abaixo para o significado atrás de cada um destes campos.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>API de pontuação
A API de pontuação é utilizada para executar a deteção de anomalias nos dados de séries de tempo não sazonais. A API é executado um número de detetores anomalias nos dados e devolve os respetivos pontuações de anomalias. A figura abaixo mostra um exemplo de anomalias pode detetar a API de pontuação. Esta série de tempo tem alterações de nível distintas 2 e 3 picos. Os pontos vermelhos mostram a hora em que a alteração do nível é detetada, enquanto os pontos preto mostram os picos de detetado.
![API de pontuação][1]

### <a name="detectors"></a>Detetores
A deteção de anomalias API suporta detetores 3 amplas categorias. Detalhes sobre os parâmetros de entrada específicos e saídas para cada detector podem ser encontrados na seguinte tabela.

| Categoria de detector | Detector | Descrição | Parâmetros de entrada | saídas |
| --- | --- | --- | --- | --- |
| Detetores de pico de pedidos |TSpike Detector |Detetar picos e dips com base na proximidade os valores são da primeira e terceira quartiles |*tspikedetector.Sensitivity:* assume o valor de número inteiro no intervalo predefinido de 1 a 10: 3; Valores superiores vai detetar mais extremos valores, por conseguinte, facilitando a menos confidencial |TSpike: valores binários-1 se for detetado um pico pedidos/dip, '0', caso contrário |
| Detetores de pico de pedidos | ZSpike Detector |Detetar picos e dips com base para datapoints pertencem à respetiva média |*zspikedetector.Sensitivity:* assumir o valor de número inteiro no intervalo predefinido de 1 a 10: 3; Valores superiores vai detetar mais extremos valores tornando menos confidencial |ZSpike: valores binários-1 se for detetado um pico pedidos/dip, '0', caso contrário | |
| Tendência lenta Detector |Tendência lenta Detector |Detetar lenta tendência positiva de acordo com a sensibilidade de conjunto |*trenddetector.Sensitivity:* limiar de pontuação de detector (predefinição: 3.25, 3.25 – 5 é um intervalo razoável para selecionar esta do; Quanto maior for a menos confidencial) |tscore: número de vírgula flutuante que representa a pontuação de anomalias no tendência |
| Detetores de nível de alteração | Detector de alteração do nível de bidirecional |Detetar alteração nível, ascendente e descendente, de acordo com a sensibilidade de conjunto |*bileveldetector.Sensitivity:* limiar de pontuação de detector (predefinição: 3.25, 3.25 – 5 é um intervalo razoável para selecionar esta do; Quanto maior for a menos confidencial) |rpscore: número de vírgula flutuante que representa a pontuação de anomalias na alteração do nível ascendente e descendente | |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre estes parâmetros de entrada estão listadas na tabela abaixo:

| Parâmetros de entrada | Descrição | Definição predefinida | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Histórico de (em n. º de pontos de dados) utilizado para o cálculo de pontuação de anomalias |500 |número inteiro |10-2000 |Dependente de séries de tempo |
| detectors.spikesdips | Se pretende detetar apenas os picos de apenas dips ou ambos |Ambos |enumerar |Ambos, os picos de Dips |Ambos |
| bileveldetector.Sensitivity |Sensibilidade de nível de bidirecional alterar detector. |3.25 |duplo |Nenhuma |3.25-5 (valores menores significam mais confidencial) |
| trenddetector.Sensitivity |Sensibilidade para detector tendência positivo. |3.25 |duplo |Nenhuma |3.25-5 (valores menores significam mais confidencial) |
| tspikedetector.Sensitivity |Sensibilidade para TSpike Detector |3 |número inteiro |1-10 |3 a 5 (valores menores significam mais confidencial) |
| zspikedetector.Sensitivity |Sensibilidade para ZSpike Detector |3 |número inteiro |1-10 |3 a 5 (valores menores significam mais confidencial) |
| postprocess.tailRows |Número de pontos de dados mais recentes que serão guardadas nos resultados de saída |0 |número inteiro |0 (manter todos os pontos de dados), ou especifique o número de pontos a manter nos resultados |N/D |

### <a name="output"></a>Saída
A API é executado todos os detetores nos seus dados de séries de tempo e devolve pontuações de anomalias e indicadores de pico de pedidos de binário para cada ponto no tempo. A tabela abaixo lista as saídas da API. 

| saídas | Descrição |
| --- | --- |
| Hora |Carimbos de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| Dados |Os valores de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| TSpike |Indicador de binário para indicar se um pico de pedidos é detetado pelo TSpike Detector |
| ZSpike |Indicador de binário para indicar se um pico de pedidos é detetado pelo ZSpike Detector |
| rpscore |Uma número anomalias de representing flutuante Pontuar em alterações de nível de bidirecional |
| rpalert |alterar o valor de 1/0 que indica que não há um nível de bidirecional anomalias com base na sensibilidade a entrada |
| tscore |Uma número anomalias de representing flutuante Pontuar no tendência positiva |
| talert |valor de 1/0 que indica que existe é uma anomalias de tendência positivos com base na sensibilidade a entrada |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
A API de ScoreWithSeasonality é utilizada para executar a deteção de anomalias em série de tempo que possuem padrões sazonais. Esta API é útil para detetar desvios em padrões sazonais.  
A figura seguinte mostra um exemplo de anomalias detectada uma série de tempo sazonais. A série de tempo tem um pico de pedidos (a 1ª preto ponto final), dois dips (o ponto de preto 2nd e um no fim) e uma alteração ao nível (ponto vermelho). Tenha em atenção que tanto o dip meio de série de tempo e a alteração do nível só estão discernable depois sazonais componentes são removidos da série.
![Sazonalidade API][2]

### <a name="detectors"></a>Detetores
Os detetores no ponto final sazonalidade são semelhantes aos que estão no ponto final não sazonalidade, mas com ligeiramente diferentes nomes de parâmetros (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre estes parâmetros de entrada estão listadas na tabela abaixo:

| Parâmetros de entrada | Descrição | Definição predefinida | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |O intervalo de agregação em segundos para agregar séries de tempo de entrada |0 (nenhum agregação é efetuada) |número inteiro |0: Ignorar caso contrário, a agregação ', ' > 0 |5 minutos para 1 dia, dependentes de séries de tempo |
| preprocess.aggregationFunc |Função utilizada para agregar dados para o AggregationInterval especificado |média |enumerar |soma, média, comprimento |N/D |
| preprocess.replaceMissing |Valores utilizados para impute dados em falta |lkv (última conhecido valor) |enumerar |zero, lkv, média |N/D |
| detectors.historyWindow |Histórico de (em n. º de pontos de dados) utilizado para o cálculo de pontuação de anomalias |500 |número inteiro |10-2000 |Dependente de séries de tempo |
| detectors.spikesdips | Se pretende detetar apenas os picos de apenas dips ou ambos |Ambos |enumerar |Ambos, os picos de Dips |Ambos |
| bileveldetector.Sensitivity |Sensibilidade de nível de bidirecional alterar detector. |3.25 |duplo |Nenhuma |3.25-5 (valores menores significam mais confidencial) |
| postrenddetector.Sensitivity |Sensibilidade para detector tendência positivo. |3.25 |duplo |Nenhuma |3.25-5 (valores menores significam mais confidencial) |
| negtrenddetector.Sensitivity |Sensibilidade para detector tendência negativo. |3.25 |duplo |Nenhuma |3.25-5 (valores menores significam mais confidencial) |
| tspikedetector.Sensitivity |Sensibilidade para TSpike Detector |3 |número inteiro |1-10 |3 a 5 (valores menores significam mais confidencial) |
| zspikedetector.Sensitivity |Sensibilidade para ZSpike Detector |3 |número inteiro |1-10 |3 a 5 (valores menores significam mais confidencial) |
| seasonality.Enable |Se a análise de sazonalidade é para ser executada |VERDADEIRO |Valor booleano |TRUE, false |Dependente de séries de tempo |
| seasonality.numSeasonality |Número máximo de ciclos periódicos para ser detetada |1 |número inteiro |1, 2 |1-2 |
| seasonality.Transform |Se sazonais (e) componentes de tendência deverá ser removidos antes de aplicar a deteção de anomalias |deseason |enumerar |Nenhum, deseason, deseasontrend |N/D |
| postprocess.tailRows |Número de pontos de dados mais recentes que serão guardadas nos resultados de saída |0 |número inteiro |0 (manter todos os pontos de dados), ou especifique o número de pontos a manter nos resultados |N/D |

### <a name="output"></a>Saída
A API é executado todos os detetores nos seus dados de séries de tempo e devolve pontuações de anomalias e indicadores de pico de pedidos de binário para cada ponto no tempo. A tabela abaixo lista as saídas da API. 

| saídas | Descrição |
| --- | --- |
| Hora |Carimbos de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| OriginalData |Os valores de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| ProcessedData |Um dos seguintes: <ul><li>Sazonalmente ajustado séries de tempo se sazonalidade significativa foi detetado e deseason opção selecionada;</li><li>sazonalmente ajustadas e detrended séries de tempo, quando tenha sido detetada sazonalidade significativa e deseasontrend opção selecionada</li><li>caso contrário, este é o mesmo que OriginalData</li> |
| TSpike |Indicador de binário para indicar se um pico de pedidos é detetado pelo TSpike Detector |
| ZSpike |Indicador de binário para indicar se um pico de pedidos é detetado pelo ZSpike Detector |
| BiLevelChangeScore |Uma número anomalias de representing flutuante Pontuar no nível de alterações |
| BiLevelChangeAlert |1/0 valor que indica que existe é uma anomalias de nível de alteração com base na sensibilidade a entrada |
| PosTrendScore |Uma número anomalias de representing flutuante Pontuar no tendência positiva |
| PosTrendAlert |valor de 1/0 que indica que existe é uma anomalias de tendência positivos com base na sensibilidade a entrada |
| NegTrendScore |Uma número anomalias de representing flutuante Pontuar no tendência negativa |
| NegTrendAlert |valor de 1/0 que indica que existe é uma anomalias de tendência negativo com base na sensibilidade a entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

