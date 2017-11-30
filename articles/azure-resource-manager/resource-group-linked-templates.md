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
ms.date: 11/28/2017
ms.author: tomfitz
ms.openlocfilehash: a86d4d8705c7093e3900a9738ddbd364db8bd3b8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Utilizar modelos ligados ao implementar os recursos do Azure

Para implementar a sua solução, pode utilizar um único modelo ou um modelo de principal com vários modelos ligados. Para pequenas e médias soluções, é mais fácil de compreender e manter um único modelo. Conseguir ver todos os recursos e os valores num único ficheiro. Para cenários avançados, modelos ligados permitem-lhe dividir a solução para componentes de destino e reutilizar modelos.

Ao utilizar o modelo ligado, criar um modelo de principal que recebe os valores de parâmetros durante a implementação. O modelo de principal contém todos os modelos ligados e transmite os valores para esses modelos conforme necessário.

![modelos ligados](./media/resource-group-linked-templates/nestedTemplateDesign.png)

## <a name="link-to-a-template"></a>Ligar a um modelo

Para ligar a outro modelo, adicione um **implementações** recursos ao seu modelo principal.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <inline-template-or-external-template>
      }
  }
]
```

As propriedades de que fornecer para o recurso de implementação variam com base em se estiver a ligar a um modelo externo ou incorporar um modelo de inline no modelo principal.

### <a name="inline-template"></a>Modelo de inline

Para incorporar o modelo ligado, utilize o **modelo** propriedade e incluir o modelo.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
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
            "name": "[variables('storageName')]",
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
  }
]
```

### <a name="external-template-and-external-parameters"></a>Modelo externo e parâmetros externos

Para ligar a um modelo externo e o ficheiro de parâmetros, utilize **templateLink** e **parametersLink**. Quando ligar a um modelo, o serviço do Gestor de recursos tem de ser capaz de aceder ao mesmo. Não é possível especificar um ficheiro local ou um ficheiro que só está disponível na sua rede local. Apenas pode fornecer um valor URI que inclui um **http** ou **https**. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e utilizar o URI para que o item.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parametersLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

### <a name="external-template-and-inline-parameters"></a>Parâmetros de modelo e inline externos

Em alternativa, pode fornecer o parâmetro inline. Para transferir um valor do modelo de principal para o modelo ligado, utilize **parâmetros**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

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

## <a name="get-values-from-linked-template"></a>Obter os valores a partir do modelo ligado

Para obter um valor de saída a partir de um modelo ligado, obter o valor da propriedade com sintaxe como: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Os exemplos seguintes demonstram como fazer referência a um modelo ligado e obter um valor de saída. O modelo ligado devolve uma mensagem simple.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

O modelo de principal implementa o modelo ligado e obtém o valor devolvido. Tenha em atenção que referencia o recurso de implementação por nome e utiliza o nome da propriedade devolvido pelo modelo de ligado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Como outros tipos de recursos, pode definir as dependências entre o modelo ligado e outros recursos. Por conseguinte, quando a outros recursos necessitam de um valor de saída do modelo ligado, pode certificar-se que o modelo ligado é implementado antes de lhes. Em alternativa, quando o modelo ligado depende de outros recursos, pode certificar-se a que outros recursos são implementados antes do modelo ligado.

O exemplo seguinte mostra um modelo que implementa um endereço IP público e devolve o ID de recurso:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Para utilizar o endereço IP público do modelo anterior, quando implementar um balanceador de carga, ligue para o modelo e adicionar uma dependência no recurso de implementação. O endereço IP público no balanceador de carga está definido para o valor de saída do modelo ligado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-templates-in-deployment-history"></a>Modelos ligados no histórico de implementação

O Resource Manager processa cada modelo ligado como uma implementação separada no histórico de implementação. Por conseguinte, um modelo de principal com três modelos ligados é apresentado no histórico de implementação, como:

![Histórico de implementações](./media/resource-group-linked-templates/deployment-history.png)

Pode utilizar estas entradas separadas no histórico para obter valores de saída após a implementação. O modelo seguinte cria um endereço IP público e produz o endereço IP:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

As seguintes ligações de modelo para o modelo anterior. Cria três endereços IP públicos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

Após a implementação, pode obter os valores de saída com o seguinte script do PowerShell:

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Em alternativa, o script de CLI do Azure:

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Proteger um modelo externo

Embora o modelo ligado tem de estar disponível externamente, não é necessário ser geralmente disponível para o público. Pode adicionar o seu modelo para uma conta de armazenamento privada que seja acessível para apenas o proprietário da conta de armazenamento. Em seguida, crie um token de assinatura (SAS) de acesso partilhado para ativar o acesso durante a implementação. Adicionar esse token SAS para o URI para o modelo ligado. Apesar do token é transmitido como uma cadeia segura, o URI do modelo ligado, incluindo o token SAS, é registado nas operações de implementação. Para limitar a exposição, defina uma expiração para o token.

O ficheiro de parâmetros também pode ser limitado para acesso através de um token SAS.

O exemplo seguinte mostra como transmitir um token SAS aquando da associação a um modelo de incidente:

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

Da CLI do Azure, pode obter um token para o contentor e implementar modelos com o seguinte código:

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

## <a name="example-templates"></a>Modelos de exemplo

### <a name="hello-world-from-linked-template"></a>Olá, mundo a partir do modelo ligado

Para implementar o [modelo principal](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) e [modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json), utilizar o PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/helloworldparent.json
```

Em alternativa, CLI do Azure:

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/helloworldparent.json
```

### <a name="load-balancer-with-public-ip-address-in-linked-template"></a>O Balanceador de carga com o endereço IP público do modelo ligado

Para implementar o [modelo principal](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) e [modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json), utilizar o PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json
```

Em alternativa, CLI do Azure:

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json
```

### <a name="multiple-public-ip-addresses-in-linked-template"></a>Vários endereços IP públicos no modelo ligado

Para implementar o [modelo principal](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) e [modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json), utilizar o PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json
```

Em alternativa, CLI do Azure:

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a definir a ordem de implementação para os seus recursos, consulte [definir dependências nos modelos Azure Resource Manager](resource-group-define-dependencies.md).
* Para saber como definir um recurso mas criar várias instâncias do mesmo, consulte o artigo [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para obter passos sobre como configurar um modelo de uma conta de armazenamento e de gerar um token SAS, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [implementar recursos com modelos do Resource Manager e CLI do Azure](resource-group-template-deploy-cli.md).