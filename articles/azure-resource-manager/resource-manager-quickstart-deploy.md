---
title: Implementar recursos no Azure | Microsoft Docs
description: "Utilize o Azure PowerShell ou a CLI do Azure para implementar recursos no Azure. Os recursos são definidos num modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3


---
# <a name="deploy-resources-to-azure"></a>Implementar recursos no Azure

Este tópico mostra como implementar recursos na sua subscrição do Azure. Pode utilizar o Azure PowerShell ou a CLI do Azure para implementar um modelo do Resource Manager que defina a infraestrutura para a sua solução.

Para obter uma introdução aos conceitos do Resource Manager, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Passos para a implementação

Este tópico parte do princípio de que está a implementar o [modelo de armazenamento de exemplo](#example-storage-template) neste tópico. Pode utilizar um modelo diferente, mas os parâmetros a transmitir são diferentes dos apresentados neste tópico.

Depois de criar um modelo, os passos gerais para implementar o modelo são:

1. Iniciar sessão na sua conta
2. Selecionar a subscrição a utilizar (necessário apenas se tiver várias subscrições e pretende utilizar uma que não é a subscrição predefinida)
3. Criar um grupo de recursos
4. Implementar o modelo
5. Verificar o estado da implementação

As secções seguintes mostram como executar esses passos com o [PowerShell](#powershell) ou a [CLI do Azure](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Para instalar o Azure PowerShell, veja [Introdução aos cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs).

2. Para iniciar rapidamente a implementação, utilize os seguintes cmdlets:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  O cmdlet `Set-AzureRmContext` só é necessário se pretender utilizar uma subscrição que não seja a sua subscrição predefinida. Para ver todas as suas subscrições e os respetivos IDs, utilize:

  ```powershell
  Get-AzureRmSubscription
  ```

3. A implementação pode demorar alguns minutos a concluir. Quando estiver concluída, verá uma mensagem semelhante a:

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Para ver que o seu grupo de recursos e conta de armazenamento foram implementados na sua subscrição, utilize:

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Pode especificar parâmetros de modelo como parâmetros do PowerShell quando implementar um modelo. O exemplo anterior não inclui parâmetros de modelo, pelo que foram utilizados os valores predefinidos no modelo. Para implementar outra conta de armazenamento e fornecer valores de parâmetros para o prefixo do nome do armazenamento e a SKU da conta de armazenamento, utilize:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Tem agora duas contas de armazenamento no seu grupo de recursos. 

## <a name="azure-cli"></a>CLI do Azure

1. Para instalar a CLI do Azure, veja [Instalar a CLI do Azure 2.0](/cli/azure/install-az-cli2).

2. Para iniciar rapidamente a implementação, utilize os seguintes comandos:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  O comando `az account set` só é necessário se pretender utilizar uma subscrição que não seja a sua subscrição predefinida. Para ver todas as suas subscrições e os respetivos IDs, utilize:

  ```azurecli
  az account list
  ```

3. A implementação pode demorar alguns minutos a concluir. Quando estiver concluída, verá uma mensagem semelhante a:

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Para ver que o seu grupo de recursos e conta de armazenamento foram implementados na sua subscrição, utilize:

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Pode especificar parâmetros de modelo como parâmetros do PowerShell quando implementar um modelo. O exemplo anterior não inclui parâmetros de modelo, pelo que foram utilizados os valores predefinidos no modelo. Para implementar outra conta de armazenamento e fornecer valores de parâmetros para o prefixo do nome do armazenamento e a SKU da conta de armazenamento, utilize:

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Tem agora duas contas de armazenamento no seu grupo de recursos. 

## <a name="example-storage-template"></a>Modelo de armazenamento de exemplo

Utilize o modelo de exemplo seguinte para implementar uma conta de armazenamento na sua subscrição:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações detalhadas sobre como utilizar o PowerShell para implementar modelos, veja o artigo [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Para obter informações detalhadas sobre como utilizar a CLI do Azure para implementar modelos, veja o artigo [Implementar recursos com modelos do Resource Manager e a CLI do Azure](/azure/azure-resource-manager/resource-group-template-deploy-cli).






<!--HONumber=Feb17_HO3-->


