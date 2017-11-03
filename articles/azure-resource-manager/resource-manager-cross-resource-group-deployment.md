---
title: "Implementar os recursos do Azure a vários grupos de recursos | Microsoft Docs"
description: "Mostra como mais do que um grupo de recursos do Azure de destino durante a implementação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Implementar recursos do Azure mais do que um grupo de recursos

Normalmente, implementa todos os recursos no seu modelo para um grupo de recursos única. No entanto, existem cenários onde pretende implementar um conjunto de recursos em conjunto, mas colocá-los em grupos de recursos diferentes. Por exemplo, poderá implementar a máquina virtual de cópia de segurança do Azure Site Recovery para um grupo de recursos separada e uma localização. O Resource Manager permite-lhe utilizar modelos aninhados para grupos de recursos diferente do grupo de recursos utilizados para o modelo de principal de destino.

O grupo de recursos é o contentor de ciclo de vida da aplicação e a sua coleção de recursos. Criar o grupo de recursos fora do modelo e especifique o grupo de recursos de destino durante a implementação. Para uma introdução aos grupos de recursos, consulte [descrição geral do Azure Resource Manager](resource-group-overview.md).

## <a name="example-template"></a>Modelo de exemplo

Para um outro recurso de destino, tem de utilizar um modelo aninhado ou ligado durante a implementação. O `Microsoft.Resources/deployments` tipo de recurso fornece um `resourceGroup` parâmetro permite-lhe especificar um grupo de recursos diferente para a implementação aninhada. Todos os grupos de recursos tem de existir antes de executar a implementação. O exemplo seguinte implementa duas contas de armazenamento - um grupo de recursos especificado durante a implementação e um grupo de recursos com o nome `crossResourceGroupDeployment`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Se definir `resourceGroup` ao nome de um grupo de recursos que não existe, a implementação falhará. Se não fornecer um valor para `resourceGroup`, Gestor de recursos utiliza o grupo de recursos principais.  

## <a name="deploy-the-template"></a>Implementar o modelo

Para implementar o modelo de exemplo, pode utilizar o portal, o Azure PowerShell ou a CLI do Azure. Para o Azure PowerShell ou a CLI do Azure, tem de utilizar uma versão de Maio de 2017 ou posterior. Os exemplos partem do princípio de que guardou o modelo localmente como um ficheiro denominado **crossrgdeployment.json**.

Para o PowerShell:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Para a CLI do Azure:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

Após a conclusão da implementação, verá dois grupos de recursos. Cada grupo de recursos contém uma conta de armazenamento.

## <a name="use-resourcegroup-function"></a>Utilize a função de resourceGroup()

Para cruzada implementações do grupo de recursos, o [resouceGroup() função](resource-group-template-functions-resource.md#resourcegroup) resolve forma diferente, consoante como especificar o modelo aninhado. 

Se incorporar um modelo dentro de outro modelo, resouceGroup() no modelo aninhado é resolvido para o grupo de recursos principais. Um modelo incorporado utiliza o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Se ligar a um modelo separado, resouceGroup() no modelo ligado é resolvido para o grupo de recursos aninhados. Um modelo ligado utiliza o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>Passos seguintes

* Para compreender como definir parâmetros no modelo, consulte [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).
* Para sugestões sobre como resolver erros comuns de implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que necessita de um token SAS, consulte [implementar a modelo privado com o SAS token](resource-manager-powershell-sas-token.md).
