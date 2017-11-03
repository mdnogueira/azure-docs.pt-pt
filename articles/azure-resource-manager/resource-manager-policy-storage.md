---
title: "Políticas de recursos do Azure para as contas do storage | Microsoft Docs"
description: "Descreve as políticas para gerir a implementação de contas do storage do Azure Resource Manager."
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-to-storage-accounts"></a>Aplicar políticas de recursos para contas de armazenamento
Este tópico mostra várias [as políticas de recursos](resource-manager-policy.md) pode aplicar às contas do storage do Azure. Estas políticas garantir consistência para as contas do storage implementada na sua organização. 

## <a name="define-permitted-storage-account-types"></a>Definir os tipos de conta de armazenamento permitido

A seguinte política restringe que [tipos de conta de armazenamento](../storage/common/storage-redundancy.md) pode ser implementado:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Uma regra de política semelhantes com um parâmetro para aceitar os SKUs permitidos está disponível como uma definição de política incorporada. A política de incorporada tem o ID de recurso dos `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`. 

## <a name="define-permitted-access-tier"></a>Definir a camada de acesso permitido

A seguinte política especifica o tipo de [camada de acesso](../storage/blobs/storage-blob-storage-tiers.md) que pode ser especificado para as contas de armazenamento:

```json
{
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
}
```

## <a name="ensure-encryption-is-enabled"></a>Certifique-se de que a encriptação está ativada

A seguinte política requer que todas as contas de armazenamento para ativar [encriptação do serviço de armazenamento](../storage/common/storage-service-encryption.md):

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Esta regra de política também está disponível como uma definição de política incorporado com o ID de recurso dos `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`.

## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (conforme ilustrado nos exemplos anteriores), terá de criar a definição de política e atribua-a um âmbito. O âmbito pode ser uma subscrição, o grupo de recursos ou o recurso. Para atribuir políticas através do portal, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](resource-manager-policy-portal.md). Para atribuir políticas através da REST API, PowerShell ou o CLI do Azure, consulte [atribuir e gerir políticas através do script](resource-manager-policy-create-assign.md). 
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

