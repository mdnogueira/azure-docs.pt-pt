---
title: "Invocar o programa de MapReduce da fábrica de dados do Azure"
description: Saiba como processar dados executando MapReduce programas num cluster do Azure HDInsight a partir de um Azure data factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e5fd49c6b269b5f247440c2bc91680fc77fc296c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar MapReduce programas da fábrica de dados
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
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados utilizando a atividade de MapReduce na versão 2 do Data Factory](../transform-data-using-hadoop-map-reduce.md).


A atividade de HDInsight MapReduce numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa programas de MapReduce num [os seus próprios](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e efetue o tutorial: [construir o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo.  

## <a name="introduction"></a>Introdução
Um pipeline de um Azure data factory processa dados nos serviços do storage ligadas utilizando os serviços ligados de computação. Contém uma sequência de atividades em que cada atividade executa uma operação de processamento específico. Este artigo descreve utilizando a atividade de MapReduce do HDInsight.

Consulte [Pig](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) para obter detalhes sobre como executar Pig/Hive scripts num HDInsight baseado em Windows/Linux cluster de um pipeline, utilizando as atividades de HDInsight Pig e do Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON de atividade de HDInsight MapReduce
Na definição de JSON para a atividade do HDInsight: 

1. Definir o **tipo** do **atividade** para **HDInsight**.
2. Especifique o nome da classe para **className** propriedade.
3. Especifique o caminho para o ficheiro JAR, incluindo o nome de ficheiro para **jarFilePath** propriedade.
4. Especifique o serviço ligado que referencia o armazenamento de Blobs do Azure que contém o ficheiro JAR para **jarLinkedService** propriedade.   
5. Especifique os argumentos para o programa de MapReduce no **argumentos** secção. Em runtime, pode ver alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do framework de MapReduce. Para diferenciar os argumentos com os argumentos de MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo seguinte (- s, - entrada, - saída etc., são opções seguidas imediatamente pelos respetivos valores).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
Pode utilizar a atividade de MapReduce do HDInsight para executar qualquer ficheiro jar do MapReduce num cluster do HDInsight. A seguinte definição de JSON exemplo de um pipeline, a atividade do HDInsight é configurada para executar um ficheiro Mahout JAR.

## <a name="sample-on-github"></a>Exemplo no GitHub
Pode transferir uma amostra para utilizar a atividade de MapReduce HDInsight do: [amostras do Data Factory no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executar o programa de contagem de Word
O pipeline neste exemplo é executado o programa de mapa/reduza a contagem de palavra no cluster do Azure HDInsight.   

### <a name="linked-services"></a>Serviços ligados
Em primeiro lugar, crie um serviço ligado para ligar o armazenamento do Azure que é utilizado pelo cluster Azure HDInsight ao Azure data factory. Se que copie/cole o seguinte código, não se esqueça de substituir **nome da conta** e **chave da conta** com o nome e a chave do armazenamento do Azure. 

#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure

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
Em seguida, crie um serviço ligado para ligar o seu cluster Azure HDInsight ao Azure data factory. Se que copie/cole o código seguinte, substitua **nome do cluster de HDInsight** com o nome do cluster do HDInsight e alteração de valores de nome e palavra-passe de utilizador.   

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
O pipeline neste exemplo não tem quaisquer entradas. Especifique um conjunto de dados de saída para a atividade de MapReduce do HDInsight. Este conjunto de dados é apenas um dataset fictício que é necessário para a agenda de pipeline de unidade.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline neste exemplo tem apenas uma atividade que é do tipo: HDInsightMapReduce. Algumas das propriedades importantes no JSON são: 

| Propriedade | Notas |
|:--- |:--- |
| tipo |O tipo tem de ser definido **HDInsightMapReduce**. |
| className |É o nome da classe: **wordcount** |
| jarFilePath |Caminho para o ficheiro jar que contém a classe. Se que copie/cole o seguinte código, não se esqueça de alterar o nome do cluster. |
| jarLinkedService |Serviço ligado do Storage do Azure que contém o ficheiro jar. Este serviço ligado refere-se para o armazenamento que estão associado com o cluster do HDInsight. |
| Argumentos |O programa de wordcount aceita dois argumentos, uma entrada e um resultado. O ficheiro de entrada é o ficheiro de davinci.txt. |
| frequência/intervalo |Os valores para estas propriedades corresponderem o conjunto de dados de saída. |
| linkedServiceName |refere-se ao serviço ligado de HDInsight criou anteriormente. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Executar programas do Spark
Pode utilizar a atividade MapReduce para executar programas do Spark no seu cluster do HDInsight Spark. Veja [Invoke Spark programs from Azure Data Factory (Invocar programas do Spark a partir do Azure Data Factory)](data-factory-spark.md) para obter detalhes  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Veja Também
* [Atividade do ramo de registo](data-factory-hive-activity.md)
* [Atividade do PIg](data-factory-pig-activity.md)
* [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

