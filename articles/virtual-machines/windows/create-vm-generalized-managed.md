---
title: Criar VM a partir de uma imagem VM gerida no Azure | Microsoft Docs
description: "Crie uma máquina virtual do Windows a partir de uma imagem VM gerida generalizada com o Azure PowerShell, o modelo de implementação Resource Manager."
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
ms.date: 05/22/2017
ms.author: cynthn
ms.openlocfilehash: 8157b77976a2152cc0b012fe6ad5fa116223bef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Criar uma VM a partir de uma imagem gerida

Pode criar várias VMs a partir de uma imagem VM gerida no Azure. Uma imagem VM gerida contém as informações necessárias para criar uma VM, incluindo os SO e discos de dados. Os VHDs que compõem a imagem, incluindo os discos de SO e discos de dados, são armazenadas como discos geridos. 


## <a name="prerequisites"></a>Pré-requisitos

Tem de ter já [criada uma imagem VM gerida](capture-image-resource.md) a utilizar para criar a nova VM. 

Certifique-se de que tem as versões mais recentes dos módulos AzureRM.Compute e AzureRM.Network PowerShell. Abra uma linha de comandos do PowerShell como administrador e execute o seguinte comando para instalá-los.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Para obter mais informações, consulte [controlo de versões do Azure PowerShell](/powershell/azure/overview).



## <a name="collect-information-about-the-image"></a>Recolher informações sobre a imagem

Primeiro é necessário recolher informações básicas sobre a imagem e criar uma variável para a imagem. Este exemplo utiliza uma imagem VM gerida com o nome **myImage** que está a ser o **myResourceGroup** grupo de recursos a **Central EUA oeste** localização. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede designada **mySubnet** com o prefixo de endereço de **10.0.0.0/24**.  
   
    ```azurepowershell-interactive
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. Este exemplo cria uma rede virtual denominada **myVnet** com o prefixo de endereço de **10.0.0.0/16**.  
   
    ```azurepowershell-interactive
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de rede e o endereço IP pública

Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público com o nome **myPip**. 
   
    ```azurepowershell-interactive
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar o NIC. Este exemplo cria um NIC com o nome **myNic**. 
   
    ```azurepowershell-interactive
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para conseguir iniciar sessão na sua VM através de RDP, tem de ter uma regra de segurança de rede (NSG) que permite o acesso RDP na porta 3389. 

Este exemplo cria um NSG denominado **myNsg** que contenha uma regra denominada **myRdpRule** que permite tráfego RDP de através da porta 3389. Para obter mais informações sobre NSGs, consulte [a abertura de portas para uma VM no Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual

Crie uma variável para a rede virtual foi concluída. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Obter as credenciais para a VM

O cmdlet seguinte irá abrir uma janela onde vai introduzir um novo nome de utilizador e palavra-passe para utilizar como conta de administrador local para aceder remotamente a VM. 

```azurepowershell-interactive
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Defina variáveis para o nome da VM, o nome do computador e o tamanho da VM

1. Crie variáveis para o nome da VM e o nome de computador. Neste exemplo define o nome da VM como **myVM** e o nome do computador como **myComputer**.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Defina o tamanho da máquina virtual. Este exemplo cria **Standard_DS1_v2** tamanho de VM. Consulte o [tamanhos de VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) documentação para obter mais informações.

    ```azurepowershell-interactive
    $vmSize = "Standard_DS1_v2"
    ```

3. Adicione o nome da VM e o tamanho para a configuração de VM.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Defina a imagem VM como imagem de origem para a nova VM

Defina a imagem de origem com o ID da imagem VM gerido.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Definir a configuração do SO e adicione o NIC.

Introduza o tipo de armazenamento (PremiumLRS ou StandardLRS) e o tamanho do disco de SO. Neste exemplo define o tipo de conta para **PremiumLRS**, o tamanho do disco para **128 GB** e colocação em cache do disco para **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Crie a VM

Criar a nova Vm a utilizar a configuração que vamos ter criado e armazenado no **$vm** variável.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Quando terminar, deverá ver a VM criada recentemente o [portal do Azure](https://portal.azure.com) em **procurar** > **máquinas virtuais**, ou utilizando os seguintes comandos do PowerShell:

```azurepowershell-interactive
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerir a sua nova máquina virtual com o Azure PowerShell, consulte [criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

