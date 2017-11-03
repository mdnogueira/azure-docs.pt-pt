---
title: Transformar dados utilizando a atividade do Spark no Azure Data Factory | Microsoft Docs
description: "Saiba como transformar dados executando Spark programas de um pipeline de fábrica de dados do Azure com a atividade de Spark."
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
ms.date: 09/29/2017
ms.author: shengc
ms.openlocfilehash: f1548c6ad397a7154482fa73e992aef9201c5752
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade do Spark no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-spark.md)
> * [Versão 2 - Pré-visualização](transform-data-using-spark.md)

A atividade de Spark numa fábrica de dados [pipeline](concepts-pipelines-activities.md) executa um programa de Spark num [os seus próprios](compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados. Quando utiliza um serviço de Spark ligado a pedido, o Data Factory cria automaticamente um cluster do Spark para lhe just-in-time processar os dados e, em seguida, elimina o cluster depois do processamento é concluído. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade do Spark no V1](v1/data-factory-spark.md).

> [!IMPORTANT]
> Atividade de Spark não suporta clusters do HDInsight Spark que utilizam um Azure Data Lake Store, como armazenamento principal.

## <a name="spark-activity-properties"></a>Propriedades da atividade do Spark
Segue-se a definição de JSON de exemplo de uma atividade de Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON:

| Propriedade              | Descrição                              | Necessário |
| --------------------- | ---------------------------------------- | -------- |
| nome                  | Nome da atividade no pipeline.    | Sim      |
| descrição           | Texto que descreve o que faz a atividade.  | Não       |
| tipo                  | Para a atividade do Spark, o tipo de atividade é HDInsightSpark. | Sim      |
| linkedServiceName     | Nome do serviço ligado de HDInsight Spark em que executa o programa de Spark. Para saber mais sobre este serviço ligado, consulte [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| sparkJobLinkedService | Storage do Azure ligado serviço que detém o Spark registos, dependências e ficheiro de tarefa.  Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster do HDInsight é utilizado. | Não       |
| rootPath              | O contentor de Blob do Azure e a pasta que contém o ficheiro de Spark. O nome de ficheiro é maiúsculas e minúsculas. Consulte a estrutura de pastas secção (secção seguinte) para obter detalhes sobre a estrutura desta pasta. | Sim      |
| entryFilePath         | Caminho relativo para a pasta raiz do Spark/pacote do código. | Sim      |
| className             | Classe principal de Java/Spark da aplicação      | Não       |
| Argumentos             | Uma lista de argumentos da linha de comandos para o programa de Spark. | Não       |
| proxyUser             | A conta de utilizador para representar a execução do programa de Spark | Não       |
| sparkConfig           | Especifique os valores de propriedades de configuração do Spark listados no tópico: [a configuração do Spark – propriedades da aplicação](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não       |
| getDebugInfo          | Especifica se os ficheiros de registo do Spark são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: None, sempre ou falha. Valor predefinido: nenhuma. | Não       |

## <a name="folder-structure"></a>Estrutura de pastas
Tarefas do Spark são mais extensíveis que as tarefas do Pig/Hive. Para tarefas do Spark, pode fornecer múltiplas dependências, tal como jar pacotes (colocadas no java CLASSPATH), ficheiros de python (colocados a PYTHONPATH) e outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento de Blobs do Azure referenciado pelo serviço ligado de HDInsight. Em seguida, carregue ficheiros dependentes para as pastas sub adequado na pasta raiz representado pelo **entryFilePath**. Por exemplo, carregar o python para a subpasta pyFiles e ficheiros jar para a subpasta v7 da pasta raiz. Em runtime, o serviço fábrica de dados espera a seguinte estrutura de pasta do Blob storage do Azure:     

| Caminho                  | Descrição                              | Necessário | Tipo   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(root)            | O caminho da raiz da tarefa do Spark no serviço ligado de armazenamento | Sim      | Pasta |
| &lt;definido pelo utilizador&gt; | O caminho que apontam para o ficheiro de entrada da tarefa de Spark | Sim      | Ficheiro   |
| . / jars                | Todos os ficheiros desta pasta são carregados e colocados a classpath java do cluster | Não       | Pasta |
| . / pyFiles             | Todos os ficheiros desta pasta são carregados e colocados PYTHONPATH do cluster | Não       | Pasta |
| . / ficheiros               | Todos os ficheiros desta pasta são carregados e colocados no diretório de trabalho de executor | Não       | Pasta |
| . / arquiva            | Todos os ficheiros desta pasta são descomprimidos | Não       | Pasta |
| . / registos                | A pasta que contém os registos do cluster do Spark. | Não       | Pasta |

Eis um exemplo de um armazenamento que contém dois ficheiros de tarefa do Spark no armazenamento de Blobs do Azure referenciada pelo serviço ligado de HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
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
* [Atividade de execução de lote de aprendizagem máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
