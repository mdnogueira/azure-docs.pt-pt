---
title: Converter Azure geridas armazenamento de discos de norma para premium e vice versa | Microsoft Docs
description: "Como converter Azure discos geridos do padrão para premium e vice versa, utilizando o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 993aea9f3b6fe4dabdc6500e5cf9d49ec594f135
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Converter Azure geridas armazenamento de discos de norma para premium e vice versa

Discos geridos oferece duas opções de armazenamento: [Premium](premium-storage.md) (baseadas em SSD) e [padrão](standard-storage.md) (baseado em HDD). Permite-lhe facilmente alternar entre as duas opções com período de indisponibilidade mínimo com base nas necessidades de desempenho. Esta capacidade não está disponível para os discos não geridos. Mas pode facilmente [converter discos geridos](convert-unmanaged-to-managed-disks.md) facilmente alternar entre as duas opções.

Este artigo mostra como converter discos geridos de padrão para premium e vice versa utilizando o Azure PowerShell. Se precisar de instalar ou atualizá-lo, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Antes de começar

* A conversão necessita de um reinício da VM, por isso, agende a migração do seu armazenamento de discos durante uma janela de manutenção já existente. 
* Se estiver a utilizar discos não geridos, primeiro [converter discos geridos](convert-unmanaged-to-managed-disks.md) para utilizar este artigo para alternar entre as duas opções de armazenamento. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Converter os discos geridos de uma VM de padrão para premium e vice versa

No exemplo seguinte, mostramos como mudar todos os discos de uma VM do padrão para o premium storage. Para utilizar discos premium gerido, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporte o premium storage. Este exemplo também muda para um tamanho que suporte o premium storage.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Converter um disco gerido standard premium e vice versa

Para sua carga de trabalho de programador/teste, poderá ter mistura de discos padrão e premium para reduzir o custo. Pode fazê-lo por atualizar para o premium storage, apenas os discos que requerem um melhor desempenho. No exemplo seguinte, mostramos como mudar num disco individual de uma VM do padrão para o premium storage e vice-versa. Para utilizar discos premium gerido, a VM tem de utilizar um [tamanho da VM](sizes.md) que suporte o premium storage. Este exemplo também muda para um tamanho que suporte o premium storage.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId
$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Passos seguintes

Efetuar uma cópia só de leitura de uma VM utilizando [instantâneos](snapshot-copy-managed-disk.md).

