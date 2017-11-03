---
title: "Atribuir e gerir políticas de recursos do Azure | Microsoft Docs"
description: "Descreve como aplicar políticas de recursos do Azure para as subscrições e grupos de recursos e como visualizar políticas de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>Atribuir e gerir políticas de recursos

Para implementar uma política, tem de efetuar estes passos:

1. Verifique as definições de política (incluindo as políticas incorporadas fornecidas pelo Azure) para ver se já existir na sua subscrição que cumpre os requisitos.
2. Se existir, obter o respetivo nome.
3. Se não existir, definir a regra de política com JSON e adicione-o como uma definição de política na sua subscrição. Este passo disponibiliza a política para a atribuição mas não se aplica as regras para a sua subscrição.
4. Para ambos os casos, atribua a política a um âmbito (por exemplo, um subscrição ou grupo de recursos). As regras da política agora são impostas.

Este artigo foca-se os passos para criar uma definição de política e atribuir essa definição a um âmbito através da REST API, PowerShell ou o CLI do Azure. Se preferir utilizar o portal para atribuir políticas, consulte [portal do Azure utilizar atribuir e gerir políticas de recursos](resource-manager-policy-portal.md). Este artigo não focar-se na sintaxe para a criação de definição de política. Para obter informações sobre a sintaxe de política, consulte [descrição geral da política de recurso](resource-manager-policy.md).

## <a name="exclusion-scopes"></a>Âmbitos de exclusão

Pode excluir um âmbito de atribuição de uma política. Esta capacidade simplifica atribuições de política porque pode atribuir uma política ao nível da subscrição, mas especificar onde não é aplicada a política. Por exemplo, na sua subscrição, tem um grupo de recursos que destina-se a infraestrutura de rede. As equipas das aplicações implementar os seus recursos de outros grupos de recursos. Não pretende que essas equipas para criar recursos de rede que podem levar a problemas de segurança. No entanto, no grupo de recursos de rede, que pretende permitir que os recursos de rede. Atribuir a política ao nível da subscrição mas excluir o grupo de recursos de rede. Pode especificar vários âmbitos sub.

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

Se especificar um âmbito de exclusão na sua atribuição, utilize o **2017-06-01-preview** versão da API.

## <a name="rest-api"></a>API REST

### <a name="create-policy-definition"></a>Criar a definição de política

Pode criar uma política com o [API REST para definições de política](/rest/api/resources/policydefinitions). A API REST permite-lhe criar e eliminar definições de política e obter informações sobre as definições existentes.

Para criar uma definição de política, execute:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Incluem um corpo do pedido semelhante ao seguinte exemplo:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Atribuir a política

Pode aplicar a definição de política no âmbito pretendido através de [API REST para atribuições de política](/rest/api/resources/policyassignments). A API REST permite-lhe criar e eliminar atribuições de política e obter informações sobre atribuições existentes.

Para criar uma atribuição de política, execute:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{-Atribuição de política} é o nome da atribuição de política.

Incluem um corpo do pedido semelhante ao seguinte exemplo:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Política do Vista
Para obter uma política, utilize o [obter a definição de política](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get) operação.

### <a name="get-aliases"></a>Obter aliases
Pode obter aliases através da API REST:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

O exemplo seguinte mostra uma definição de um alias. Como pode ver, um alias define caminhos em diferentes versões de API, mesmo quando há uma alteração de nome de propriedade. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

Antes de continuar com os exemplos do PowerShell, certifique-se de que tem [instalada a versão mais recente](/powershell/azure/install-azurerm-ps) do Azure PowerShell. Parâmetros de política foram adicionados na versão 3.6.0. Se tiver uma versão anterior, os exemplos de devolverem um erro que indica que o parâmetro não é possível encontrar.

### <a name="view-policy-definitions"></a>Definições de política do Vista
Para ver todas as definições de política na sua subscrição, utilize o seguinte comando:

```powershell
Get-AzureRmPolicyDefinition
```

Devolve todas as definições de política disponíveis, incluindo políticas incorporadas. Cada política é devolvida o seguinte formato:

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Antes de continuar para criar uma definição de política, observe as políticas incorporadas. Se encontrar uma política incorporada que aplica-se os limites que é necessário, pode ignorar a criação de uma definição de política. Em vez disso, atribua a política incorporada para o âmbito pretendido.

### <a name="create-policy-definition"></a>Criar a definição de política
Pode criar uma definição de política utilizando o `New-AzureRmPolicyDefinition` cmdlet.

Para criar uma definição de política de um ficheiro, passe o caminho para o ficheiro. Para um ficheiro externo, utilize:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para um ficheiro local, utilize:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Para criar uma definição de política com uma regra de inline, utilize:

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

O resultado é armazenado num `$definition` objeto, que é utilizado durante a atribuição de política. 

O exemplo seguinte cria uma definição de política que inclui os parâmetros:

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Atribuir a política

Aplicar a política no âmbito pretendido utilizando a `New-AzureRmPolicyAssignment` cmdlet. O seguinte exemplo atribui a política para um grupo de recursos.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Para atribuir uma política que necessita de parâmetros, crie e de objeto com esses valores. O exemplo seguinte obtém uma política incorporada e transmite os valores de parâmetros:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Atribuição de política do Vista

Para obter uma atribuição de política específica, utilize:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Para ver a regra de política para uma definição de política, utilize:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, utilize:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>CLI do Azure

### <a name="view-policy-definitions"></a>Definições de política do Vista
Para ver todas as definições de política na sua subscrição, utilize o seguinte comando:

```azurecli
az policy definition list
```

Devolve todas as definições de política disponíveis, incluindo políticas incorporadas. Cada política é devolvida o seguinte formato:

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Antes de continuar para criar uma definição de política, observe as políticas incorporadas. Se encontrar uma política incorporada que aplica-se os limites que é necessário, pode ignorar a criação de uma definição de política. Em vez disso, atribua a política incorporada para o âmbito pretendido.

### <a name="create-policy-definition"></a>Criar a definição de política

Pode criar uma definição de política utilizando a CLI do Azure com o comando de definição de política.

Para criar uma definição de política com uma regra de inline, utilize:

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Atribuir a política

Pode aplicar a política para o âmbito pretendido utilizando o comando de atribuição de política. O seguinte exemplo atribui uma política para um grupo de recursos.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Atribuição de política do Vista

Para ver uma atribuição de política, forneça o nome da atribuição de política e o âmbito:

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, utilize:

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Passos seguintes
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

