---
title: Implementar recursos com o PowerShell e o modelo | Microsoft Docs
description: "Utilize o Azure Resource Manager e o Azure PowerShell para implementar um recursos no Azure. Os recursos são definidos num modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.openlocfilehash: 724c03bd360cef5548a3460263ec39a6ca791d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementar recursos com modelos do Resource Manager e do Azure PowerShell

Este tópico explica como utilizar o Azure PowerShell com modelos do Resource Manager para implementar os recursos no Azure. Se não estiver familiarizado com conceitos de implementar e gerir as suas soluções do Azure, consulte [descrição geral do Azure Resource Manager](resource-group-overview.md).

O modelo do Resource Manager que implementar possível ser um ficheiro local no seu computador, ou um ficheiro externo que está localizado num repositório como GitHub. O modelo que implementa neste artigo está disponível no [modelo de exemplo](#sample-template) secção, ou como [modelo de conta de armazenamento no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Implementar um modelo a partir do seu computador local

Quando implementar recursos para o Azure, pode:

1. Inicie sessão na sua conta do Azure
2. Crie um grupo de recursos que funciona como o contentor para os recursos implementados. O nome do grupo de recursos só pode incluir carateres alfanuméricos, pontos, carateres de sublinhado, hífenes e parênteses. Pode ser até 90 carateres. Não pode terminar num período.
3. Implementar o modelo que define os recursos para criar o grupo de recursos

Um modelo pode incluir parâmetros que permitem-lhe personalizar a implementação. Por exemplo, pode fornecer valores que são adaptados para um ambiente específico (por exemplo, o desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para a conta de armazenamento SKU.

O exemplo seguinte cria um grupo de recursos e implementa um modelo a partir do seu computador local:

```powershell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

A implementação pode demorar alguns minutos a concluir. Quando terminar, verá uma mensagem que inclui o resultado:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Implementar um modelo a partir de uma origem externa

Em vez de armazenar modelos do Resource Manager no seu computador local, pode preferir armazená-las numa localização externa. Pode armazenar modelos num repositório de controlo de origem (por exemplo, o GitHub). Em alternativa, pode armazená-las numa conta de armazenamento do Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o **TemplateUri** parâmetro. Utilize o URI de exemplo para implementar o modelo de exemplo a partir do GitHub.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários, porque o modelo não deve incluir dados confidenciais. Se tiver de especificar os dados confidenciais (por exemplo, uma palavra-passe de administrador), passe esse valor como um parâmetro seguro. No entanto, se não pretender que o modelo para estar acessível publicamente, pode protegê-lo armazenando-o num contentor de armazenamento privada. Para obter informações sobre a implementação de um modelo que necessita de um token de assinatura (SAS) de acesso partilhado, consulte [implementar a modelo privado com o SAS token](resource-manager-powershell-sas-token.md).

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

Para transferir um ficheiro de parâmetros local, utilizar o **TemplateParameterFile** parâmetro:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para transferir um ficheiro de parâmetros externos, utilize o **TemplateParameterUri** parâmetro:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Pode utilizar parâmetros inline e um ficheiro de parâmetros local na mesma operação de implementação. Por exemplo, pode especificar alguns valores no ficheiro de parâmetros local e adicionar outro inline valores durante a implementação. Se fornecer valores para um parâmetro no ficheiro de parâmetros local e inline, o valor de inline tem precedência.

No entanto, quando utiliza um ficheiro de parâmetros externos, não poder passar outros valores ou inline ou a partir de um ficheiro local. Quando especificar um ficheiro de parâmetros nos **TemplateParameterUri** parâmetro, inline todos os parâmetros são ignorados. Fornece todos os valores de parâmetros no ficheiro externo. Se o seu modelo incluir um valor confidencial que não pode incluir no ficheiro de parâmetros, adicione esse valor para um cofre de chaves ou fornecer dinamicamente a todas as inline de valores de parâmetro.

Se o modelo inclui um parâmetro com o mesmo nome que um dos parâmetros de comando do PowerShell, o PowerShell apresenta o parâmetro do seu modelo com o sufixo **FromTemplate**. Por exemplo, um parâmetro com o nome **ResourceGroupName** no seu conflitos de modelo com o **ResourceGroupName** parâmetro o [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet. É-lhe pedido que forneça um valor para **ResourceGroupNameFromTemplate**. Em geral, deve evitar este confusão ao nomear não parâmetros com o mesmo nome de parâmetros utilizada para operações de implementação.

## <a name="test-a-template-deployment"></a>Testar um modelo de implementação

Para testar os valores de parâmetros de modelo e sem a implementar, na verdade, quaisquer recursos, utilize [teste-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Se forem detetados sem erros, o comando terminar sem uma resposta. Se for detetado um erro, o comando devolve uma mensagem de erro. Por exemplo, a tentar transmitir um valor incorreto para a conta de armazenamento SKU e devolve o erro seguinte:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se o seu modelo tem um erro de sintaxe, o comando devolve um erro que indica que não foi possível analisar o modelo. A mensagem indica o número de linha e a posição do erro de análise.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para utilizar o modo concluído, utilize o `Mode` parâmetro:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
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
* Os exemplos neste artigo implementar recursos num grupo de recursos na sua subscrição predefinida. Para utilizar uma subscrição diferente, consulte [gerir várias subscrições do Azure](/powershell/azure/manage-subscriptions-azureps).
* Para um script de exemplo completo que implementa um modelo, consulte [script de implementação de modelos do Resource Manager](resource-manager-samples-powershell-deploy.md).
* Para compreender como definir parâmetros no modelo, consulte [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).
* Para sugestões sobre como resolver erros comuns de implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que necessita de um token SAS, consulte [implementar a modelo privado com o SAS token](resource-manager-powershell-sas-token.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

