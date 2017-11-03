---
title: Criar uma VM do Windows a partir de um VHD especializado no Azure | Microsoft Docs
description: "Crie uma nova VM do Windows ao anexar um disco gerido especializado como disco do SO a utilizar no modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cynthn
ms.openlocfilehash: 39cbd30102813a4502cd25811589d04a9adb0aa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-vm-from-a-specialized-disk"></a>Criar uma VM do Windows a partir de um disco especializado

Crie uma nova VM ao anexar um disco gerido especializado como disco do SO através do Powershell. Um disco especializado é uma cópia de disco rígido virtual (VHD) de uma VM existente que mantém as contas de utilizador, aplicações e outros dados de estado de original VM. 

Quando utilizar um VHD especializado para criar uma nova VM, a nova VM mantém o nome do computador da original VM. Também é possível mantida outras informações específicas do computador e, em alguns casos, estas informações de duplicados pode causar problemas. Lembre-se de que tipos de informações específicas do computador as suas aplicações dependem quando copiar uma VM.

Tem duas opções:
* [Carregar um VHD](#option-1-upload-a-specialized-vhd)
* [Copiar uma VM do Azure existente](#option-2-copy-an-existing-azure-vm)

Este tópico mostra como utilizar discos geridos. Se tiver uma implementação de legado que necessita de utilizar uma conta do storage, consulte [criar uma VM a partir de um VHD numa conta de armazenamento especializado](sa-create-vm-specialized.md)

## <a name="before-you-begin"></a>Antes de começar
Se utilizar o PowerShell, certifique-se de que tem a versão mais recente do módulo do AzureRM.Compute PowerShell. 

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para obter mais informações, consulte [controlo de versões do Azure PowerShell](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: Carregar um VHD especializado

Pode carregar o VHD de uma VM especializada criado com uma ferramenta de virtualização no local, como o Hyper-V ou numa VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Se pretender utilizar o VHD como-para criar uma nova VM, certifique-se de que os seguintes passos são concluídos. 
  
  * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize a VM com o Sysprep.
  * Remova quaisquer agentes que estão instalados na VM (como as ferramentas do VMware) e ferramentas de Virtualização do convidado.
  * Certifique-se de que a VM está configurada para solicitar o respetivo endereço IP e as definições de DNS através do DHCP. Isto garante que o servidor obtém um endereço IP dentro da VNet durante o arranque. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Necessita de uma conta de armazenamento do Azure para armazenar o VHD foi carregado. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Para mostrar as contas de armazenamento disponíveis, escreva:

```powershell
Get-AzureRmStorageAccount
```

Se pretender utilizar uma conta de armazenamento existente, avance para o [carrega o VHD](#upload-the-vhd-to-your-storage-account) secção.

Se precisar de criar uma conta de armazenamento, siga estes passos:

1. Tem o nome do grupo de recursos onde a conta de armazenamento deve ser criada. Para obter todos os grupos de recursos que estão na sua subscrição, escreva:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para criar um grupo de recursos denominado *myResourceGroup* no *EUA oeste* região, escreva:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Criar uma conta de armazenamento com o nome *mystorageaccount* neste grupo de recursos utilizando o [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD à sua conta de armazenamento 
Utilize o [adicionar AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet para carregar o VHD para um contentor na sua conta de armazenamento. Neste exemplo carrega o ficheiro *myVHD.vhd* de `"C:\Users\Public\Documents\Virtual hard disks\"` para uma conta de armazenamento com o nome *mystorageaccount* no *myResourceGroup* grupo de recursos. O ficheiro é armazenado num contentor com o nome *mycontainer* e o novo nome de ficheiro será *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName -Destination $urlOfUploadedVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se tiver êxito, receberá uma resposta semelhante a isto:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da sua ligação de rede e o tamanho do ficheiro VHD, este comando pode demorar algum tempo para concluir

### <a name="create-a-managed-disk-from-the-vhd"></a>Criar um disco gerido do VHD

Criar um disco gerido do VHD especializado na sua conta de armazenamento utilizar [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Este exemplo utiliza **myOSDisk1** para o nome do disco, coloca o disco no *StandardLRS* armazenamento e utiliza *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* como o URI para o VHD de origem.

Crie um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location -Name $destinationResourceGroup
```

Crie o novo disco de SO do VHD foi carregado. 

```powershell
$sourceUri = (https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-2-copy-an-existing-azure-vm"></a>Opção 2: Copiar uma VM do Azure existente

Pode criar uma cópia de uma VM que utiliza discos geridos por um instantâneo da VM, em seguida, utilizar esse instantâneo para criar uma nova geridos disco e uma nova VM.


### <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco de SO

Pode demorar uma VM de instantâneo do e completa (incluindo todos os discos) ou de apenas um único disco. Os passos seguintes mostram como criar um instantâneo da apenas o disco do SO da VM utilizando o [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) cmdlet. 

Defina alguns parâmetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obter o objeto da VM.

```powershell
$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
```
Obter o nome do disco de SO.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $vm.StorageProfile.OsDisk.Name
```

Crie a configuração de instantâneo. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -OsType Windows -CreateOption Copy -Location $location 
```

Tire o instantâneo.

```powershell
$snapShot = New-AzureRmSnapshot -Snapshot $snapshotConfig -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName
```


Se planear utilizar o instantâneo para criar uma VM que tem de ser a execução elevada, utilize o parâmetro `-AccountType Premium_LRS` com o comando AzureRmSnapshot de novo. O parâmetro cria o instantâneo, de modo a que seja armazenada como um disco de gerido para Premium. Os discos Premium geridos são mais dispendiosos do que padrão. Por isso, não se esqueça de que precisar realmente Premium antes de utilizar o parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco do instantâneo

Criar um disco gerido do instantâneo com [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Este exemplo utiliza *myOSDisk* para o nome do disco.

Crie um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location -Name $destinationResourceGroup
```

Defina o nome do disco de SO. 

```powershell
$osDiskName = 'myOsDisk'
```

Crie o disco gerido.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Criar nova VM 

Crie redes e outros recursos VM a serem utilizadas pela nova VM.

### <a name="create-the-subnet-and-vnet"></a>Criar a sub-rede e a vNet

Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

Crie a sub-rede. Este exemplo cria uma sub-rede designada **mySubNet**, no grupo de recursos **myDestinationResourceGroup**e define o prefixo de endereço de sub-rede **10.0.0.0/24**.
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Crie a vNet. Neste exemplo define o nome de rede virtual para ser **myVnetName**, a localização para **EUA oeste**e o prefixo de endereço para a rede virtual para **10.0.0.0/16**. 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $destinationResourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para conseguir iniciar sessão na sua VM através de RDP, tem de ter uma regra de segurança que permite o acesso RDP na porta 3389. Porque o VHD para a nova VM foi criado a partir de uma VM especializada existente, pode utilizar uma conta da máquina virtual de origem para RDP.

Neste exemplo define o nome NSG para **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $destinationResourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre os pontos finais e as regras do NSG, consulte [a abertura de portas para uma VM no Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

Crie o IP público. Neste exemplo, o nome de endereço IP público está definido como **myIP**.
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $destinationResourceGroup -Location $location `
   -AllocationMethod Dynamic
```       

Criar o NIC. Neste exemplo, o nome NIC está definido como **myNicName**.
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $destinationResourceGroup `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>Definir o nome da VM e o tamanho

Neste exemplo define o nome VM para *myVM* e o tamanho da VM *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicionar o NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicionar o disco de SO 

Adicionar o disco de SO para a configuração utilizando [conjunto AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Neste exemplo, define o tamanho do disco para *128 GB* e anexa o disco gerido como um *Windows* disco do SO.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Conclua a VM 

Criar a VM utilizando [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm)as configurações que acabamos de criar.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se este comando foi concluída com êxito, verá o resultado como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Deverá ver a VM recém-criado está no [portal do Azure](https://portal.azure.com), em **procurar** > **máquinas virtuais**, ou utilizando os seguintes comandos do PowerShell:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para iniciar sessão sua nova máquina virtual, navegue para a VM com o [portal](https://portal.azure.com), clique em **Connect**e abra o ficheiro RDP de ambiente de trabalho remoto. Utilize as credenciais da conta da máquina virtual original para iniciar sessão sua nova máquina virtual. Para obter mais informações, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure com o Windows](connect-logon.md).

