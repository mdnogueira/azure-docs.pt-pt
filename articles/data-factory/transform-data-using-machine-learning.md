---
title: Criar pipelines de dados preditiva utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como criar um pipeline preditivo utilizando o Azure Machine Learning - atividade de execução do Batch no Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 413f12d301a0e2c47048d23b2d4fb7de6423256d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Criar pipelines preditivos com o Azure Machine Learning e o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão 2 - Pré-visualização](transform-data-using-machine-learning.md)

[O Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe criar, testar e implementar soluções de Análise Preditiva. Do ponto de vista alto nível, é efetuada em três passos:

1. **Criar uma experimentação de preparação**. Execute este passo utilizando o Azure ML Studio. ML studio é um ambiente de desenvolvimento de visual de colaboração que utilizar para formação e testar um modelo de Análise Preditiva utilizando dados de preparação.
2. **Convertê-lo para uma experimentação preditiva**. Depois do seu modelo tem sido preparado com a dados existentes e está pronto para utilizá-lo para pontuar novos dados, pode prepara e simplificar a sua experimentação para classificação.
3. **Implementá-lo como um serviço web**. Pode publicar a sua experimentação de classificação como um serviço web do Azure. Pode enviar dados para o seu modelo através deste ponto de final de serviço web e receber predições resultado fro o modelo.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de execução do Machine Learning Batch no V1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Fábrica de dados e de Machine Learning em conjunto
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizam um publicados [Azure Machine Learning] [azure--] serviço web machine learning para Análise Preditiva. Utilizar o **atividade de execução de lote** um pipeline do Azure Data Factory, pode invocar um serviço web do Azure ML para tornar as predições nos dados no batch. 

Ao longo do tempo, os modelos preditivos do Azure ML experimentações da classificação tem de ser retrained utilizando conjuntos de dados de entrada novo. Pode a reparametrização de um modelo do Azure ML de um pipeline do Data Factory efetuando os seguintes passos:

1. Publica a experimentação de preparação (experimentação preditiva não) como um serviço web. Efetue este passo no Azure ML Studio tal como fez para expor experimentação preditiva como um serviço web no cenário anterior.
2. Utilize a atividade de execução de lote do Azure ML para invocar o serviço web para a experimentação de preparação. Basicamente, pode utilizar a atividade de execução de lote do Azure ML invocar serviço web de formação e classificação serviço web.

Depois de terminar com reparametrização, atualize o serviço web de classificação (experimentação preditiva exposta como um serviço web) com o modelo treinado recentemente utilizando o **da atividade de recursos de atualização do Azure ML**. Consulte [atualizar modelos de atividade do recurso da atualização](update-machine-learning-models.md) artigo para obter detalhes.

## <a name="azure-machine-learning-linked-service"></a>Serviço de Machine Learning ligado do Azure

Criar um **Azure Machine Learning** serviço para associar um serviço Web do Azure Machine Learning a uma fábrica de dados do Azure ligado. O serviço ligado é utilizado pela atividade de execução de lote do Azure Machine Learning e [atividade do recurso da atualização](update-machine-learning-models.md). 


```JSON
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Consulte [serviços ligados de computação](compute-linked-services.md) artigo para obter descrições sobre as propriedades de definição JSON. 

O Azure Machine Learning suporta serviços Web clássicos e novos serviços Web para a sua experimentação preditiva. Pode escolher um direito para utilizar a partir da fábrica de dados. Para obter as informações necessárias para criar o serviço ligado do Azure Machine Learning, aceda a https://services.azureml.net, onde estão apresentados todos os seus (novo) os serviços Web e serviços Web clássicos. Clique no serviço Web que gostaria de aceder e clique em **Consume** página. Cópia **chave primária** para **apiKey** propriedade, e **pedidos de Batch** para **mlEndpoint** propriedade. 

![Serviços Web do Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

##<a name="azure-machine-learning-batch-execution-activity"></a>Atividade de execução de lote do Machine Learning do Azure

O fragmento JSON seguinte define uma atividade de execução de lote do Azure Machine Learning. Definição da atividade tem uma referência para o serviço ligado do Azure Machine Learning que criou anteriormente. 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| nome              | Nome da atividade no pipeline     | Sim      |
| descrição       | Texto que descreve o que faz a atividade.  | Não       |
| tipo              | Atividade U-SQL do Data Lake Analytics, o tipo de atividade é **AzureMLBatchExecution**. | Sim      |
| linkedServiceName | Serviços ligados para o Azure Machine Learning serviço ligado. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| webServiceInputs  | Chaves, pares de valor, mapear os nomes de entradas de serviço Web do Azure Machine Learning. Chave tem de corresponder os parâmetros de entrada definidos no publicados Web serviço do Azure Machine Learning. O valor é um par de propriedades de serviços ligados de armazenamento do Azure e FilePath especificar as localizações de Blob de entrada. | Não       |
| webServiceOutputs | Chaves, pares de valor, mapear os nomes das saídas de serviço Web do Azure Machine Learning. Chave tem de corresponder os parâmetros de saída definidos no publicados Web serviço do Azure Machine Learning. O valor é um serviços ligados de armazenamento do Azure e FilePath especificando a saída de par de propriedades Blob localizações. | Não       |
| globalParameters  | Pares de valor chaves, a transmitir ao ponto final do serviço de execução de lote do Azure ML. As chaves têm de corresponder nomes de parâmetros de serviço web definidos no serviço de web do Azure ML publicadas. Os valores são transmitidos na propriedade GlobalParameters do pedido de execução de lote do Azure ML | Não       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: Experimentações com o Web service entradas/saídas que fazem referência a dados no Blob Storage do Azure

Neste cenário, o serviço Web do Azure Machine Learning torna predições utilizando os dados de um ficheiro no armazenamento de Blobs do Azure e armazena os resultados de predição no armazenamento de Blobs. O JSON seguinte define um pipeline do Data Factory com uma atividade AzureMLBatchExecution. Os dados de entrada e saídos blogue do armazenamento do Azure são referenciados através de um par de LinkedName e FilePath. No exemplo são diferentes um serviço ligado de entradas e saídas, pode utilizar diferentes serviços ligados para cada uma das suas entradas/saídas da fábrica de dados para poder recolher os ficheiros à direita e enviar para o Azure ML Web Service. 

> [!IMPORTANT]
> Na sua experimentação do Azure ML, a entrada de serviço web e a saída portas e parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes a que utilizar para webServiceInputs, webServiceOutputs e definições de globalParameters devem corresponder exatamente os nomes de experimentações. Pode ver o payload de pedido de exemplo na página de ajuda de execução de Batch para o ponto de final do Azure ML verificar o mapeamento esperado.
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: Experimentações com módulos de leitor/escritor para fazer referência aos dados no armazenamento de vários
Outro cenário típico possível quando criar experimentações do Azure ML é utilizar módulos de importar dados e os dados de saída. O módulo de importação de dados é utilizado para carregar dados para uma experimentação e é o módulo de dados de saída guardar os dados a partir das suas experimentações. Para obter detalhes sobre o módulos importar dados e os dados de saída, consulte [importar dados](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [dados de saída](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos da biblioteca MSDN.     

Quando utilizar os módulos de importar dados e os dados de saída, é boa prática para utilizar um parâmetro de serviço Web para cada propriedade destes módulos. Estes parâmetros web permitem-lhe configurar os valores durante o tempo de execução. Por exemplo, pode criar uma experimentação com um módulo de importar dados que utiliza uma base de dados do SQL do Azure: XXX.database.windows.net. Depois de implementar o serviço web, que pretende permitir que os consumidores do serviço web especificar outro servidor de SQL do Azure chamado `YYY.database.windows.net`. Pode utilizar um parâmetro de serviço Web para permitir que este valor para ser configurado.

> [!NOTE]
> Entrada de serviço Web e de saída são diferentes dos parâmetros de serviço Web. O primeiro cenário, constatou como uma entrada e saída podem ser especificados para um serviço Web do Azure ML. Neste cenário, passar parâmetros para um serviço Web que correspondem às propriedades de módulos de dados de saída de dados/importação.
>
> 

Vamos ver um cenário de utilização de parâmetros de serviço Web. Tem um serviço implementado de web Azure Machine Learning que utiliza um módulo leitor para ler dados a partir de uma das origens de dados suportadas pelo Azure Machine Learning (por exemplo: SQL Database do Azure). Após a execução de batch é efetuada, os resultados são escritos utilizando um módulo de escritor (SQL Database do Azure).  Não existem entradas de serviço web e saídas estão definidas em experimentações. Neste caso, recomendamos que configure parâmetros de serviço web relevantes para os módulos de leitor e escritor. Esta configuração permite que o escritor/leitor módulos quando utilizar a atividade de AzureMLBatchExecution. Especifique os parâmetros do serviço Web no **globalParameters** secção no JSON de atividade da seguinte forma.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> Os parâmetros de serviço Web são maiúsculas e minúsculas, por isso, certifique-se de que os nomes que especificar na atividade JSON corresponder aos perfis expostos pelo serviço Web.
>

Depois de terminar com reparametrização, atualize o serviço web de classificação (experimentação preditiva exposta como um serviço web) com o modelo treinado recentemente utilizando o **da atividade de recursos de atualização do Azure ML**. Consulte [atualizar modelos de atividade do recurso da atualização](update-machine-learning-models.md) artigo para obter detalhes.



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
