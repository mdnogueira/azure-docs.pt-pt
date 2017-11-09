---
title: Aguarde que a atividade no Azure Data Factory | Microsoft Docs
description: "A atividade de espera interrompe a execução de pipeline para o período de tempo especificado."
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
ms.date: 11/07/2017
ms.author: shlo
ms.openlocfilehash: 3ff06ea07fcf5e391783575adf9dd5d99255eced
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="wait-activity-in-azure-data-factory"></a>Aguarde que a atividade no Azure Data Factory
Quando utiliza uma atividade de espera num pipeline, o pipeline aguarda que o período de tempo antes de continuar com a execução de atividades subsequentes especificado. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory V1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome do `Wait` atividade. | Cadeia | Sim
tipo | Tem de ser definido como **aguarde**. | Cadeia | Sim
waitTimeInSeconds | O número de segundos que o pipeline aguarda antes de continuar com o processamento. | Número inteiro | Sim

## <a name="example"></a>Exemplo

> [!NOTE]
> Esta secção fornece definições de JSON e comandos do PowerShell de exemplo para executar o pipeline. Para obter instruções com instruções passo a passo para criar um pipeline de fábrica de dados utilizando as definições do Azure PowerShell e JSON, consulte [tutorial: criar uma fábrica de dados utilizando o Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline com atividade de espera
Neste exemplo, o pipeline com duas atividades: **até** e **aguarde**. A atividade de espera está configurada para aguardar por um segundo. O pipeline é executado a atividade de Web num ciclo com um segundo, à espera que o tempo entre cada execução. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Passos seguintes
Outras atividades de fluxo de controlo suportadas pela fábrica de dados, consulte: 

- [Se a condição atividade](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade de Web](control-flow-web-activity.md)
- [Até que a atividade](control-flow-until-activity.md)