---
title: Criar o primeiro modelo do Azure Resource Manager | Microsoft Docs
description: "Um guia passo a passo para criar o primeiro modelo do Azure Resource Manager. Mostra como utilizar a referência de modelo para uma conta de armazenamento para criar o modelo."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: pt-pt
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Criar e implementar o seu primeiro modelo do Azure Resource Manager
Este tópico explica os passos de criação do primeiro modelo do Azure Resource Manager. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md). Se dispõe de recursos existentes e pretende obter um modelo para esses recursos, veja [Exportar um modelo do Azure Resource Manager a partir de recursos existentes](resource-manager-export-template.md).

Para criar e rever modelos, é necessário um editor de JSON. O [Visual Studio Code](https://code.visualstudio.com/) é um editor de código simples, open source para várias plataformas. Recomendamos vivamente que utilize o Visual Studio Code para criar modelos do Resource Manager. Este tópico parte do princípio de que está a utilizar o Código VS. No entanto, se tiver outro editor de JSON (por exemplo, o Visual Studio), pode utilizar esse editor.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio Code. Se for preciso, instale-o a partir do site [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-template"></a>Criar o modelo

Vamos começar com um modelo simples que implementa uma conta de armazenamento na sua subscrição.

1. Selecione **Ficheiro** > **Novo Ficheiro**. 

   ![Novo ficheiro](./media/resource-manager-create-first-template/new-file.png)

2. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Os nomes das contas de armazenamento têm várias restrições que dificultam a respetiva definição. O nome tem de ter entre 3 e 24 carateres, utilizar apenas números e letras minúsculas e ser exclusivo. O modelo anterior utiliza a função [uniqueString](resource-group-template-functions-string.md#uniquestring) para gerar um valor hash. Para atribuir um significado mais amplo a este valor hash, adiciona o prefixo *storage*. 

3. Guarde este ficheiro com o nome **azuredeploy.json** numa pasta local.

   ![Guardar o modelo](./media/resource-manager-create-first-template/save-template.png)

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

Uma vez concluída a implementação, a sua conta de armazenamento passa a estar presente no grupo de recursos.

## <a name="deploy-template-from-cloud-shell"></a>Implementar o modelo a partir do Cloud Shell

Pode utilizar o [Cloud Shell](../cloud-shell/overview.md) para executar os comandos da CLI do Azure para implementar o modelo. No entanto, primeiro tem de carregar o modelo na partilha de ficheiros do Cloud Shell. Se não utilizou o Cloud Shell, consulte a [Descrição Geral do Azure Cloud Shell](../cloud-shell/overview.md) para obter informações sobre como o configurar.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).   

2. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar grupo de recursos](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Selecione a conta de armazenamento para o Cloud Shell.

   ![Selecionar a conta de armazenamento](./media/resource-manager-create-first-template/select-storage.png)

4. Selecione **Ficheiros**.

   ![Selecionar ficheiros](./media/resource-manager-create-first-template/select-files.png)

5. Selecione a partilha de ficheiros para o Cloud Shell. O padrão de nome é `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selecionar a partilha de ficheiros](./media/resource-manager-create-first-template/select-file-share.png)

6. Selecione **Adicionar diretório**.

   ![Adicionar diretório](./media/resource-manager-create-first-template/select-add-directory.png)

7. Atribua-lhe o nome **templates** e selecione **OK**.

   ![Atribuir um nome ao diretório](./media/resource-manager-create-first-template/name-templates.png)

8. Selecione o novo diretório.

   ![Selecionar o diretório](./media/resource-manager-create-first-template/select-templates.png)

9. Selecione **Upload**.

   ![Selecionar a operação carregar](./media/resource-manager-create-first-template/select-upload.png)

10. Localize e carregue o modelo.

   ![Carregar o ficheiro](./media/resource-manager-create-first-template/upload-files.png)

11. Abra o prompt.

   ![Abrir o Cloud Shell](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. Introduza os seguintes comandos no Cloud Shell:

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
   ```

Uma vez concluída a implementação, a sua conta de armazenamento passa a estar presente no grupo de recursos.

## <a name="customize-the-template"></a>Personalizar o modelo

O modelo é bastante funcional, mas não é flexível. Implementa sempre um armazenamento localmente redundante na região South Central US. O nome é sempre *storage* seguido de um valor hash. Para permitir a utilização do modelo noutros cenários, adicione parâmetros ao modelo.

O exemplo seguinte mostra a secção de parâmetros com dois parâmetros. O primeiro parâmetro `storageSKU` permite-lhe especificar o tipo de redundância. Limita os valores que pode transmitir a valores que são válidos para uma conta de armazenamento. Também especifica um valor predefinido. O segundo parâmetro `storageNamePrefix` está definido para permitir um máximo de 11 carateres. Especifica um valor predefinido.

```json
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
```

Na secção de variáveis, adicione uma variável com o nome `storageName`. Alia o valor do prefixo dos parâmetros a um valor hash da função [uniqueString](resource-group-template-functions-string.md#uniquestring). Utiliza a função [toLower](resource-group-template-functions-string.md#tolower) para converter todos os carateres em minúsculas.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Para utilizar estes novos valores para a sua conta de armazenamento, altere a definição do recurso:

```json
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
    "properties": {}
  }
],
```

Tenha em atenção que o nome da conta de armazenamento está agora definido para a variável que adicionou. O nome do SKU está definido para o valor do parâmetro. A localização está definida para a mesma localização que o grupo de recursos.

Guarde o ficheiro. 

Depois de concluir os passos neste artigo, o modelo tem este aspeto:

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
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Reimplementar o modelo

Reimplemente o modelo com outros valores.

Para o PowerShell, utilize:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Para a CLI do Azure, utilize:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Para o Cloud Shell, carregue o modelo alterado na partilha de ficheiros. Substitua o ficheiro existente. Em seguida, utilize o seguinte comando:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

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

