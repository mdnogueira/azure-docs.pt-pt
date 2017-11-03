---
title: Criar uma VM com Linux zoned com a CLI do Azure | Microsoft Docs
description: Criar uma VM com Linux numa zona de disponibilidade com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 232c2cf1ba0a7de23da10357de9a6e6ad9a0d41d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Criar uma máquina virtual Linux uma zona de disponibilidade com a CLI do Azure

Passos neste artigo, através de utilizar a CLI do Azure para criar uma VM com Linux numa zona de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Certifique-se de que instalou a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) e com sessão iniciada numa conta do Azure com [início de sessão az](/cli/azure/#login).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create).  

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. Neste exemplo, um grupo de recursos denominado *myResourceGroupVM* é criado no *eastus2* região. East US 2 (E.U.A. Leste 2) é uma das regiões do Azure que suporta zonas de disponibilidade em modo de pré-visualização.

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser vista ao longo deste artigo.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Criar uma máquina virtual com o [az vm criar](/cli/azure/vm#az_vm_create) comando. 

Ao criar uma máquina virtual, várias opções estão disponíveis, tais como a imagem do sistema operativo, as credenciais administrativas e dimensionamento do disco. Neste exemplo, é criada uma máquina virtual com um nome de *myVM* Ubuntu Server a executar. A VM é criada na zona de disponibilidade *1*. Por predefinição, a VM é criada no *Standard_DS1_v2* tamanho. Este tamanho é suportado a pré-visualização de zonas de disponibilidade.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

Pode demorar alguns minutos para criar a VM. Quando tiver sido criada a VM, a CLI do Azure produz informações sobre a VM. Tome nota do `zones` valor que indica a zona de disponibilidade no qual a VM está em execução. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>Zona para o endereço IP e o disco gerido

Quando a VM está implementada uma zona de disponibilidade, o endereço IP e os recursos de disco gerido são implementados no mesmo horário de disponibilidade. Os exemplos seguintes obtém informações sobre estes recursos.

Primeiro utilizar o [az lista-endereços ip vm-](/cli/azure/vm#az_vm_list_ip_addresses) comando para devolver o nome do recurso de endereço IP público na *myVM*. Neste exemplo, o nome é armazenado numa variável que é utilizada num passo posterior.

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Agora pode obter informações sobre o endereço IP:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

O resultado mostra que o endereço IP está na mesma zona de disponibilidade da VM:

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

Da mesma forma, certifique-se de que o disco gerido a VM está na zona de disponibilidade. Utilize o [mostrar de vm az](/cli/azure/vm#az_vm_show) comando para devolver o id de disco. Neste exemplo, o id de disco é armazenado numa variável que é utilizada num passo posterior. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Agora pode obter informações sobre o disco gerido:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

O resultado mostra que o disco gerido está na mesma zona de disponibilidade que a VM:

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma VM uma zona de disponibilidade. Saiba mais sobre [regiões e disponibilidade](regions-and-availability.md) para VMs do Azure.




