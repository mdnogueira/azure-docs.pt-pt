---
title: "Guia de Introdução do Azure - Criar PowerShell da VM do Windows | Microsoft Docs"
description: "Aprender rapidamente a criar máquinas virtuais do Windows com o PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 7e00a1fd047cd38b8fb8a7d9e2e03fd203dbaaab
ms.contentlocale: pt-pt
ms.lasthandoff: 05/11/2017


---

# <a name="create-a-windows-virtual-machine-with-powershell"></a>Criar máquinas virtuais do Windows com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia detalha a utilização do PowerShell para criar e uma máquina virtual do Azure a executar o Windows Server 2016. Depois de concluída a implementação, vamos ligar ao servidor e instalar o IIS.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este guia de introdução requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-networking-resources"></a>Criar recursos de rede

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Crie uma rede virtual, uma sub-rede e um endereço IP público. 
Estes recursos são utilizados para fornecer conectividade de rede à máquina virtual e ligá-la à Internet.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location EastUS `
    -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location EastUS `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra do grupo de segurança de rede. 
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
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location EastUS `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Crie uma placa de rede para a máquina virtual. 
Crie uma placa de rede com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) para a máquina virtual. A placa de rede liga a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Criar uma configuração da máquina virtual. Esta configuração inclui as definições que são utilizadas ao implementar a máquina virtual, como uma imagem de máquina virtual, o tamanho e a configuração da autenticação. Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a máquina virtual.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Criar a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location EastUS -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto à máquina virtual.

Utilize o comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver o endereço IP público da máquina virtual. Tome nota deste endereço IP, para que se possa ligar ao mesmo com o seu browser e testar a conectividade Web num passo posterior.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP com o *publicIPAddress* da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```bash 
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalar o IIS através do PowerShell

Agora que iniciou sessão na VM do Azure, pode utilizar uma única linha do PowerShell para instalar o IIS e ativar a regra de firewall local para permitir o tráfego da Web. Abra uma janela do PowerShell e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Certifique-se de que utiliza o *publicIpAddress* que documentou acima para visitar a página predefinida. 

![Site predefinido do IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, pode utilizar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, implementou uma máquina virtual simples, uma regra de grupo de segurança de rede e instalou um servidor Web. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)

