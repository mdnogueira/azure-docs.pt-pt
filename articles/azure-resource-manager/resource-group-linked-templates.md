---
title: "Ligação a modelos de implementação do Azure | Microsoft Docs"
description: "Descreve como utilizar modelos ligados num modelo Azure Resource Manager para criar uma solução de modelo modulares. Mostra como passar valores de parâmetros, especifique um ficheiro de parâmetros e URLs criados dinamicamente."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Utilizar modelos ligados ao implementar os recursos do Azure
De dentro de um modelo Azure Resource Manager, pode ligar a outro modelo permite-lhe decompor a implementação para um conjunto de destino, modelos de finalidade específica. Tal como acontece com decomposing uma aplicação em várias classes de código, decomposição proporciona vantagens em termos de teste, reutilização e facilitar a leitura.  

Pode passar os parâmetros a partir de um modelo principal para um modelo ligado e esses parâmetros diretamente podem mapear para parâmetros ou variáveis expostas pelo modelo de chamada. O modelo ligado também pode passar de uma variável de saída para o modelo de origem, ativar uma troca de dados bidirecional entre os modelos.

## <a name="linking-to-a-template"></a>Ligar a um modelo
Criar uma ligação entre dois modelos através da adição de um recurso de implementação no modelo principal que aponta para o modelo ligado. Definir o **templateLink** propriedade para o URI do modelo ligado. Pode fornecer valores de parâmetros para o modelo ligado diretamente no seu modelo ou um ficheiro de parâmetros. O exemplo seguinte utiliza o **parâmetros** propriedade para especificar um valor de parâmetro diretamente.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Como outros tipos de recursos, pode definir as dependências entre o modelo ligado e outros recursos. Por conseguinte, quando a outros recursos necessitam de um valor de saída do modelo ligado, pode certificar-se que o modelo ligado é implementado antes de lhes. Em alternativa, quando o modelo ligado depende de outros recursos, pode certificar-se a que outros recursos são implementados antes do modelo ligado. Pode obter um valor a partir de um modelo ligado com a seguinte sintaxe:

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

O serviço do Gestor de recursos tem de ser capaz de aceder ao modelo ligado. Não é possível especificar um ficheiro local ou um ficheiro que só está disponível na sua rede local para o modelo ligado. Apenas pode fornecer um valor URI que inclui um **http** ou **https**. Uma opção é colocar o seu modelo ligado numa conta do storage e utilize o URI para que o item, tal como mostrado no exemplo seguinte:

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Embora o modelo ligado tem de estar disponível externamente, não é necessário ser geralmente disponível para o público. Pode adicionar o seu modelo para uma conta de armazenamento privada que seja acessível para apenas o proprietário da conta de armazenamento. Em seguida, crie um token de assinatura (SAS) de acesso partilhado para ativar o acesso durante a implementação. Adicionar esse token SAS para o URI para o modelo ligado. Para obter passos sobre como configurar um modelo de uma conta de armazenamento e de gerar um token SAS, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [implementar recursos com modelos do Resource Manager e CLI do Azure](resource-group-template-deploy-cli.md). 

O exemplo seguinte mostra um modelo de principal que liga a outro modelo. O modelo ligado é acedido com um token SAS, que é transmitido como um parâmetro.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Apesar do token é transmitido como uma cadeia segura, o URI do modelo ligado, incluindo o token SAS, é registado nas operações de implementação. Para limitar a exposição, defina uma expiração para o token.

O Resource Manager processa cada modelo ligado como uma implementação separada. O histórico de implementação para o grupo de recursos, é apresentado o implementações separadas para o principal e modelos aninhados.

![histórico de implementações](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Ligar a um ficheiro de parâmetros
O exemplo seguinte utiliza o **parametersLink** propriedade para ligar a um ficheiro de parâmetros.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

O valor URI para o ficheiro de parâmetros ligado não pode ser um ficheiro local e tem de incluir um **http** ou **https**. O ficheiro de parâmetros também pode ser limitado para acesso através de um token SAS.

## <a name="using-variables-to-link-templates"></a>Utilizar variáveis para ligar os modelos
Os exemplos anteriores mostrou valores codificados de URL para as ligações de modelo. Esta abordagem poderá funcionar para um modelo simples, mas não funciona bem quando trabalhar com um grande conjunto de modelos modulares. Em vez disso, pode criar uma variável estática que armazena um URL de base para o modelo de principal e, em seguida, criar dinamicamente os URLs para os modelos ligados desse URL base. A vantagem desta abordagem é facilmente pode mover ou copiar o modelo porque apenas precisar de alterar a variável estática no modelo principal. O modelo de principal transmite os URIs em todo o modelo decomposed correto.

O exemplo seguinte mostra como utilizar um URL de base para criar dois URLs para modelos ligados (**sharedTemplateUrl** e **vmTemplate**). 

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Também pode utilizar [deployment()](resource-group-template-functions-deployment.md#deployment) para obter o URL de base para o modelo atual e utilize-a para obter o URL para outros modelos na mesma localização. Esta abordagem é útil se a localização do modelo é alterado (talvez devido ao controlo de versões) ou se quiser evitar rígido codificação URLs no ficheiro de modelo. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Exemplo completo
Os modelos de exemplo seguintes mostram uma disposição simplificada de modelos ligados para ilustrar muitos outros dos conceitos neste artigo. Pressupõe que os modelos foram adicionados ao mesmo contentor numa conta do storage com acesso de público desativado. O modelo ligado transmite um valor para o modelo no principal de **produz** secção.

O **parent.json** ficheiro é composta por:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

O **helloworld.json** ficheiro é composta por:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

No PowerShell, obter um token para o contentor e implementar modelos com:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

No Azure CLI 2.0, obter um token para o contentor e implementar modelos com o seguinte código:

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a definir a ordem de implementação para os seus recursos, consulte [definir dependências nos modelos Azure Resource Manager](resource-group-define-dependencies.md)
* Para saber como definir um recurso mas criar várias instâncias do mesmo, consulte o artigo [criar várias instâncias de recursos no Gestor de recursos do Azure](resource-group-create-multiple.md)

