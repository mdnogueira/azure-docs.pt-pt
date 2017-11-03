---
title: Transformar dados utilizando a atividade de MapReduce do Hadoop no Azure Data Factory | Microsoft Docs
description: Saiba como processar dados executando Hadoop MapReduce programas num cluster do Azure HDInsight a partir de um Azure data factory.
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
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: b473ba03b8b700b3123f82343e59a1ed897c4189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade de MapReduce do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-map-reduce.md)
> * [Versão 2 - Pré-visualização](transform-data-using-hadoop-map-reduce.md)


A atividade de HDInsight MapReduce numa fábrica de dados [pipeline](concepts-pipelines-activities.md) invoca o programa de MapReduce no [os seus próprios](compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de MapReduce em V1](v1/data-factory-map-reduce.md).


Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e efetue o tutorial: [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Consulte [Pig](transform-data-using-hadoop-pig.md) e [Hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre como executar Pig/Hive scripts no HDInsight a um cluster de um pipeline, utilizando as atividades de HDInsight Pig e do Hive. 

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "jarlibs": "MyAzureStorage/jars/jar1",
        "getDebugInfo": "Failure",
        "arguments": [
          "-SampleHadoopJobArgument1"
        ],
        "defines": {
          "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Detalhes de sintaxe

| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| nome              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve o que é utilizada a atividade para | Não       |
| tipo              | Para a atividade de MapReduce, o tipo de atividade é HDinsightMapReduce | Sim      |
| linkedServiceName | Referência para o cluster do HDInsight registado como um serviço ligado no Factory de dados. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| className         | Nome da classe para ser executada         | Sim      |
| jarLinkedService  | Referência a um serviço ligado do Storage do Azure utilizados para armazenar os ficheiros Jar. Se não especificar este serviço ligado, o serviço ligado de armazenamento de Azure definido no serviço ligado do HDInsight é utilizado. | Não       |
| jarFilePath       | Forneça o caminho para os ficheiros Jar armazenados no Storage do Azure que referida pelo jarLinkedService. O nome de ficheiro é maiúsculas e minúsculas. | Sim      |
| jarlibs           | Forneça o caminho para os ficheiros da biblioteca Jar referenciada pela tarefa armazenada no Storage do Azure que referida pelo jarLinkedService. O nome de ficheiro é maiúsculas e minúsculas. | Não       |
| getDebugInfo      | Especifica se os ficheiros de registo são copiados para o armazenamento do Azure utilizados pelo cluster do HDInsight (ou) especificado por jarLinkedService. Valores permitidos: None, sempre ou falha. Valor predefinido: nenhuma. | Não       |
| Argumentos         | Especifica uma matriz de argumentos para uma tarefa do Hadoop. Os argumentos são transmitidos como argumentos da linha de comandos para cada tarefa. | Não       |
| Define           | Especifique parâmetros como pares chave-valor para referenciar dentro do script de ramo de registo. | Não       |



## <a name="example"></a>Exemplo
Pode utilizar a atividade de MapReduce do HDInsight para executar qualquer ficheiro jar do MapReduce num cluster do HDInsight. A seguinte definição de JSON exemplo de um pipeline, a atividade do HDInsight é configurada para executar um ficheiro Mahout JAR.

```json   
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
Pode especificar os argumentos para o programa de MapReduce no **argumentos** secção. Em runtime, pode ver alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do framework de MapReduce. Para diferenciar os argumentos com os argumentos de MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo seguinte (- s, - entrada, - saída etc., são opções seguidas imediatamente pelos respetivos valores).

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade do PIg](transform-data-using-hadoop-pig.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
