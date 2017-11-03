---
title: "Localização de recursos do Azure no modelo | Microsoft Docs"
description: "Mostra como definir uma localização para um recurso de um modelo Azure Resource Manager"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Localização do conjunto de recursos em modelos do Azure Resource Manager
Quando implementar um modelo, tem de fornecer uma localização para cada recurso. Este tópico mostra como determinar as localizações que estão disponíveis para a sua subscrição para cada tipo de recurso.

## <a name="determine-supported-locations"></a>Determinar as localizações suportadas

Para uma lista completa das localizações suportadas para cada tipo de recurso, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/). No entanto, a subscrição poderá não ter acesso a todas as localizações que lista. Para ver uma lista personalizada de localizações que estão disponíveis para a sua subscrição, utilize o Azure PowerShell ou a CLI do Azure. 

O exemplo seguinte utiliza o PowerShell para obter as localizações para o `Microsoft.Web\sites` tipo de recurso:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

O exemplo seguinte utiliza o Azure CLI 2.0 para obter as localizações para o `Microsoft.Web\sites` tipo de recurso:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>Definir localização do modelo

Após determinar as localizações suportadas para os seus recursos, tem de definir essa localização no seu modelo. A forma mais fácil para definir este valor é criar um grupo de recursos numa localização que suporta os tipos de recursos e como cada localização `[resourceGroup().location]`. Pode implementar novamente o modelo para grupos de recursos em localizações diferentes e não alterar quaisquer valores no modelo ou de parâmetros. 

O exemplo seguinte mostra uma conta de armazenamento que é implementada a mesma localização que o grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Se precisar que codifique os a localização no seu modelo, forneça o nome de uma das regiões suportadas. O exemplo seguinte mostra uma conta de armazenamento sempre implementar Norte Central-na:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes
* Para ver as recomendações sobre como criar modelos, consulte [melhores práticas para a criação de modelos Azure Resource Manager](resource-manager-template-best-practices.md).

