---
title: Utilize pontos finais do Azure Machine Learning no Stream Analytics | Microsoft Docs
description: "Idioma de máquina funções definidas pelo utilizador no Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 406b258f-b8c2-4e55-953c-b7f84e8e5354
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: ca656ce11f267340872f4b8566d0ee21791f29d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Máquina integração de aprendizagem no Stream Analytics
Stream Analytics suporta as funções definidas pelo utilizador que chamar a pontos finais do Azure Machine Learning. Suporte da REST API para esta funcionalidade está detalhado no [biblioteca de API de REST do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações suplementares necessárias para uma implementação efetuada com êxito desta capacidade no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Descrição geral: Terminologia do Azure Machine Learning
Microsoft Azure Machine Learning fornece uma ferramenta de colaboração, de arrastar e largar que pode utilizar para criar, testar e implementar soluções de Análise Preditiva nos seus dados. Esta ferramenta é denominada o *Azure Machine Learning Studio*. O studio é utilizado para interagir com os recursos de Machine Learning e facilmente criar, testar e iterar o design. Estes recursos e as respetivas definições são abaixo.

* **Área de trabalho**: O *área de trabalho* é um contentor que retém todos os outros recursos de Machine Learning em conjunto num contentor de gestão e o controlo.
* **Experimentação**: *experimentações* são criados por cientistas de dados para utilizar conjuntos de dados e dar formação sobre um modelo de machine learning.
* **Ponto final**: *pontos finais* são o objeto do Azure Machine Learning utilizado para utilizar funcionalidades como entrada, aplicar um modelo de aprendizagem máquina especificada e devolver classificada de saída.
* **A classificação de Webservice**: A *classificação webservice* é uma coleção de pontos finais, tal como mencionado acima.

Cada ponto final tem apis para execução de lote e a execução síncrona. Do Stream Analytics utiliza execução síncrona. O serviço específico é denominado um [serviço pedido/resposta](../machine-learning/studio/consume-web-services.md) no studio do AzureML.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning recursos necessários para tarefas do Stream Analytics
Para efeitos de análise de sequência de tarefa de processamento de um ponto final de pedido/resposta, uma [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), e uma definição de swagger são necessária para a execução com êxito. Do Stream Analytics tem um ponto de final adicional que constrói o url para o ponto final de swagger, a interface de procura e devolve uma definição de UDF predefinido para o utilizador.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configure do Stream Analytics e a aprendizagem automática UDF através da REST API
Utilizando REST APIs pode configurar a tarefa para chamar as funções do Azure Machine idioma. Os passos são os seguintes:

1. Criar uma tarefa do Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma função definida pelo utilizador (UDF)
5. Escrever uma transformação de Stream Analytics que chama UDF
6. Iniciar a tarefa

## <a name="creating-a-udf-with-basic-properties"></a>Criar um UDF com propriedades básicas
Por exemplo, o código de exemplo seguinte cria um UDF escalar denominado *newudf* que está vinculado a um ponto final do Azure Machine Learning. Tenha em atenção que o *endpoint* (URI do serviço) pode ser encontrado na página de ajuda de API para o serviço que escolheu e *apiKey* podem ser encontrados na página principal dos serviços.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corpo do pedido de exemplo:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Ponto final de RetrieveDefaultDefinition chamada para a predefinição UDF
Assim que a estrutura UDF é criada a definição do UDF completa é necessário. O ponto final RetreiveDefaultDefinition ajuda a obter a definição predefinida para uma função escalar que está vinculada a um ponto final do Azure Machine Learning. O payload abaixo requer a obter a definição de UDF predefinido para uma função escalar que está vinculada a um ponto final do Azure Machine Learning. Se não especifica o ponto final real porque já tiver sido fornecido durante a pedido PUT. O ponto final fornecido no pedido, se é fornecido explicitamente chama o do Stream Analytics. Caso contrário, este utilizará a originalmente referenciada. Aqui demora a UDF cadeia de um único parâmetro (uma frase) e devolve um único de saída do tipo cadeia que indica a etiqueta de "dados de sentimento" para esse frases.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corpo do pedido de exemplo:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Um exemplo de saída Isto iria procure algo, conforme mostrado abaixo.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Patch UDF com a resposta
Agora tem de ser corrigido UDF com a resposta anterior, conforme mostrado abaixo.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corpo do pedido (saída de RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar a transformação do Stream Analytics para chamar UDF
Agora consultar UDF (aqui com o nome scoreTweet) para cada evento de entrada e de escrever uma resposta para esse evento uma saída.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
