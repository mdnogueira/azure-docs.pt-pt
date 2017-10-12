---
title: "Modelo do Azure Resource Manager com a extensão do VS Code | Microsoft Docs"
description: "Utilize a extensão Azure Resource Manager Tools para trabalhar em modelos do Resource Manager."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: fbc0fd12999c9085c3c364f0d7115eb1ab1ddd74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>Utilizar a extensão do Visual Studio Code para criar modelos do Azure Resource Manager
Este artigo mostra as vantagens de instalar e utilizar a extensão Azure Resource Manager Tools no Visual Studio Code. Pode criar modelos do Resource Manager no VS Code sem a extensão, mas esta proporciona opções de conclusão automática que simplificam o desenvolvimento dos modelos. Sugere funções, parâmetros e variáveis que estão disponíveis nos modelos.

Para concluir este artigo, precisa do [Visual Studio Code](https://code.visualstudio.com/).

Para compreender os conceitos associados à implementação e gestão das suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md).

## <a name="create-the-template"></a>Criar o modelo

Este artigo baseia-se no modelo que criou em [Criar e implementar o seu primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md). Se já tiver este modelo, pode ignorar esta secção.

1. Se precisar de criar o modelo, inicie o VS Code. Selecione **Ficheiro** > **Novo Ficheiro**. 

   ![Novo ficheiro](./media/resource-manager-vscode-extension/new-file.png)

2. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
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
       },   
       "storageNamePrefix": {
         "type": "string",
         "maxLength": 11,
         "defaultValue": "storage",
         "metadata": {
           "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
         }
       }
     },
     "variables": {
       "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
     },
     "resources": [
       {
         "name": "[variables('storageName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "[parameters('storageSKU')]"
         },
         "kind": "Storage",
         "location": "[resourceGroup().location]",
         "tags": {},
         "properties": {
           "encryption":{
             "services":{
               "blob":{
                 "enabled":true
               }
             },
             "keySource":"Microsoft.Storage"
           }
         }
       }
     ],
     "outputs": {  }
   }
   ```

3. Guarde este ficheiro com o nome **azuredeploy.json** numa pasta local.

   ![Guardar o modelo](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>Instalar a extensão

1. No VS Code, selecione **Extensões**.

   ![Selecionar extensões](./media/resource-manager-vscode-extension/select-extensions.png)

2. Procure **Azure Resource Manager Tools** e selecione**Instalar**.

   ![Instalar a extensão](./media/resource-manager-vscode-extension/install-extension.png)

3. Para concluir a instalação da extensão, selecione **Recarregar**.

## <a name="edit-the-template"></a>Editar o modelo

1. Abra o ficheiro azuredeploy.json.

2. A extensão obtém todas as [funções de modelo](resource-group-template-functions.md) disponíveis. Também lê os parâmetros e as variáveis que definiu no modelo. Para ver esta funcionalidade, adicione dois valores à secção de saída. No modelo, substitua a secção de saídas por:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. Coloque o cursor entre as aspas para o valor em **groupLocation**. Escreva o parênteses reto de abertura (`[`). Repare que a extensão sugere imediatamente as funções de modelo disponíveis.

   ![Mostrar as funções disponíveis](./media/resource-manager-vscode-extension/available-functions.png)

4. Comece a escrever **resourceGroup**. Quando a função `resourceGroup()` for apresentada, prima Tab ou Enter.

   ![Selecionar funções de resourceGroup](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. A extensão preenche a sintaxe da função. A função [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) não aceita parâmetros. Adicione um período a seguir ao parênteses de fecho. A extensão fornece as propriedades que estão disponíveis para o objeto que a função `resourceGroup()` devolveu. Selecione `location`.

   ![Selecionar propriedades](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. A seguir **location** (localização), adicione o parênteses reto de fecho.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. Agora, coloque o cursor dentro das aspas para **storageUri**. Volte a introduzir o parênteses reto de abertura. Comece a escrever **reference**. Quando esta função for selecionada, prima Tab ou Enter.

   ![Selecionar referência](./media/resource-manager-vscode-extension/add-reference.png)

8. [Reference](resource-group-template-functions-resource.md#reference) aceita o ID de recurso ou o nome de recurso como parâmetro. Já tem o nome da conta de armazenamento numa variável. Escreva **var** e, em seguida, selecione Ctrl + espaço. A extensão sugere a função variables.

   ![Selecionar a variável](./media/resource-manager-vscode-extension/add-variable.png)

   Prima Tab ou Enter.

9. A função [variables](resource-group-template-functions-deployment.md#variables) requer o nome da variável. Dentro dos parênteses, adicione uma plica. A extensão fornece os nomes das variáveis que definiu no modelo.

   ![Mostrar variáveis](./media/resource-manager-vscode-extension/show-variables.png) 

10. Selecione a variável **storageName**. Adicione o parêntesis reto de fecho. O exemplo seguinte mostra a secção de saídas:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(variables('storageName'))]"
       }
   }
   ```

O modelo final é:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageName'))]"
    }
  }
}
```

## <a name="deploy-template"></a>Implementar o modelo

Está pronto para implementar este modelo. Utilize o PowerShell ou a CLI do Azure para criar um grupo de recursos. Em seguida, implemente uma conta de armazenamento nesse grupo de recursos.

* Para o PowerShell, utilize os seguintes comandos a partir da pasta que contém o modelo:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Para uma instalação local da CLI do Azure, utilize os seguintes comandos a partir da pasta que contém o modelo:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Após a conclusão da implementação, os valores da saída são devolvidos.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, limpe os recursos implementados eliminando o grupo de recursos.

Para o PowerShell, utilize:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Para a CLI do Azure, utilize:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a estrutura de um modelo, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre as propriedades de uma conta de armazenamento, consulte a [referência do modelo de contas de armazenamento](/azure/templates/microsoft.storage/storageaccounts).
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
