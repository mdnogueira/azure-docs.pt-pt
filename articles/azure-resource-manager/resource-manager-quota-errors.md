---
title: Erros de quota do Azure | Microsoft Docs
description: Descreve como resolver erros de qouta do recurso.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 6a8024a12c4a79e92e37df0a56b6e6bd0cb6a8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolva os erros para quotas de recursos

Este artigo descreve os erros de quota que poderá encontrar durante a implementação de recursos.

## <a name="symptom"></a>Sintoma

Se implementar um modelo que cria os recursos que excedem as quotas do Azure, receberá um erro de implementação que se pareça com:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Em alternativa, poderá ver:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Causa

As quotas são aplicadas por grupo de recursos, subscrições, contas e outros âmbitos. Por exemplo, a sua subscrição pode ser configurada para limitar o número de núcleos para uma região. Se tentar implementar uma máquina virtual com mais núcleos do que a quantidade permitido, receberá um erro a indicar que a quota foi excedida.
Para informações sobre a quota completa, consulte [subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md).

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Para a CLI do Azure, utilize o `az vm list-usage` comando para encontrar as quotas de máquina virtual.

```azurecli
az vm list-usage --location "South Central US"
```

Que devolve:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="solution-2"></a>Solução 2

Para o PowerShell, utilize o **Get-AzureRmVMUsage** comando para encontrar as quotas de máquina virtual.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

Que devolve:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

### <a name="solution-3"></a>Solução 3

Se precisar de aumentar o limite de quota, vá para o portal de ficheiros e um problema de suporte para aumentar a quota para a região na qual pretende implementar.

> [!NOTE]
> Lembre-se de que para os grupos de recursos, a quota para cada região individuais, não para a subscrição completa. Se precisar de implementar 30 núcleos nos EUA oeste, terá de voltar a pedir 30 núcleos do Gestor de recursos em EUA oeste. Se precisar de implementar 30 núcleos em qualquer uma das regiões aos quais têm acesso, deverá solicitar ao para 30 núcleos do Gestor de recursos em todas as regiões.
>
>