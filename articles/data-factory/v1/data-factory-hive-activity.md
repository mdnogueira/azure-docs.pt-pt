---
title: Transformar dados utilizando o Hive atividade - Azure | Microsoft Docs
description: "Saiba como pode utilizar a atividade do ramo de registo de um Azure data factory para executar consultas do Hive num cluster do HDInsight no-a pedido/seu próprio."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 995983a8e32bc01ddc1ab8bbc64345da96875941
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade do ramo de registo no Azure Data Factory 
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
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados utilizando a atividade do ramo de registo de versão 2 do Data Factory](../transform-data-using-hadoop-hive.md).

A atividade do ramo de registo do HDInsight numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa consultas do Hive no [os seus próprios](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e efetue o tutorial: [construir o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Detalhes de sintaxe
| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade |Sim |
| descrição |Texto que descreve o que é utilizada a atividade para |Não |
| tipo |HDinsightHive |Sim |
| Entradas |Entradas consumidas pela atividade do ramo de registo |Não |
| saídas |Saídas produzidas pela atividade do ramo de registo |Sim |
| linkedServiceName |Referência para o cluster do HDInsight registado como um serviço ligado no Factory de dados |Sim |
| Script |Especifique o inline de script de ramo de registo |Não |
| caminho do script |Guarde o script de ramo de registo num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro é maiúsculas e minúsculas. |Não |
| Define |Especifique parâmetros como pares chave-valor para referenciar dentro do script de ramo de registo utilizando 'hiveconf' |Não |

## <a name="example"></a>Exemplo
Vejamos um exemplo de jogos registos de análise onde pretende identificar o tempo despendido por utilizadores jogos iniciadas pela sua empresa. 

O registo de seguinte é um registo de jogos de exemplo, que é a vírgula (`,`) separados e contém os campos seguintes – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script de ramo de registo** para processar dados:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Para executar este script de ramo de registo num pipeline Data Factory, tem de fazer o seguinte

1. Criar um serviço ligado para registar [o seus próprios HDInsight cluster de cálculo](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar [cluster de cálculo do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar este serviço ligado "HDInsightLinkedService".
2. Criar um [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação ao Blob storage do Azure que aloja os dados. Vamos chamar este serviço ligado "StorageLinkedService"
3. Criar [conjuntos de dados](data-factory-create-datasets.md) apontar para a entrada e os dados de saída. Vamos chamar o conjunto de dados de entrada "HiveSampleIn" e o conjunto de dados de saída "HiveSampleOut"
4. Copiar a consulta do Hive, como um ficheiro para o Blob Storage do Azure configurada no passo #2. Se o armazenamento para alojar dados é diferente da que aloja este ficheiro de consulta, criar um serviço ligado do Storage do Azure separada e se refere ao mesmo a atividade. Utilize **scriptPath** para especificar o caminho para o ramo de registo do ficheiro de consulta e **scriptLinkedService** para especificar o armazenamento do Azure que contém o ficheiro de script. 
   
   > [!NOTE]
   > Também pode fornecer o inline de script de ramo de registo na definição da atividade, utilizando o **script** propriedade. Iremos não recomendamos esta abordagem como todos os carateres especiais no script dentro as necessidades de documentos JSON para ser escape e pode provocar problemas de depuração. A melhor prática é siga o passo #4.
   > 
   > 
5. Crie um pipeline com a atividade HDInsightHive. A atividade processos/transformações de dados.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Implemente o pipeline. Consulte [Criar pipelines](data-factory-create-pipelines.md) artigo para obter detalhes. 
7. Monitorize o pipeline com as vistas de monitorização e gestão da fábrica de dados. Consulte [monitorização e gerir pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificar parâmetros para um script de ramo de registo
Neste exemplo, jogos registos são ingeridos diariamente para o armazenamento de Blobs do Azure em são armazenados numa pasta particionada com a data e hora. Pretende parametrizar o script de ramo de registo e passar a localização da pasta de entrada dinamicamente durante o tempo de execução e também produzir o resultado particionado com a data e hora.

Para utilizar o script de ramo de registo parametrizada, efetue o seguinte

* Definir os parâmetros **define**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* O Script de ramo de registo, referem-se a utilizar o parâmetro **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
## <a name="see-also"></a>Veja Também
* [Atividade do PIg](data-factory-pig-activity.md)
* [Atividade de MapReduce](data-factory-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

