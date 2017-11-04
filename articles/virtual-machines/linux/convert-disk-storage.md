---
title: Converter Azure geridas armazenamento de discos de norma para premium e vice versa | Microsoft Docs
description: "Como converter Azure geridos armazenamento de discos do padrão para premium e vice versa, utilizando a CLI do Azure."
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Converter Azure geridas armazenamento de discos de norma para premium e vice versa

Discos geridos oferece duas opções de armazenamento: [Premium](../windows/premium-storage.md) (baseadas em SSD) e [padrão](../windows/standard-storage.md) (baseado em HDD). Permite-lhe facilmente alternar entre as duas opções com período de indisponibilidade mínimo com base nas necessidades de desempenho. Esta capacidade não está disponível para os discos não geridos. Mas pode facilmente [converter discos geridos](convert-unmanaged-to-managed-disks.md) facilmente alternar entre as duas opções.

Este artigo mostra como converter discos geridos de padrão para premium e vice versa, utilizando a CLI do Azure. Se precisar de instalar ou atualizá-lo, consulte [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Antes de começar

* A conversão necessita de um reinício da VM, por isso, agende a migração do seu armazenamento de discos durante uma janela de manutenção já existente. 
* Se estiver a utilizar discos não geridos, primeiro [converter discos geridos](convert-unmanaged-to-managed-disks.md) para utilizar este artigo para alternar entre as duas opções de armazenamento. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Converter os discos geridos de uma VM de padrão para premium e vice versa

No exemplo seguinte, mostramos como mudar todos os discos de uma VM do padrão para o premium storage. Para utilizar discos premium gerido, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporte o premium storage. Este exemplo também muda para um tamanho que suporte o premium storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Converter um disco gerido standard premium e vice versa

Para sua carga de trabalho de programador/teste, poderá ter mistura de discos padrão e premium para reduzir o custo. Pode fazê-lo por atualizar para o premium storage, apenas os discos que requerem um melhor desempenho. No exemplo seguinte, mostramos como mudar num disco individual de uma VM do padrão para o premium storage e vice-versa. Para utilizar discos premium gerido, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporte o premium storage. Este exemplo também muda para um tamanho que suporte o premium storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Passos seguintes

Efetuar uma cópia só de leitura de uma VM utilizando [instantâneos](snapshot-copy-managed-disk.md).

