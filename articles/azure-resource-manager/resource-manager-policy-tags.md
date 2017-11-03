---
title: "Políticas de recursos do Azure para etiquetas | Microsoft Docs"
description: "Fornece exemplos de políticas de recursos para gerir etiquetas de recursos"
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
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-tags"></a>Aplicar políticas de recursos de etiquetas

Este tópico fornece as regras de política comum que pode aplicar para garantir a utilização consistente de etiquetas de recursos.

Aplicar uma política de etiqueta a um grupo de recursos ou subscrição com os recursos existentes não retroactively aplicar a política a esses recursos. Para impor políticas nesses recursos, acione uma atualização para os recursos existentes. Este artigo inclui um exemplo do PowerShell para acionar uma atualização.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Certifique-se de que todos os recursos num grupo de recursos tem um tag/valor

Um requisito comum é que todos os recursos num grupo de recursos tem uma tag específica e o valor. Este requisito, muitas vezes, é necessário para controlar os custos pelo departamento. As condições seguintes têm de ser cumpridas:

* A tag obrigatória e o valor são acrescentadas recursos novos e atualizados que não têm a etiqueta.
* A tag obrigatória e o valor não não possível remover quaisquer recursos existentes.

Concretizar este requisito aplicando duas políticas incorporadas para um grupo de recursos.

| ID | Descrição |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Aplica-se uma tag necessária e o respetivo valor predefinido se não for especificada pelo utilizador. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Impõe uma tag necessária e o respetivo valor. |

### <a name="powershell"></a>PowerShell

O seguinte script do PowerShell atribui as duas definições de política incorporadas para um grupo de recursos. Antes de executar o script, atribua todas as etiquetas necessárias para o grupo de recursos. Cada etiqueta no grupo de recursos é necessária para os recursos no grupo. Para atribuir a todos os grupos de recursos na sua subscrição, não fornecem o `-Name` parâmetro ao obter os grupos de recursos.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

Depois de atribuir as políticas, pode acionar uma atualização para todos os recursos existentes para impor as políticas de tag que tenha adicionado. O script seguinte mantém outras etiquetas que existiam nos recursos:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>Exigir etiquetas para um tipo de recurso
O exemplo seguinte mostra como aninhar operadores lógicos para exigir uma etiqueta de aplicação para um tipo de recurso especificado (neste caso, contas de armazenamento).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Exigir etiqueta
A seguinte política nega pedidos que não tenham uma etiqueta que contém a chave "costCenter" (pode ser aplicada a qualquer valor):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (conforme ilustrado nos exemplos anteriores), terá de criar a definição de política e atribua-a um âmbito. O âmbito pode ser uma subscrição, o grupo de recursos ou o recurso. Para atribuir políticas através do portal, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](resource-manager-policy-portal.md). Para atribuir políticas através da REST API, PowerShell ou o CLI do Azure, consulte [atribuir e gerir políticas através do script](resource-manager-policy-create-assign.md).
* Para uma introdução às políticas de recursos, consulte [descrição geral da política de recurso](resource-manager-policy.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

