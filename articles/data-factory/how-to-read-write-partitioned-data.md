---
title: Como de leitura ou escrita particionada dados no Azure Data Factory | Microsoft Docs
description: "Saiba como ler ou escrever dados particionados versão 2 do Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: shlo
ms.openlocfilehash: ee83fce3eeef4bde6dc8e0ea6f17b40396619412
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Como de leitura ou escrita particionada dados no Azure Data Factory versão 2
Na versão 1, o Azure Data Factory suportado ler ou escrever dados particionados utilizando variáveis de sistema do SliceStart/SliceEnd/WindowStart/WindowEnd. Na versão 2, pode conseguir este comportamento, utilizando um parâmetro de pipeline e agendada/hora a hora de início do acionador como um valor do parâmetro. 

## <a name="use-a-pipeline-parameter"></a>Utilizar um parâmetro de pipeline 
Na versão 1, pode utilizar a propriedade partitionedBy e a variável de sistema do SliceStart conforme mostrado no exemplo seguinte: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/yearno={Year}/monthno={Month}/dayno={Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Para obter mais informações sobre a propriedade partitonedBy, consulte [conector de Blob do Azure versão 1](v1/data-factory-azure-blob-connector.md#dataset-properties) artigo. 

Na versão 2, uma forma de alcançar este comportamento é para executar as seguintes ações: 

1. Definir um **pipeline parâmetro** do tipo cadeia. No exemplo seguinte, o nome do parâmetro de pipeline está **ScheduledRunTime**. 
2. Definir **folderPath** na definição do conjunto de dados para o valor do parâmetro de pipeline para como mostrado no exemplo. 
3. Transmita um valor de codificado para o parâmetro antes de executar o pipeline. Ou, transmita de início um acionador tempo ou hora agendada dinamicamente durante a execução. 

```json
"folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.ScheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.ScheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.ScheduledRunTime, '%d'), '/')",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Passar no valor de um acionador
Na definição do acionador seguinte, a hora agendada do acionador é transmitida como um valor para o parâmetro de pipeline ScheduledRunTime: 

```json
{
    "name": "MyTrigger",
    "properties": {
       ...
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "ScheduledRunTime": "@trigger().scheduledTime"
            }
        }
    }
}
```

## <a name="example"></a>Exemplo

Segue-se uma definição de conjunto de dados de exemplo (que utiliza um parâmetro com o nome: `date`):

```json
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.date, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.date, '%M'), '/dayno=', formatDateTime(pipeline().parameters.date, '%d'), '/')",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Definição de pipeline: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "PARTITIONEDOUTPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/partitionedgameevents/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.date, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.date, '%M')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.date, '%d')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "date": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            },
            "partitionHiveScriptFile": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para instruções completas de criação de uma fábrica de dados com um pipeline, consulte [início rápido: criar uma fábrica de dados](quickstart-create-data-factory-powershell.md). 