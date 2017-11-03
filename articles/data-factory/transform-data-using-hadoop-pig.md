---
title: Transformar dados utilizando a atividade de Pig do Hadoop no Azure Data Factory | Microsoft Docs
description: "Saiba como pode utilizar a atividade de Pig de um Azure data factory para executar Pig scripts num cluster do HDInsight no-a pedido/seu próprio."
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
ms.openlocfilehash: 7745f273283ff998b41fe59a10e705e7564a7bd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade de Pig do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-pig-activity.md)
> * [Versão 2 - Pré-visualização](transform-data-using-hadoop-pig.md)

A atividade do HDInsight Pig numa fábrica de dados [pipeline](concepts-pipelines-activities.md) executa consultas Pig num [os seus próprios](compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [Pig atividade no V1](v1/data-factory-pig-activity.md).

Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e efetue a [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Detalhes de sintaxe

| Propriedade            | Descrição                              | Necessário |
| ------------------- | ---------------------------------------- | -------- |
| nome                | Nome da atividade                     | Sim      |
| descrição         | Texto que descreve o que é utilizada a atividade para | Não       |
| tipo                | Para a atividade do ramo de registo, o tipo de atividade é HDinsightPig | Sim      |
| linkedServiceName   | Referência para o cluster do HDInsight registado como um serviço ligado no Factory de dados. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| scriptLinkedService | Referência a um serviço ligado do Storage do Azure utilizados para armazenar os scripts de Pig para ser executada. Se não especificar este serviço ligado, o serviço ligado de armazenamento de Azure definido no serviço ligado do HDInsight é utilizado. | Não       |
| ScriptPath          | Forneça o caminho para o ficheiro de script armazenado no Storage do Azure que referida pelo scriptLinkedService. O nome de ficheiro é maiúsculas e minúsculas. | Não       |
| getDebugInfo        | Especifica se os ficheiros de registo são copiados para o armazenamento do Azure utilizados pelo cluster do HDInsight (ou) especificado pelo scriptLinkedService. Valores permitidos: None, sempre ou falha. Valor predefinido: nenhuma. | Não       |
| Argumentos           | Especifica uma matriz de argumentos para uma tarefa do Hadoop. Os argumentos são transmitidos como argumentos da linha de comandos para cada tarefa. | Não       |
| Define             | Especifique parâmetros como pares chave-valor para referenciar no Pig script. | Não       |

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do ramo de registo](transform-data-using-hadoop-hive.md)
* [Atividade de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
