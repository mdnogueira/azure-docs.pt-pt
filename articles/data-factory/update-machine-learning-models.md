---
title: Atualizar Modelos de machine learning utilizando o Azure Data Factory | Microsoft Docs
description: Descreve como criar criar pipelines preditivos utilizando o Azure Data Factory e machine learning
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: df139383eb2fa20fe75ecc6b3f5e2aa0773f186c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Atualizar Modelos do Azure Machine Learning, utilizando a atividade do recurso de atualização
Este artigo complementa o principal do Azure Data Factory - artigo de integração do Azure Machine Learning: [Criar pipelines preditivos com o Azure Machine Learning e o Azure Data Factory](transform-data-using-machine-learning.md). Se ainda não o fez, consulte o artigo principal antes de ler através deste artigo. 

## <a name="overview"></a>Descrição geral
Como parte do processo de operacionalizar modelos do Azure Machine Learning, o seu modelo está preparado e guardou. Pode, em seguida, utilizado para criar um serviço Web predicative. O serviço Web, em seguida, pode ser utilizado em web sites, dashboards e as aplicações móveis.

Modelos criados com Machine Learning não são normalmente estáticos. Como novos dados ficarem disponíveis ou quando o consumidor da API tem os seus próprios dados o modelo precise de ser retrained. Consulte [reparametrização de um modelo de Machine Learning](../machine-learning/machine-learning-retrain-machine-learning-model.md) para obter detalhes sobre como pode reparametrização de um modelo no Azure Machine Learning. 

Reparametrização pode ocorrer com frequência. Com a atividade de execução de lote e a atividade de recursos de atualização, pode operacionalizar o modelo do Azure Machine Learning reparametrização e atualizar o serviço Web preditiva utilizando o Data Factory. 

A imagem seguinte ilustra a relação entre os serviços Web de formação e preditiva. 

![Serviços web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Atividade de recursos de atualização do Azure Machine Learning 

O fragmento JSON seguinte define uma atividade de execução de lote do Azure Machine Learning.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```




| Propriedade                      | Descrição                              | Necessário |
| :---------------------------- | :--------------------------------------- | :------- |
| nome                          | Nome da atividade no pipeline     | Sim      |
| descrição                   | Texto que descreve o que faz a atividade.  | Não       |
| tipo                          | Para a atividade de recursos de atualização do Azure Machine Learning, é o tipo de atividade **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | Serviço ligado do Machine Learning do Azure que contém a propriedade updateResourceEndpoint. | Sim      |
| Trainedmodeldatasetname              | Nome do módulo modelo preparado na experimentação de serviço Web da atualização | Sim      |
| trainedModelLinkedServiceName | Nome do serviço ligado do Storage do Azure que contém o ficheiro ilearner que é carregado pela operação de atualização | Sim      |
| trainedModelFilePath          | O caminho de ficheiro relativo no trainedModelLinkedService para representar o ficheiro ilearner que é carregado pela operação de atualização | Sim      |


## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto

Todo o processo de operacionalizar reparametrização um modelo e atualize os serviços Web preditiva envolve os seguintes passos: 

- Invocar o **serviço Web de preparação** utilizando o **atividade de execução de lote**. Invocar uma serviço Web de formação é igual a invocar um serviço Web preditiva descrito [Criar pipelines preditivos com o Azure Machine Learning e execução de lote de fábrica de dados de atividade](transform-data-using-machine-learning.md). O resultado de preparação do serviço Web é um ficheiro iLearner que pode utilizar para atualizar o serviço Web preditiva. 
- Invocar o **atualizar o ponto final de recursos** do **preditiva serviço Web** utilizando o **atividade de atualizar o recurso** para atualizar o serviço Web com o modelo treinado recentemente. 

## <a name="azure-machine-learning-linked-service"></a>Serviço de Machine Learning ligado do Azure

Acima mencionados ponto-a-ponto fluxo de trabalho funcionar, terá de criar dois serviços do Azure Machine Learning ligada: 

1. Do Azure Machine Learning serviço ligado ao serviço web de formação, este serviço ligado é utilizado pela atividade de execução de lote da mesma forma como o que é mencionado na [Criar pipelines preditivos com o Azure Machine Learning e o lote de fábrica de dados Atividade de execução](transform-data-using-machine-learning.md). Diferença é que a saída do serviço web formação é um ficheiro iLearner qual é seguidamente utilizado pela atividade de atualizar o recurso para atualizar o serviço web preditiva. 
2. Um serviço ligado do Azure Machine Learning ao ponto final de recursos de atualização do serviço web preditiva. Este serviço ligado é utilizado pela atividade de atualizar o recurso para atualizar o serviço web preditiva utilizando o ficheiro iLearner devolvido from above passo. 

Para o serviço ligado do Azure Machine Learning segundo, a configuração é diferente quando o serviço Web do Azure Machine Learning é um serviço Web clássico ou um novo serviço Web. As diferenças são debatidas separadamente nas secções seguintes. 

## <a name="web-service-is-a-classic-web-service"></a>Serviço Web é um serviço web clássico
Se o serviço web de predição é uma **serviço web clássico**, crie o segundo **ponto final não predefinidas e atualizável** utilizando o portal do Azure. Consulte [criar pontos finais](../machine-learning/machine-learning-create-endpoint.md) artigo para obter os passos. Depois de criar o ponto de final atualizável não predefinido, efetue os seguintes passos:

* Clique em **de execução de lote** para obter o valor URI para o **mlEndpoint** propriedade JSON.
* Clique em **ATUALIZAÇÃO recursos** ligação para obter o valor URI para o **updateResourceEndpoint** propriedade JSON. A chave de API é a página de ponto final (no canto inferior direito).

![ponto final atualizável](./media/update-machine-learning-models/updatable-endpoint.png)

Depois disso, utilizar o seguinte exemplo de serviço ligado para criar um novo Azure Machine Learning serviço ligado. O serviço ligado utiliza o apiKey para autenticação.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint2"
            },
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Serviço Web é o novo serviço web do Azure Resource Manager 

Se o serviço web é o novo tipo de serviço web que expõe um ponto final do Azure Resource Manager, não terá de adicionar o segundo **não predefinidas** ponto final. O **updateResourceEndpoint** no serviço ligado tem o formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Pode obter os valores para proprietários de local no URL ao consultar o serviço web no [Portal de serviços Web do Azure Machine Learning](https://services.azureml.net/). 

O novo tipo de ponto final de recursos de atualização necessita de autenticação principal de serviço. Para utilizar a autenticação principal de serviço, registar uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder a **contribuinte** ou **proprietário** onde de grupo da função de subscrição ou o recurso o serviço web pertence. O consulte [como criar o serviço principal e atribuir permissões para gerir recursos do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md). Tome nota dos seguintes valores que utilizar para definir o serviço ligado:

- ID da aplicação
- Chave da aplicação 
- ID do inquilino

Segue-se uma definição de serviço ligado de exemplo: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
            "type": "SecureString",
            "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

O cenário seguinte fornece mais detalhes. Tem um exemplo para a reparametrização e atualizar os modelos do Azure ML de um pipeline do Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exemplo: Reparametrização e a atualizar um modelo do Azure Machine Learning

Esta secção fornece um exemplo de um pipeline que utiliza o **atividade de execução de lote do Azure ML** a reparametrização de um modelo. O pipeline também utiliza o **atividade do Azure ML atualizar recursos** para atualizar o modelo no serviço web de classificação. A secção também fornece fragmentos JSON para todos os serviços ligados, conjuntos de dados e pipeline no exemplo.

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
O fragmento JSON seguinte define um serviço ligado do Azure Machine Learning que aponta para o ponto final atualizável do serviço web classificação.  

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

### <a name="pipeline"></a>Pipeline
O pipeline com duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote aceita como entrada os dados de preparação e produz um ficheiro iLearner como resultado. A atividade de atualizar o recurso, em seguida, utiliza este ficheiro iLearner e utilizá-lo para atualizar o serviço web preditiva. 

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            }, 
                            "FilePath":"azuremltesting/input"
                        }, 
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference" 
                            }, 
                            "FilePath":"azuremltesting/input"
                        }        
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"   
                            }, 
                            "FilePath":"azuremltesting/output"
                        }        
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                                "type": "LinkedServiceReference",
                                "referenceName": "StorageLinkedService"
                            },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [ 
                    { 
                        "activity": "amlbeGetilearner", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ]

            }
        ]
    }
}
```
## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade do PIg](transform-data-using-hadoop-pig.md)
* [Atividade de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
