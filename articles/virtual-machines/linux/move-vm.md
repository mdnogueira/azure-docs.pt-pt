---
title: Mover uma VM com Linux no Azure | Microsoft Docs
description: "Mova uma VM com Linux para outro Azure subscrição ou grupo de recursos no modelo de implementação Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 4695a9c934f97f2b2d448c4990e7ad5533e38e9f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover uma VM com Linux para outro subscrição ou grupo de recursos
Este artigo explica como mover uma VM com Linux entre grupos de recursos ou subscrições. Mover uma VM entre subscrições pode ser útil se tiver criado uma VM numa subscrição pessoal e agora pretende movê-la para a subscrição da sua empresa.

> [!IMPORTANT]
>Não é possível mover discos geridos neste momento. 
>
>Novo IDs de recurso são criados como parte da transição. Assim que a VM foi movida, terá de atualizar as suas ferramentas e scripts para utilizar o novo IDs de recurso. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Utilizar a CLI do Azure para mover uma VM
Para mover com êxito uma VM, tem de mover a VM e todos os respetivos recursos de suporte. Utilize o **mostrar de grupo do azure** comando para listar todos os recursos no grupo de recursos e os respetivos IDs. Ajuda a encaminhar a saída deste comando para um ficheiro para que possa copiar e colar os IDs para comandos posteriores.

    azure group show <resourceGroupName>

Para mover uma VM e os respetivos recursos para outro grupo de recursos, utilize o **mover recursos do azure** comando da CLI. O exemplo seguinte mostra como mover uma VM e os recursos mais comuns requer. Utilizamos o **-i** parâmetro e passar numa lista separada por vírgulas (sem espaços) de IDs de para os recursos a mover.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

Se pretender mover VM e os respetivos recursos para uma subscrição diferente, adicione o **– subscriptionId destino &#60; destinationSubscriptionID &#62;** parâmetro para especificar a subscrição de destino.

Se estiver a trabalhar na linha de comandos num computador Windows, terá de adicionar um  **$**  à frente os nomes de variáveis quando, declará-los. Isto não é necessário no Linux.

É-lhe pedido para confirmar que pretende mover o recurso especificado. Tipo **Y** para confirmar que pretende mover os recursos.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passos seguintes
Pode mover vários tipos de recursos entre grupos de recursos e as subscrições. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../../resource-group-move-resources.md).    

