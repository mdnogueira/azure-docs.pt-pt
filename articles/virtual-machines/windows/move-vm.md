---
title: Mover um recurso de VM do Windows no Azure | Microsoft Docs
description: "Mova uma VM do Windows para outro Azure subscrição ou grupo de recursos no modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 1db25a5d9ff5cb6aa2787a0cafa40cfb010e3b06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover uma VM do Windows para outro grupo de subscrição ou recurso do Azure
Este artigo explica como mover uma VM do Windows entre grupos de recursos ou subscrições. Mover entre subscrições pode ser útil se tiver uma VM originalmente criado numa subscrição pessoal e agora pretende movê-la para a subscrição da sua empresa para continuar o trabalho.

> [!IMPORTANT]
>Não é possível mover discos geridos neste momento. 
>
>Novo IDs de recurso são criados como parte da transição. Assim que a VM foi movida, terá de atualizar as suas ferramentas e scripts para utilizar o novo IDs de recurso. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Utilize o Powershell para mover uma VM
Para mover uma máquina virtual para outro grupo de recursos, tem de certificar-se de que também mova todos os recursos dependentes. Para utilizar o cmdlet Move-AzureRMResource, terá do nome do recurso e o tipo de recurso. Pode obter ambos a partir do cmdlet AzureRMResource localizar.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"


Para mover uma VM, é necessário mover vários recursos. Iremos pode apenas criar variáveis separadas para cada recurso e, em seguida, lista-los. Este exemplo inclui a maioria dos recursos básicos para uma VM, mas pode adicionar mais conforme necessário.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"

    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"

    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Para mover os recursos para uma subscrição diferente, inclua o **- DestinationSubscriptionId** parâmetro. 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Será pedido para confirmar que pretende mover os recursos especificados. Tipo **Y** para confirmar que pretende mover os recursos.

## <a name="next-steps"></a>Passos seguintes
Pode mover vários tipos de recursos entre grupos de recursos e as subscrições. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../../resource-group-move-resources.md).    

