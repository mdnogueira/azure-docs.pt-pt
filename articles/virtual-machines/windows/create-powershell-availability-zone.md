---
title: "Criar uma VM do Windows numa zona definida – Azure PowerShell | Microsoft Docs"
description: "Criar uma máquina virtual do Windows numa zona de disponibilidade com o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: e86fcb4dbf170e5bc07553165e09d6fc3d3cf283
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>Criar uma máquina virtual do Windows numa zona de disponibilidade com o PowerShell

Este artigo descreve a utilização do Azure PowerShell para criar uma máquina virtual do Azure com o Windows Server 2016 uma zona de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona separada fisicamente numa região do Azure. Utilize as zonas de disponibilidade para proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Certifique-se de que instalou o módulo Azure PowerShell mais recente. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Neste exemplo, é criado um grupo de recursos chamado *myResourceGroup* na região *eastus2*. East US 2 (E.U.A. Leste 2) é uma das regiões do Azure que suporta zonas de disponibilidade em modo de pré-visualização.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location eastus2
```

## <a name="create-networking-resources"></a>Criar recursos de rede

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Criar uma rede virtual, uma sub-rede e um endereço IP público 
Estes recursos são utilizados para fornecer conectividade de rede à máquina virtual e ligá-la à Internet. Crie o endereço IP numa zona de disponibilidade, *2* neste exemplo. Para criar a VM numa zona de disponibilidade (um procedimento mostrado num passo posterior), especifique a mesma zona utilizada para criar o endereço IP.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede 
O grupo de segurança de rede protege a máquina virtual com regras de entrada e de saída. Neste caso, é criada uma regra de entrada para a porta 3389, que permite ligações de ambiente de trabalho remotas recebidas. Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego da Web de entrada.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Criar uma placa de rede para a máquina virtual 
Crie uma placa de rede com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) para a máquina virtual. A placa de rede liga a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Criar uma configuração da máquina virtual. Esta configuração inclui as definições que são utilizadas ao implementar a máquina virtual, como uma imagem de máquina virtual, o tamanho e a configuração da autenticação. O tamanho *Standard_DS1_v2* deste exemplo é suportado no modo de pré-visualização de zonas de disponibilidade. Esta configuração também especifica a zona de disponibilidade que definiu ao criar o endereço IP. Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a máquina virtual.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Criar a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="zone-for-ip-address-and-managed-disk"></a>Zona para o endereço IP e o disco gerido

Criou o recurso de endereço IP da VM na mesma zona de disponibilidade que a VM. O recurso de disco gerido para a VM também é criado na mesma zona de disponibilidade. Pode verificar isto com [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk):

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

O resultado mostra que o disco gerido está na mesma zona de disponibilidade que a VM:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```




## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma VM uma zona de disponibilidade. Saiba mais sobre [regiões e disponibilidade](regions-and-availability.md) para VMs do Azure.