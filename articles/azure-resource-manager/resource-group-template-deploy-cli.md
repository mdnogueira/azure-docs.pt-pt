---
title: Implementar recursos com a CLI do Azure e o modelo | Microsoft Docs
description: "Utilize o Azure Resource Manager e a CLI do Azure para implementar um recursos no Azure. Os recursos são definidos num modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: 13154e41ebd4867de9af74340a69446400814f5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementar recursos com modelos do Resource Manager e do CLI do Azure

Este tópico explica como utilizar o Azure CLI 2.0 com modelos do Resource Manager para implementar os recursos no Azure. Se não estiver familiarizado com conceitos de implementar e gerir as suas soluções do Azure, consulte [descrição geral do Azure Resource Manager](resource-group-overview.md).  

O modelo do Resource Manager que implementar possível ser um ficheiro local no seu computador, ou um ficheiro externo que está localizado num repositório como GitHub. O modelo que implementa neste artigo está disponível no [modelo de exemplo](#sample-template) secção, ou como um [modelo de conta de armazenamento no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Se não tiver a CLI do Azure instalado, pode utilizar o [nuvem Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Implementar a modelo local

Quando implementar recursos para o Azure, pode:

1. Inicie sessão na sua conta do Azure
2. Crie um grupo de recursos que funciona como o contentor para os recursos implementados. O nome do grupo de recursos só pode incluir carateres alfanuméricos, pontos, carateres de sublinhado, hífenes e parênteses. Pode ser até 90 carateres. Não pode terminar num período.
3. Implementar o modelo que define os recursos para criar o grupo de recursos

Um modelo pode incluir parâmetros que permitem-lhe personalizar a implementação. Por exemplo, pode fornecer valores que são adaptados para um ambiente específico (por exemplo, o desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para a conta de armazenamento SKU. 

O exemplo seguinte cria um grupo de recursos e implementa um modelo a partir do seu computador local:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

A implementação pode demorar alguns minutos a concluir. Quando terminar, verá uma mensagem que inclui o resultado:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Implementar a modelo externo

Em vez de armazenar modelos do Resource Manager no seu computador local, pode preferir armazená-las numa localização externa. Pode armazenar modelos num repositório de controlo de origem (por exemplo, o GitHub). Em alternativa, pode armazená-las numa conta de armazenamento do Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o **modelo uri** parâmetro. Utilize o URI de exemplo para implementar o modelo de exemplo a partir do GitHub.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários, porque o modelo não deve incluir dados confidenciais. Se tiver de especificar os dados confidenciais (por exemplo, uma palavra-passe de administrador), passe esse valor como um parâmetro seguro. No entanto, se não pretender que o modelo para estar acessível publicamente, pode protegê-lo armazenando-o num contentor de armazenamento privada. Para obter informações sobre a implementação de um modelo que necessita de um token de assinatura (SAS) de acesso partilhado, consulte [implementar a modelo privado com o SAS token](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Na Shell de nuvem, utilize os seguintes comandos:

   ```azurecli-interactive
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageAccountType=Standard_GRS
   ```

## <a name="parameter-files"></a>Ficheiros de parâmetro

Em vez de a transmitir parâmetros como valores de inline no script, poderá considerar mais fáceis de utilizar um ficheiro JSON que contém os valores de parâmetros. O ficheiro de parâmetros tem de estar no seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Tenha em atenção que a secção de parâmetros inclui um nome de parâmetro que corresponda ao parâmetro definido no seu modelo (storageAccountType). O ficheiro de parâmetros contém um valor para o parâmetro. Este valor é automaticamente transferido para o modelo durante a implementação. Pode criar vários ficheiros de parâmetro para diferentes cenários de implementação e, em seguida, passar o ficheiro de parâmetros adequados. 

Copie o exemplo anterior e guarde-o como um ficheiro denominado `storage.parameters.json`.

Para transferir um ficheiro de parâmetros local, utilizar `@` para especificar um ficheiro local com o nome storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testar um modelo de implementação

Para testar os valores de parâmetros de modelo e sem a implementar, na verdade, quaisquer recursos, utilize [validar a implementação do grupo az](/cli/azure/group/deployment#validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Se forem detetados sem erros, o comando devolve informações sobre a implementação de teste. Em particular, tenha em atenção que o **erro** valor é nulo.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Se for detetado um erro, o comando devolve uma mensagem de erro. Por exemplo, a tentar transmitir um valor incorreto para a conta de armazenamento SKU e devolve o erro seguinte:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Se o seu modelo tem um erro de sintaxe, o comando devolve um erro que indica que não foi possível analisar o modelo. A mensagem indica o número de linha e a posição do erro de análise.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para utilizar o modo concluído, utilize o `mode` parâmetro:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Modelo de exemplo

O modelo seguinte é utilizado para os exemplos neste tópico. Copie e guarde-o como um ficheiro denominado storage.json. Para compreender como criar este modelo, consulte [criar o primeiro modelo Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Passos seguintes
* Os exemplos neste artigo implementar recursos num grupo de recursos na sua subscrição predefinida. Para utilizar uma subscrição diferente, consulte [gerir várias subscrições do Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Para um script de exemplo completo que implementa um modelo, consulte [script de implementação de modelos do Resource Manager](resource-manager-samples-cli-deploy.md).
* Para compreender como definir parâmetros no modelo, consulte [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).
* Para sugestões sobre como resolver erros comuns de implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que necessita de um token SAS, consulte [implementar a modelo privado com o SAS token](resource-manager-cli-sas-token.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).
