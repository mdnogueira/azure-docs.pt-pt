---
title: "Transformar dados utilizando a atividade do Hadoop de transmissão em fluxo - Azure | Microsoft Docs"
description: "Saiba como pode utilizar a atividade de transmissão em fluxo do Hadoop de um Azure data factory para transformar dados através da execução de programas de transmissão em fluxo do Hadoop num cluster do HDInsight no-a pedido/seu próprio."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 80ff1c10f2d66f77242bcec0e17ccbaa701e6aa6
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade de transmissão em fluxo do Hadoop no Azure Data Factory
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
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados de utilização do Hadoop atividade versão 2 do Factory de dados de transmissão em fluxo](../transform-data-using-hadoop-streaming.md).


Pode utilizar a atividade de HDInsightStreamingActivity invocar uma tarefa de transmissão em fluxo do Hadoop de um pipeline do Azure Data Factory. O fragmento JSON seguinte mostra a sintaxe para utilizar o HDInsightStreamingActivity um ficheiro JSON do pipeline. 

A atividade de transmissão em fluxo do HDInsight numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa programas de transmissão em fluxo do Hadoop no [os seus próprios](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e efetue o tutorial: [construir o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="json-sample"></a>Exemplo JSON
O cluster do HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e os dados (davinci.txt). Por predefinição, o nome do contentor que é utilizado pelo cluster do HDInsight é o nome do cluster em si. Por exemplo, se o nome do cluster é myhdicluster, o nome do contentor do blob associado seria myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Tenha em atenção os seguintes pontos:

1. Definir o **linkedServiceName** com o nome do serviço ligado que aponta para o HDInsight cluster no qual a tarefa de mapreduce transmissão em fluxo é executada.
2. Definir o tipo de atividade para **HDInsightStreaming**.
3. Para o **mapeador** propriedade, especifique o nome do mapeador de executável. No exemplo, cat.exe é o mapeador de executável.
4. Para o **reducer** propriedade, especifique o nome do executável reducer. No exemplo, wc.exe é reducer executável.
5. Para o **entrada** propriedade de tipo, especifique o ficheiro de entrada (incluindo a localização) para o mapeador de pontos. No exemplo: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample é o contentor de blob, dados/exemplo/Gutenberg é a pasta, não sendo davinci.txt o blob.
6. Para o **saída** propriedade de tipo, especifique o ficheiro de saída (incluindo a localização) para o reducer. O resultado da tarefa de transmissão em fluxo do Hadoop é escrito para a localização especificada para esta propriedade.
7. No **filePaths** secção, especifique os caminhos para o mapeador de pontos e reducer executáveis. No exemplo: "adfsample/example/apps/wc.exe" adfsample é o contentor de blob, / aplicações de exemplo é a pasta e wc.exe for o executável.
8. Para o **fileLinkedService** propriedade, especifique o serviço ligado do Storage do Azure que representa o armazenamento do Azure que contém os ficheiros especificados na secção filePaths.
9. Para o **argumentos** propriedade, especifique os argumentos para a tarefa de transmissão em fluxo.
10. O **getDebugInfo** propriedade é um elemento opcional. Quando for definida para falha, os registos são transferidos só em caso de falha. Quando estiver definido como Always, os registos serão sempre transferidos independentemente do Estado de execução.

> [!NOTE]
> Como é mostrado no exemplo, especifique um conjunto de dados de saída para a atividade de transmissão em fluxo do Hadoop para o **produz** propriedade. Este conjunto de dados é apenas um dataset fictício que é necessário para a agenda de pipeline de unidade. Não é necessário especificar qualquer conjunto de dados de entrada para a atividade para o **entradas** propriedade.  
> 
> 

## <a name="example"></a>Exemplo
O pipeline esta explicação passo a passo executa o programa de mapa/reduza de transmissão em fluxo de contagem de palavra no cluster do Azure HDInsight. 

### <a name="linked-services"></a>Serviços ligados
#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Em primeiro lugar, crie um serviço ligado para ligar o armazenamento do Azure que é utilizado pelo cluster Azure HDInsight ao Azure data factory. Se que copie/cole o seguinte código, não se esqueça de substituir o nome da conta e chave da conta com o nome e a chave do armazenamento do Azure. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Serviço ligado de HDInsight do Azure
Em seguida, crie um serviço ligado para ligar o seu cluster Azure HDInsight ao Azure data factory. Se que copie/cole o código seguinte, substitua o nome de cluster do HDInsight com o nome do cluster do HDInsight e alterar os valores de nome e palavra-passe do utilizador. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Conjuntos de dados
#### <a name="output-dataset"></a>Conjunto de dados de saída
O pipeline neste exemplo não tem quaisquer entradas. Especifique um conjunto de dados de saída para a atividade de transmissão em fluxo do HDInsight. Este conjunto de dados é apenas um dataset fictício que é necessário para a agenda de pipeline de unidade. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline neste exemplo tem apenas uma atividade que é do tipo: **HDInsightStreaming**. 

O cluster do HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e os dados (davinci.txt). Por predefinição, o nome do contentor que é utilizado pelo cluster do HDInsight é o nome do cluster em si. Por exemplo, se o nome do cluster é myhdicluster, o nome do contentor do blob associado seria myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Veja Também
* [Atividade do ramo de registo](data-factory-hive-activity.md)
* [Atividade do PIg](data-factory-pig-activity.md)
* [Atividade de MapReduce](data-factory-map-reduce.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

