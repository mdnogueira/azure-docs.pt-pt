---
title: Atualizar Modelos de Machine Learning utilizando o Azure Data Factory | Microsoft Docs
description: Descreve como criar criar pipelines preditivos com o Azure Data Factory e o Azure Machine Learning
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 009c7349e82194f9b7f0c8a0c49c427fc78bba85
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Atualizar Modelos do Azure Machine Learning utilizando a atividade do recurso da atualização

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do ramo de registo](data-factory-hive-activity.md) 
> * [Atividade do PIg](data-factory-pig-activity.md)
> * [Atividade de MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [atualizar modelos de machine learning na versão 2 do Data Factory](../update-machine-learning-models.md).

Este artigo complementa o principal do Azure Data Factory - artigo de integração do Azure Machine Learning: [Criar pipelines preditivos com o Azure Machine Learning e o Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Se ainda não o fez, consulte o artigo principal antes de ler através deste artigo. 

## <a name="overview"></a>Descrição geral
Ao longo do tempo, os modelos preditivos do Azure ML experimentações da classificação tem de ser retrained utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, que pretende atualizar o serviço web de classificação com o modelo de ML retrained. Os passos típicos para ativar a reparametrização e atualizar modelos do Azure ML através de serviços web são:

1. Criar uma experimentação no [Azure ML Studio](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, utilizar o Azure ML Studio para publicar serviços web para ambos os **experimentação de preparação** e classificação /**experimentação preditiva**.

A tabela seguinte descreve os serviços web utilizados neste exemplo.  Consulte [Machine Learning reparametrização dos modelos programáticos](../../machine-learning/machine-learning-retrain-models-programmatically.md) para obter mais detalhes.

- **Serviço web de preparação** - recebe dados de formação e produz os modelos de formação. O resultado a reparametrização é um ficheiro de .ilearner um Blob storage do Azure. O **predefinido endpoint** é criado automaticamente para que quando publicar a formação experimentação como um serviço web. Pode criar mais pontos finais, mas o exemplo utiliza apenas o ponto final predefinido.
- **Serviço web de classificação** - recebe exemplos de dados sem etiqueta e torna as predições. A saída da predição pode ter várias formas, como um ficheiro. csv ou linhas numa base de dados SQL do Azure, dependendo da configuração da experimentação. O ponto final predefinido é criado automaticamente quando publicar a experimentação preditiva como um serviço web. 

A imagem seguinte ilustra a relação entre a formação e a classificação de pontos finais do Azure ML.

![Serviços web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Pode invocar o **serviço web de preparação** utilizando o **atividade de execução de lote do Azure ML**. Invocar um serviço web de formação é igual ao invocar um serviço da web do Azure ML (serviço web de classificação) para classificação de dados. As secções anteriores abrangem como invocar um serviço web do Azure ML um pipeline do Azure Data Factory em detalhe. 

Pode invocar o **da classificação de serviço web** utilizando o **da atividade de recursos de atualização do Azure ML** para atualizar o serviço web com o modelo treinado recentemente. Os exemplos seguintes fornecem definições de serviço ligado: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>A classificação de serviço web é um serviço web clássico
Se o serviço web de classificação é um **serviço web clássico**, crie o segundo **ponto final não predefinidas e atualizável** utilizando o portal do Azure. Consulte [criar pontos finais](../../machine-learning/machine-learning-create-endpoint.md) artigo para obter os passos. Depois de criar o ponto de final atualizável não predefinido, efetue os seguintes passos:

* Clique em **de execução de lote** para obter o valor URI para o **mlEndpoint** propriedade JSON.
* Clique em **ATUALIZAÇÃO recursos** ligação para obter o valor URI para o **updateResourceEndpoint** propriedade JSON. A chave de API é a página de ponto final (no canto inferior direito).

![ponto final atualizável](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

O exemplo seguinte fornece uma definição de JSON de exemplo para o serviço AzureML ligado. O serviço ligado utiliza o apiKey para autenticação.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>A classificação de serviço web é o serviço web do Azure Resource Manager 
Se o serviço web é o novo tipo de serviço web que expõe um ponto final do Azure Resource Manager, não terá de adicionar o segundo **não predefinidas** ponto final. O **updateResourceEndpoint** no serviço ligado tem o formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Pode obter os valores para proprietários de local no URL ao consultar o serviço web no [Portal de serviços Web do Azure Machine Learning](https://services.azureml.net/). O novo tipo de ponto final de recursos de update requer um token do AAD (Azure Active Directory). Especifique **servicePrincipalId** e **servicePrincipalKey**no AzureML serviço ligado. Consulte [como criar o serviço principal e atribuir permissões para gerir recursos do Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Eis um exemplo de definição de serviço AzureML ligado: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

O cenário seguinte fornece mais detalhes. Tem um exemplo para a reparametrização e atualizar os modelos do Azure ML de um pipeline do Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: reparametrização e a atualizar um modelo do Azure ML
Esta secção fornece um exemplo de um pipeline que utiliza o **atividade de execução de lote do Azure ML** a reparametrização de um modelo. O pipeline também utiliza o **atividade do Azure ML atualizar recursos** para atualizar o modelo no serviço web de classificação. A secção também fornece fragmentos JSON para todos os serviços ligados, conjuntos de dados e pipeline no exemplo.

Segue-se a vista de diagrama do exemplo de pipeline. Como pode ver, a atividade de execução de lote do Azure ML demora a entrada de formação e produz uma saída de formação (ficheiro iLearner). A atividade de recursos do Azure ML atualização demora este resultado de formação e o modelo de pontuação web ponto final do serviço de atualizações. A atividade de recursos de atualização não produzir qualquer saída. O placeholderBlob é apenas um saída fictício conjunto de dados que é necessário para o serviço do Azure Data Factory para executar o pipeline.

![Diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado do armazenamento de Blobs do Azure:
Storage do Azure mantém os dados seguintes:

* dados de preparação. Os dados de entrada para o serviço de web de formação do Azure ML.  
* ficheiro iLearner. A saída do serviço web de formação do Azure ML. Este ficheiro também é a entrada para a atividade de recursos de atualização.  

Segue-se a definição de JSON de exemplo do serviço ligado:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Conjunto de dados entrado de formação:
O conjunto de dados seguinte representa os dados de preparação de entrada para o serviço de web de formação do Azure ML. A atividade de execução de lote do Azure ML demora este conjunto de dados como entrada.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Conjunto de dados de saída formação:
O conjunto de dados seguinte representa o ficheiro iLearner a saída do serviço web de formação do Azure ML. A atividade de execução de lote do Azure ML produz este conjunto de dados. Este conjunto de dados também é a entrada para a atividade de recursos do Azure ML atualização.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço ligado para o ponto final de formação do Azure ML
O fragmento JSON seguinte define um serviço ligado do Azure Machine Learning que aponta para o ponto final predefinido do serviço web formação.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

No **Azure ML Studio**, faça o seguinte para obter os valores para **mlEndpoint** e **apiKey**:

1. Clique em **serviços WEB** no menu da esquerda.
2. Clique em de **serviço web de preparação** na lista de serviços web.
3. Clique em Copiar junto a **chave de API** caixa de texto. Cole a chave na área de transferência no editor de JSON de fábrica de dados.
4. No **do Azure ML studio**, clique em **de execução de lote** ligação.
5. Copiar o **URI de pedido** do **pedido** secção e cole-o editor de JSON de fábrica de dados.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço ligado para o ponto final de classificação atualizável do Azure ML:
O fragmento JSON seguinte define um serviço ligado do Azure Machine Learning que aponta para o ponto de final atualizável do serviço web classificação não predefinidas.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Conjunto de dados de saída do marcador de posição:
A atividade de recursos do Azure ML atualização não gera qualquer saída. No entanto, o Azure Data Factory requer um conjunto de dados de saída para orientar o agendamento de um pipeline. Por conseguinte, iremos utilizar um conjunto de dados fictício/marcador de posição neste exemplo.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline com duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote do Azure ML aceita como entrada os dados de preparação e produz um ficheiro iLearner como resultado. A atividade invoca o serviço web de formação (experimentação de preparação exposta como um serviço web) com os dados de entrada de formação e recebe o ficheiro ilearner a partir do webservice. O placeholderBlob é apenas um saída fictício conjunto de dados que é necessário para o serviço do Azure Data Factory para executar o pipeline.

![Diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
