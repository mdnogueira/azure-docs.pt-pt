---
title: Criar um alerta de registo de atividade com um modelo do Resource Manager | Microsoft Docs
description: "Seja notificado quando são criados os recursos do Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: 92076c7fe1f867919b7e02abf79cf0fb74fb7eb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Criar um alerta de registo de atividade com um modelo do Resource Manager
Este artigo mostra como utilizar um [modelo Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) para configurar alertas de registo de atividade. Utilizando os modelos, pode facilmente definir muitos alertas que ativar com base nas condições de eventos de registo de atividade específica como parte do processo de implementação automática.

Os passos básicos são:

1. Crie um modelo como um ficheiro JSON que descreve como criar o alerta de registo de atividade.

2. Implementar o modelo utilizando [qualquer método de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modelo do Resource Manager para um alerta de registo de atividade
Para criar um alerta de registo de atividade, utilizando um modelo do Resource Manager, criar um recurso do tipo `microsoft.insights/activityLogAlerts`. Em seguida, tem de preencher todas as propriedades relacionadas. Segue-se um modelo que cria um alerta de registo de atividade.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Visite a nossa [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) para alguns exemplos de modelos de alerta de registo de atividade.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [alertas](monitoring-overview-alerts.md).
- Saiba como adicionar [grupos ação utilizando um modelo do Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).
- Saiba como [criar um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Saiba como [criar um alerta de registo de atividade para monitorizar todas as operações de escala-na/escalável de dimensionamento automático falhou na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
