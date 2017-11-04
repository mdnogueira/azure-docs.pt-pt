---
title: "Ativar automaticamente definições de diagnóstico com um modelo do Resource Manager | Microsoft Docs"
description: "Saiba como utilizar um modelo do Resource Manager para criar definições de diagnóstico que irão permitir-lhe transmitir os registos de diagnóstico para os Event Hubs ou armazená-las numa conta do storage."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/30/2017
ms.author: johnkem
ms.openlocfilehash: 2f764bc14e882f71957299b833d5bc1a6765622a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Ativar automaticamente definições de diagnóstico durante a criação de recursos através de um modelo do Resource Manager
Neste artigo mostramos como pode utilizar um [modelo Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para configurar definições de diagnóstico num recurso quando é criado. Isto permite-lhe iniciar automaticamente a transmissão em fluxo os registos de diagnóstico e métricas para os Event Hubs, arquivá-los numa conta de armazenamento ou enviando-as à análise de registos quando um recurso é criado.

O método para ativar os registos de diagnóstico com um modelo do Resource Manager depende do tipo de recurso.

* **Computação não** recursos (por exemplo, grupos de segurança de rede, as Logic Apps, automatização) [definições de diagnóstico descrito neste artigo](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **Computação** recursos (WAD/LAD baseado), utilize o [ficheiro de configuração de WAD/LAD descrito neste artigo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Neste artigo, vamos descrevem como configurar diagnósticos utilizando um dos métodos.

Os passos básicos são os seguintes:

1. Crie um modelo como um ficheiro JSON que descreve como criar o recurso e ative os diagnósticos.
2. [Implementar o modelo com qualquer método de implementação](../azure-resource-manager/resource-group-template-deploy.md).

Abaixo que lhe damos um exemplo do ficheiro de JSON do modelo que tem de gerar de não computação e de recursos de computação.

## <a name="non-compute-resource-template"></a>Modelo de recursos de computação não
Não-recursos de computação, terá de efetuar dois procedimentos:

1. Adicione parâmetros para o blob de parâmetros para o nome da conta de armazenamento, ID de regra de barramento de serviço, e/ou ID da área de trabalho de análise de registos do OMS (ativar o arquivo de registos de diagnóstico numa conta do storage, transmissão em fluxo de registos para os Event Hubs, e/ou enviar registos ao Log Analytics).
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the Service Bus Rule for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Na matriz de recursos do recurso para o qual pretende ativar registos de diagnóstico, adicione um recurso do tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "timeGrain": "PT1M",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

O blob de propriedades para a definição de diagnóstico segue [o formato descrito neste artigo](https://msdn.microsoft.com/library/azure/dn931931.aspx). Adicionar o `metrics` propriedade permitirá também enviar métricas de recurso para estes mesmas saídas, fornecidas que [o recurso suporta métricas de Monitor de Azure](monitoring-supported-metrics.md).

Eis um exemplo completo que cria uma aplicação lógica e ativa a transmissão em fluxo para armazenamento numa conta do storage e Hubs de eventos.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Modelo de recursos de computação
Para ativar o diagnóstico num recurso de computação, por exemplo um cluster de Máquina Virtual ou de Service Fabric, tem de:

1. Adicione a extensão de diagnóstico do Azure para a definição do recurso VM.
2. Especifique um concentrador de conta e/ou evento de armazenamento como um parâmetro.
3. Adicione o conteúdo do ficheiro XML de WADCfg para a propriedade de XMLCfg escape todos os carateres XML corretamente.

> [!WARNING]
> Neste último passo pode ser tricky obter à direita. [Consulte este artigo](../virtual-machines/windows/extensions-diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o esquema de configuração de diagnósticos para variáveis de escape e formatadas corretamente.
> 
> 

O processo completo, incluindo exemplos, está descrito [neste documento](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes
* [Leia mais sobre os registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
* [Transmitir os registos de diagnóstico do Azure para os Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)

