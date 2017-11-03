---
title: "Migrar uma VM clássica para uma VM de disco gerida ARM | Microsoft Docs"
description: "Migre uma única VM do Azure do modelo de implementação clássica para discos geridos no modelo de implementação Resource Manager."
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
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: 82389834d85981c0ed71bdcc891fbfdbe1377654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>Migrar manualmente uma VM clássica para ARM geridos disco VM nova do VHD 


Esta secção ajuda-o a migrar as suas VMs do Azure existente a partir do modelo de implementação clássica para [discos geridos](managed-disks-overview.md) no modelo de implementação Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Planear a migração para discos geridos

Esta secção ajuda-o a tomar a decisão de melhor em tipos de disco e de VM.


### <a name="location"></a>Localização

Escolha uma localização onde discos gerida do Azure estão disponíveis. Se estiver a migrar para os discos Premium gerido, certifique-se também que armazenamento Premium está disponível na região onde estiver a planear migrar para o. Consulte [byRegion de serviços do Azure](https://azure.microsoft.com/regions/#services) para obter informações atualizadas em localizações disponíveis.

### <a name="vm-sizes"></a>Tamanhos de VM

Se estiver a migrar discos geridos de Premium, terá de atualizar o tamanho da VM para Premium com capacidade de tamanho de armazenamento disponível na região onde está localizada a VM. Reveja os tamanhos VM que fazem com capacidade de armazenamento Premium. As especificações de tamanho da VM do Azure estão listadas na [tamanhos das virtual machines](sizes.md).
Reveja as características de desempenho de máquinas virtuais que funcionam com o Premium Storage e escolha o tamanho VM mais adequado que melhor se adapta às sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível na VM para o tráfego de disco da unidade.

### <a name="disk-sizes"></a>Tamanhos de disco

**Premium discos geridos**

Existem sete tipos de discos de gerido premium que podem ser utilizados com a VM e cada um tem IOPs específicos e débito limites. Quando escolher o tipo de disco Premium para VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade, considere estes limites e carrega das horas de ponta.

| Tipo de discos Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo | 

**Discos geridos padrão**

Existem sete tipos de discos padrão gerido que podem ser utilizados com a VM. Cada um deles tem capacidade de diferentes, mas tem o mesmo IOPS e limites de débito. Escolha o tipo de discos padrão geridos com base nas necessidades de capacidade da sua aplicação.

| Tipo de Disco Standard  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPs por disco       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 


### <a name="disk-caching-policy"></a>Política de colocação em cache em disco 

**Premium discos geridos**

Por predefinição, o disco de política de colocação em cache é *só de leitura* para todos os dados discos Premium, e *leitura e escrita* para o disco de sistema operativo Premium ligados à VM. Esta definição de configuração é recomendada para alcançar o desempenho ideal para IOs a aplicação. Pesado de escrita ou só de escrita para discos de dados (por exemplo, ficheiros de registo do SQL Server), desative a colocação em cache no disco, de modo a que pode alcançar um melhor desempenho de aplicações.

### <a name="pricing"></a>Preços

Reveja o [preços para discos geridos](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Preços dos discos de geridos Premium é a mesmo discos Premium não gerido. Mas preços para os discos padrão geridos são diferente de discos padrão de não gerido.


## <a name="checklist"></a>Lista de verificação

1.  Se estiver a migrar para os discos Premium gerido, certifique-se de que está disponível na região que estiver a migrar para.

2.  Decida a série VM nova que irá utilizar. Deve ser um com capacidade de armazenamento Premium se estiver a migrar para os discos Premium geridos.

3.  Decida o exato tamanho da VM que irá utilizar que estão disponíveis na região que estiver a migrar. Tamanho da VM tem de ser suficientemente grande para suportar o número de discos de dados que tiver. Por exemplo, se tiver discos de dados quatro, a VM tem de ter dois ou mais núcleos. Além disso, considere o processamento de energia, memória e as necessidades de largura de banda de rede.

4.  Ter à mão, incluindo a lista de discos e blobs VHD correspondentes os detalhes da VM atuais.

Prepare a sua aplicação para o período de indisponibilidade. Para efetuar uma migração limpa, terá de parar todas as o processamento no sistema atual. Apenas, em seguida, pode obtê-lo para o estado consistente que pode migrar para a plataforma de novo. Duração do período de indisponibilidade depende da quantidade de dados nos discos para migrar.


## <a name="migrate-the-vm"></a>Migrar a VM

Prepare a sua aplicação para o período de indisponibilidade. Para efetuar uma migração limpa, terá de parar todas as o processamento no sistema atual. Apenas, em seguida, pode obtê-lo para o estado consistente que pode migrar para a plataforma de novo. Duração do período de indisponibilidade depende da quantidade de dados nos discos para migrar.


1.  Em primeiro lugar, defina os parâmetros comuns:

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Crie um disco de SO gerido com o VHD da VM clássico.

    Certifique-se de que forneceu o URI do VHD SO para o parâmetro $osVhdUri concluído. Além disso, introduza **- AccountType** como **PremiumLRS** ou **StandardLRS** com base no tipo de discos (Premium ou Standard) estiver a migrar para.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Anexe o disco de SO para a nova VM.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Criar um disco de dados geridos a partir do ficheiro VHD de dados e adicione-o para a nova VM.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Criar nova VM ao definir o IP público, rede Virtual e a NIC.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Poderão existir passos adicionais necessários para suportar a sua aplicação que não é ser abrangida neste guia.
>
>

## <a name="next-steps"></a>Passos seguintes

- Ligar à máquina virtual. Para obter instruções, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure com o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

