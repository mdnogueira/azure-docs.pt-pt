---
title: "Converter uma máquina virtual do Windows de discos não geridos para discos geridos - discos gerida do Azure | Microsoft Docs"
description: "Como converter uma VM do Windows a partir de discos não geridos para gerido discos utilizando o PowerShell no modelo de implementação Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.openlocfilehash: 3f3075fc5c6f3a47bde13d98d0b705c29b98112b
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual do Windows de discos não geridos para gerido discos

Se tiver existentes máquinas virtuais (VMs Windows) que utilizam discos não geridos, pode converter as VMs para utilizar discos geridos através de [Azure geridos discos](managed-disks-overview.md) serviço. Este processo converte o disco do SO e discos de dados anexados.

Este artigo mostra como converter VMs com o Azure PowerShell. Se precisar de instalar ou atualizá-lo, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Antes de começar


* Reveja [planear a migração para discos geridos](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
* Reveja [as FAQ sobre a migração para discos geridos](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Converter a VMs de instância única
Esta secção abrange como converter as VMs do Azure de instância única de discos não geridos para gerido discos. (Se as suas VMs num conjunto de disponibilidade, consulte a secção seguinte). 

1. Desalocar a VM utilizando o [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet. O exemplo seguinte deallocates VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Converta a VM em discos geridos utilizando o [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) cmdlet. O seguinte processo converte a VM anterior, incluindo o disco do SO e discos de dados:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Iniciar a VM após a conversão para discos geridos utilizando [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). O exemplo seguinte reinicia a VM anterior:

  ```azurepowershell-interactive
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Converter VMs num conjunto de disponibilidade

Se as VMs que pretende converter gerido discos estão num conjunto de disponibilidade, terá primeiro de converter a conjunto de disponibilidade para um conjunto de disponibilidade gerido.

1. Converter o conjunto através de disponibilidade de [atualização AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) cmdlet. O exemplo seguinte atualiza o conjunto nomeada de disponibilidade `myAvailabilitySet` no grupo de recursos denominado `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Se a região onde definir a sua disponibilidade está localizada tem apenas 2 domínios de falhas gerido, mas o número de domínios de falhas não gerido 3, este comando apresenta um erro semelhante a "o número de domínios de falhas especificado 3 tem de estar no intervalo de 1 a 2." Para resolver o erro, o domínio de falhas de atualização 2 e atualização `Sku` para `Aligned` da seguinte forma:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Desalocar e converter as VMs no conjunto de disponibilidade. O script seguinte deallocates cada VM utilizando o [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet, converte-o utilizando [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)e reinicia-lo utilizando [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm):

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Resolução de problemas

Se ocorrer um erro durante a conversão, ou se uma VM está em estado de falha devido a problemas de uma conversão anterior, execute o `ConvertTo-AzureRmVMManagedDisk` cmdlet novamente. Uma repetição simple unblocks, normalmente, a situação.
Antes de converter, certifique-se a todas as extensões VM estão no estado "Foi concluída com êxito a aprovisionar" ou a conversão irá falhar com o código de erro 409.


## <a name="next-steps"></a>Passos seguintes

[Converter discos geridos padrão premium](convert-disk-storage.md)

Efetuar uma cópia só de leitura de uma VM utilizando [instantâneos](snapshot-copy-managed-disk.md).

