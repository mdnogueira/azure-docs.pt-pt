---
title: "Erro de RequestDisallowedByPolicy com a política de recursos do Azure | Microsoft Docs"
description: Descreve a causa do erro RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erro de RequestDisallowedByPolicy com a política de recursos do Azure

Este artigo descreve a causa do erro RequestDisallowedByPolicy, também fornece solução para este erro.

## <a name="symptom"></a>Sintoma

Quando tenta efetuar uma ação durante a implementação, poderá receber um **RequestDisallowedByPolicy** erro impede a conclusão da ação. O exemplo seguinte mostra o erro:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Resolução de problemas

Para obter detalhes sobre a política que bloqueados a implementação, utilize um dos métodos seguinte:

### <a name="method-1"></a>Método 1

No PowerShell, fornecer esse identificador de política, como o `Id` parâmetro para obter detalhes sobre a política que a implementação de bloqueado.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Método 2 

No Azure CLI 2.0, forneça o nome da definição de política: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou a conformidade, o departamento de TI pode impor uma política de recurso que proíbe a criação de endereços IP públicos, grupos de segurança de rede, rotas definidas pelo utilizador ou as tabelas de rotas. A mensagem de erro no **sintomas** secção mostra uma política com o nome **regionPolicyDefinition**. A política pode ter um nome diferente.
Para resolver este problema, trabalhar com o seu departamento de TI para rever as políticas de recursos e determinar como efetuar a ação pedida em conformidade com as políticas.

Para obter mais informações, veja os artigos seguintes:

- [Descrição geral da política de recurso](resource-manager-policy.md)
- [Atribuições de política do Vista através do portal](resource-manager-policy-portal.md)
