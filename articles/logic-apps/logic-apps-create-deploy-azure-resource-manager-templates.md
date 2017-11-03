---
title: Criar as logic apps a partir de modelos Azure Resource Manager | Microsoft Docs
description: "Criar e implementar os fluxos de trabalho de aplicação de lógica com modelos Azure Resource Manager"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; mandia
ms.openlocfilehash: e30ed8b1b8e2241bbebab1d7c5f337fabf37e1dd
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Criar e implementar aplicações lógicas com modelos Azure Resource Manager

Aplicações lógicas do Azure fornece modelos Azure Resource Manager que pode utilizar, não só para aplicações lógicas para automatizar fluxos de trabalho, mas também para definir os parâmetros que são utilizados para a implementação e de recursos. Pode utilizar este modelo para os seus próprios cenários de negócios ou personalizar o modelo para satisfazer os seus requisitos. Saiba mais sobre o [modelo do Resource Manager para aplicações lógicas](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) e [estrutura de modelo do Azure Resource Manager e sintaxe](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definir a aplicação lógica

Esta definição de aplicação de lógica de exemplo é executada uma vez a uma hora e a localização especificada na efetua o ping a `testUri` parâmetro.
O modelo utiliza valores de parâmetros para o nome da aplicação lógica (```logicAppName```) e a localização para enviar um ping para fins de teste (```testUri```). Saiba mais sobre [definir estes parâmetros no modelo](#define-parameters). O modelo define também a localização para a aplicação lógica para a mesma localização que o grupo de recursos do Azure. 

``` json
{
    "type": "Microsoft.Logic/workflows",
    "apiVersion": "2016-06-01",
    "name": "[parameters('logicAppName')]",
    "location": "[resourceGroup().location]",
    "tags": {
        "displayName": "LogicApp"
    },
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
                "Recurrence": {
                    "type": "Recurrence",
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            },
            "actions": {
                "Http": {
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
    }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>definir os parâmetros

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Seguem-se descrições para os parâmetros no modelo:

| Parâmetro | Descrição | Exemplo de definição de JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Define o nome da aplicação lógica que o modelo cria. | "logicAppName": {"type": "cadeia", "metadados": {"Descrição": "myExampleLogicAppName"}} |
| `testUri` | Define a localização para enviar um ping para fins de teste. | "testUri": {"type": "cadeia", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

Saiba mais sobre [API REST para a definição de fluxo de trabalho de aplicações de lógica e propriedades](https://docs.microsoft.com/rest/api/logic/workflows) e [tendo definições da aplicação lógica com JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Implementar automaticamente as logic apps

Para criar e implementar automaticamente uma aplicação lógica para o Azure, escolha **implementar no Azure** aqui:

[![Implementar no Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Esta ação inicia o portal do Azure, onde pode fornecer a lógica de detalhes da aplicação e fazer alterações ao modelo ou de parâmetros. Por exemplo, o portal do Azure pede-lhe estes detalhes:

* Nome da subscrição do Azure
* Grupo de recursos que pretende utilizar
* Localização da aplicação lógica
* Um nome para a sua aplicação lógica
* Um URI de teste
* Aceitação dos termos especificados e condições

## <a name="deploy-logic-apps-with-commands"></a>Implementar aplicações lógicas com comandos

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>CLI do Azure

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)