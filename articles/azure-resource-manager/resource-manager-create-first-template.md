---
title: Criar o primeiro modelo do Azure Resource Manager | Microsoft Docs
description: "Um guia passo a passo para criar o primeiro modelo do Azure Resource Manager. Mostra como utilizar a referência de modelo para uma conta de armazenamento para criar o modelo."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Criar o primeiro modelo do Azure Resource Manager
Este tópico explica os passos de criação do primeiro modelo do Azure Resource Manager. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](resource-group-overview.md). Se dispõe de recursos existentes e pretende obter um modelo para esses recursos, veja [Exportar um modelo do Azure Resource Manager a partir de recursos existentes](resource-manager-export-template.md).

Para criar e rever modelos, é necessário um editor de JSON. O [Visual Studio Code](https://code.visualstudio.com/) é um editor de código simples, open source para várias plataformas. Suporta a criação e edição de modelos do Resource Manager através de um extensão. Este tópico parte do princípio de que está a utilizar o Código VS. No entanto, se tiver outro editor de JSON (por exemplo, o Visual Studio), pode utilizar esse editor.

## <a name="get-vs-code-and-extension"></a>Obter o Código VS e a extensão
1. Se necessário, instale o Código VS a partir de [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Instale a extensão [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools), acedendo a Quick Open (Ctrl+P) e executando: 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. Reinicie o VS Code quando lhe for pedido para ativar a extensão.

## <a name="create-blank-template"></a>Criar um modelo em branco

Vamos começar com um modelo em branco que inclui apenas as secções básicas de um modelo.

1. Crie um ficheiro. 

2. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. Guarde este ficheiro como **azuredeploy.json**. 

## <a name="add-storage-account"></a>Adicionar conta de armazenamento
1. Para definir uma conta de armazenamento para implementação, adicione essa conta de armazenamento à secção **resources** do seu modelo. Para localizar os valores que estão disponíveis para a conta de armazenamento, consulte a [referência de modelo de contas de armazenamento](/azure/templates/microsoft.storage/storageaccounts). Copie o JSON que é apresentado para a conta de armazenamento. 

3. Cole esse JSON na secção **resources** do seu modelo, conforme apresentado no exemplo seguinte: 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-05-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  O exemplo anterior inclui muitos valores de marcador de posição e algumas propriedades que poderão não ser necessárias na sua conta de armazenamento.

## <a name="set-values-for-storage-account"></a>Definir valores para a conta de armazenamento

Agora, está pronto para definir valores para a sua conta de armazenamento. 

1. Observe novamente a [referência de modelo de contas de armazenamento](/azure/templates/microsoft.storage/storageaccounts) onde copiou o JSON. Existem várias tabelas que descrevem as propriedades e fornecem os valores disponíveis. 

2. Repare que dentro do elemento **properties**, os valores **customDomain**, **encryption** e **accessTier** estão todos indicados como não necessários. Estes valores podem ser importantes para os seus cenários, mas vamos removê-los para manter este exemplo simples.

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-05-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. Atualmente, o elemento **kind** está definido como um valor de marcador de posição ("string"). O Código VS inclui muitas funcionalidades que ajudam a compreender os valores a utilizar no seu modelo. Tenha em atenção que o Código VS indica que este valor não é válido. Se passar o rato sobre "string", o Código VS sugere que os valores válidos para **kind** são `Storage` ou `BlobStorage`. 

   ![mostrar os valores sugeridos pelo Código VS](./media/resource-manager-create-first-template/vs-code-show-values.png)

   Para ver os valores disponíveis, elimine os carateres entre aspas e selecione **Ctrl+Espaço**. Selecione **Armazenamento** nas opções disponíveis.
  
   ![mostrar o intellisense](./media/resource-manager-create-first-template/intellisense.png)

   Se não estiver a utilizar o Código VS, observe a página de referência do modelo de contas de armazenamento. Repare que a descrição indica os mesmos valores válidos. Defina o elemento como **Armazenamento**.

   ```json
   "kind": "Storage",
   ```

Agora, o seu modelo tem este aspeto:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Adicionar funções ao modelo

Utilize funções no seu modelo para simplificar a sintaxe do modelo e para obter valores que só estão disponíveis quando o modelo está a ser implementado. Para obter o conjunto completo de funções de modelo, veja o artigo [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).

Para especificar que a conta de armazenamento está implementada na mesma localização que o grupo de recursos, defina a propriedade **location** como:

```json
"location": "[resourceGroup().location]",
```

Novamente, o Código VS ajuda-o ao sugerir funções disponíveis. 

![mostrar funções](./media/resource-manager-create-first-template/show-functions.png)

Repare que a função está entre parênteses retos. A função [resourceGroup](resource-group-template-functions.md#resourcegroup) devolve um objeto com uma propriedade chamada `location`. O grupo de recursos contém todos os recursos relacionados para a sua solução. Podia codificar a propriedade de localização para um valor como "E.U.A. Central", mas teria de alterar manualmente o modelo para voltar a implementar numa localização diferente. Com a função `resourceGroup`, é fácil voltar a implementar este modelo num grupo de recursos diferente numa localização diferente.

Agora, o seu modelo tem este aspeto:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
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

## <a name="add-parameters-and-variables"></a>Adicionar parâmetros e variáveis
Só falta definir dois valores no seu modelo - **name** e **sku.name**. Para estas propriedades, adicione parâmetros que lhe permitem personalizar estes valores durante a implementação. 

Os nomes das contas de armazenamento têm várias restrições que dificultam a respetiva definição. O nome tem de ter entre 3 e 24 carateres, utilizar apenas números e letras minúsculas e ser exclusivo. Em vez de tentar adivinhar um valor exclusivo que corresponda às restrições, utilize a função [uniqueString](resource-group-template-functions.md#uniquestring) para gerar um valor hash. Para atribuir mais significado a este valor hash, adicione um prefixo que o ajude a identificá-lo como uma conta de armazenamento após a implementação. 

1. Para introduzir um prefixo para o nome que corresponda às suas convenções de nomenclatura, aceda à secção **parameters** do seu modelo. Adicione um parâmetro ao modelo que aceite um prefixo para o nome da conta de armazenamento:

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  O prefixo está limitado a um máximo de 11 carateres porque `uniqueString` devolve 13 carateres e o nome não pode exceder os 24 carateres. Se não introduzir um valor para o parâmetro durante a implementação, será utilizado o valor predefinido.

2. Aceda à secção **variables** do modelo. Para construir o nome a partir do prefixo e da cadeia única, adicione a seguinte variável:

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. Na secção **resources**, defina a conta de armazenamento para essa variável.

   ```json
   "name": "[variables('storageName')]",
   ```

3. Para ativar a introdução de SKUs diferentes para a conta de armazenamento, aceda à secção **parameters**. A seguir ao parâmetro para o prefixo de nome de armazenamento, adicione um parâmetro que especifique os valores de SKU permitidos e um valor predefinido. Pode encontrar os valores permitidos na página de referência do modelo ou Código VS. No exemplo seguinte, vai incluir todos os valores válidos para SKU. No entanto, pode limitar os valores permitidos para apenas os tipos de SKUs que pretende implementar através deste modelo.

   ```json
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
   ```

3. Altere a propriedade SKU para utilizar o valor do parâmetro:

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. Guarde o ficheiro.

Agora, o seu modelo tem este aspeto:

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
* O modelo está concluído e está preparado para ser implementado na sua subscrição. Para implementar, veja [Implementar recursos no Azure](resource-manager-quickstart-deploy.md).
* Para saber mais sobre a estrutura de um modelo, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

