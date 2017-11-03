---
title: "SKU do Azure não está disponíveis erros | Microsoft Docs"
description: "Descreve como resolver o SKU não está disponível erro durante a implementação."
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
ms.openlocfilehash: 25cea4ae23471d182105ca3f720aaf74f81bf8c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolver erros de SKU não está disponível

Este artigo descreve como resolver o **SkuNotAvailable** erro.

## <a name="symptom"></a>Sintoma

Quando implementar um recurso (normalmente, uma máquina virtual), receberá o seguinte código de erro e a mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Recebe este erro quando o recurso SKU que selecionou (por exemplo, o tamanho da VM) não está disponível para a localização que selecionou.

## <a name="solution"></a>Solução

Para resolver este problema, tem de determinar quais os SKUs estão disponíveis numa região. Pode utilizar o PowerShell, o portal ou de uma operação REST para localizar SKUs disponíveis.

### <a name="solution-1"></a>Solução 1

Utilize o [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) comando do PowerShell. Filtre os resultados por localização. Tem de ter a versão mais recente do PowerShell para este comando.

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
```

Os resultados incluem uma lista de SKUs para a localização e as restrições para esse SKU.

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

### <a name="solution-2"></a>Solução 2

Utilizar a CLI do Azure, o `az vm list-skus` comando. Em seguida, pode utilizar `grep` ou um utilitário semelhante para filtrar os resultados.

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

### <a name="solution-3"></a>Solução 3

Utilize o [portal](https://portal.azure.com). Inicie sessão no portal e adicione um recurso através da interface. Como definir os valores, consulte os SKUs disponíveis para esse recurso. Não é necessário concluir a implementação.

![SKUs disponíveis](./media/resource-manager-sku-not-available-errors/view-sku.png)

### <a name="solution-4"></a>Solução 4

Utilize a API REST para máquinas virtuais. Envie o pedido seguinte:

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

Devolve e regiões de SKUs disponíveis no seguinte formato:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

Se não conseguir encontrar um SKU adequado nessa região ou uma região alternativa que satisfaz a sua empresa, tem de submeter um [pedido SKU](https://aka.ms/skurestriction) para o suporte do Azure.