---
title: Transformar dados utilizando a atividade de Pig no Azure Data Factory | Microsoft Docs
description: "Saiba como pode utilizar a atividade de Pig de um Azure data factory para executar Pig scripts num cluster do HDInsight no-a pedido/seu próprio."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 7612eda8e40cb0ff2b205c2dfe11c2bba1b05b6a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformar dados utilizando a atividade de Pig no Azure Data Factory
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
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados utilizando a atividade de Pig na versão 2 do Data Factory](../transform-data-using-hadoop-pig.md).


A atividade do HDInsight Pig numa fábrica de dados [pipeline](data-factory-create-pipelines.md) executa consultas Pig num [os seus próprios](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight baseado em Windows/Linux. Este artigo baseia-se a [atividades de transformação de dados](data-factory-data-transformation-activities.md) artigo, que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

> [!NOTE] 
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](data-factory-introduction.md) e efetue o tutorial: [construir o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```JSON
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
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
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```
## <a name="syntax-details"></a>Detalhes de sintaxe
| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da atividade |Sim |
| descrição |Texto que descreve o que é utilizada a atividade para |Não |
| tipo |HDinsightPig |Sim |
| Entradas |Uma ou mais entradas consumidas pela atividade Pig |Não |
| saídas |Um ou mais saídas produzidas pela atividade Pig |Sim |
| linkedServiceName |Referência para o cluster do HDInsight registado como um serviço ligado no Factory de dados |Sim |
| Script |Especifique o inline de scripts Pig |Não |
| caminho do script |Armazene no script Pig no armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro é maiúsculas e minúsculas. |Não |
| Define |Especifique parâmetros como pares chave-valor para referenciar no Pig script |Não |

## <a name="example"></a>Exemplo
Vejamos um exemplo de jogos registos de análise onde pretende identificar o tempo despendido por jogadores jogos iniciadas pela sua empresa.

O registo de jogos de exemplo seguinte é um ficheiro de valores separados por vírgula (,). Contém os campos seguintes – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **Pig script** para processar dados:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Para executar este script Pig num pipeline fábrica de dados, efetue os seguintes passos:

1. Criar um serviço ligado para registar [o seus próprios HDInsight cluster de cálculo](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar [cluster de cálculo do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar este serviço ligado **HDInsightLinkedService**.
2. Criar um [serviço ligado](data-factory-azure-blob-connector.md) para configurar a ligação ao Blob storage do Azure que aloja os dados. Vamos chamar este serviço ligado **StorageLinkedService**.
3. Criar [conjuntos de dados](data-factory-create-datasets.md) apontar para a entrada e os dados de saída. Vamos chamar o conjunto de dados de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4. Copie a consulta de Pig num ficheiro de armazenamento de Blobs do Azure configurado num passo #2. Se o armazenamento do Azure que aloja os dados é diferente da que aloja o ficheiro de consulta, crie um serviço ligado do Storage do Azure separada. Consulte o serviço ligado na configuração da atividade. Utilize * * scriptPath * * para especificar o caminho para o ficheiro de script pig e **scriptLinkedService**. 
   
   > [!NOTE]
   > Também pode fornecer inline de scripts Pig na definição da atividade, utilizando o **script** propriedade. No entanto, iremos não recomendamos esta abordagem como todos os carateres especiais nas necessidades de script a ser escape e pode provocar problemas de depuração. A melhor prática é siga o passo #4.
   > 
   > 
5. Crie o pipeline com a atividade de HDInsightPig. Esta atividade processa os dados de entrada executando no script Pig num cluster do HDInsight.

    ```JSON   
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
        ]
      }
    } 
    ```
6. Implemente o pipeline. Consulte [Criar pipelines](data-factory-create-pipelines.md) artigo para obter detalhes. 
7. Monitorize o pipeline com as vistas de monitorização e gestão da fábrica de dados. Consulte [monitorização e gerir pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificar parâmetros para um script do Pig
Considere o seguinte exemplo: jogos registos são ingeridos diariamente para o armazenamento de Blobs do Azure e armazenados numa pasta particionada data com base no e hora. Pretende parametrizar no script Pig e passar a localização da pasta de entrada dinamicamente durante o tempo de execução e também produzir o resultado particionado com a data e hora.

Para utilizar no script Pig parametrizado, efetue o seguinte:

* Definir os parâmetros **define**.

    ```JSON  
    {
        "name": "PigActivitySamplePipeline",
          "properties": {
        "activities": [
            {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                      {
                        "name": "PigSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "PigSampleOut"
                      }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      }
                },
                   "scheduler": {
                      "frequency": "Day",
                      "interval": 1
                }
              }
        ]
      }
    }
    ```  
* No Pig Script, consulte os parâmetros a utilizar '**$parameterName**' conforme mostrado no exemplo seguinte:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Veja Também
* [Atividade do ramo de registo](data-factory-hive-activity.md)
* [Atividade de MapReduce](data-factory-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas do Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

