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
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 91ef7f432d0954cc8456e5d98c48943aa0ad72a7
ms.lasthandoff: 04/12/2017


---

# <a name="create-a-windows-virtual-machine-with-powershell"></a>Criar máquinas virtuais do Windows com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia detalha a utilização do PowerShell para criar e uma máquina virtual do Azure a executar o Windows Server 2016.  Depois de concluída a implementação, vamos ligar ao servidor e instalar o IIS.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

Além disso, certifique-se de que a versão mais recente do módulo do Azure PowerShell foi instalada. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-networking-resources"></a>Criar recursos de rede

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Crie uma rede virtual, uma sub-rede e um endereço IP público. 
Estes recursos são utilizados para fornecer conectividade de rede à máquina virtual e ligá-la à Internet.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra do grupo de segurança de rede. 
O grupo de segurança de rede protege a máquina virtual com regras de entrada e de saída. Neste caso, é criada uma regra de entrada para a porta 3389, que permite ligações de ambiente de trabalho remotas recebidas.  Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego da Web de entrada.

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
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Crie uma placa de rede para a máquina virtual. 
A placa de rede liga a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
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
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Criar a máquina virtual.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto à máquina virtual.

Execute os seguintes comandos para devolver o endereço IP público da máquina virtual.  Tome nota deste endereço IP, para que se possa ligar ao mesmo com o seu browser e testar a conectividade Web num passo posterior.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP pelo `publicIPAddress` da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```bash 
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalar o IIS através do PowerShell

Agora que iniciou sessão na VM do Azure, pode utilizar uma única linha do PowerShell para instalar o IIS e ativar a regra de firewall local para permitir o tráfego da Web.  Abra uma janela do PowerShell e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Certifique-se de que utiliza o `publicIpAddress` que documentou acima para visitar a página predefinida. 

![Site predefinido do IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, pode ser utilizado o seguinte comando para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre instalação de uma função e a configuração de firewall](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar exemplos do PowerShell de implementação de VM](powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
