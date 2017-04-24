---
title: "Guia de Introdução do Azure - Criar PowerShell da VM | Microsoft Docs"
description: "Aprender rapidamente a criar máquinas virtuais do Linux com o PowerShell"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: bffbeb5238ec69d763e1cc7ad3c8a6e4fad34306
ms.lasthandoff: 04/12/2017


---

# <a name="create-a-linux-virtual-machine-with-powershell"></a>Criar máquinas virtuais do Linux com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia detalha a utilização do PowerShell para criar e uma máquina virtual do Azure a executar o Ubuntu 14.04 LTS.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

Além disso, certifique-se de que a versão mais recente do módulo do Azure PowerShell foi instalada. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs).

Finalmente, uma chave SSH pública com o nome `id_rsa.pub` tem de ser armazenada no diretório `.ssh` do seu perfil de utilizador do Windows. Para obter informações detalhadas sobre a criação de chaves SSH para o Azure, consulte [Create SSH keys for Azure (Criar chaves SSH para o Azure)](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

Crie uma rede virtual, uma sub-rede e um endereço IP público. Estes recursos são utilizados para fornecer conectividade de rede à máquina virtual e ligá-la à Internet.

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

Crie um grupo de segurança de rede e uma regra do grupo de segurança de rede. O grupo de segurança de rede protege a máquina virtual com regras de entrada e de saída. Neste caso, é criada uma regra de entrada para a porta 22, que permite ligações SSH recebidas. Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego da Web de entrada.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Crie uma placa de rede para a máquina virtual. A placa de rede liga a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Criar uma configuração da máquina virtual. Esta configuração inclui as definições que são utilizadas ao implementar a máquina virtual, como uma imagem de máquina virtual, o tamanho e a configuração da autenticação.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Criar a máquina virtual.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Depois de a implementação estar concluída, crie uma ligação de SSH com a máquina virtual.

Execute os seguintes comandos para devolver o endereço IP público da máquina virtual.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

A partir de um sistema com SSH instalado, utilize o seguinte comando para ligar à máquina virtual. Se trabalhar no Windows, o [Putty](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty) pode ser utilizado para criar a ligação. 

```bash 
ssh <Public IP Address>
```

Quando lhe for pedido, o nome de utilizador de início de sessão é `azureuser`. Se introduziu uma frase de acesso quando criou chaves SSH, também terá de introduzir esta.


## <a name="install-nginx"></a>Instalar o NGINX

Utilize o script de bash seguinte para atualizar as origens de pacotes e instalar o pacote NGINX mais recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 agora aberta na sua VM a partir da Internet, pode utilizar um browser à sua escolha para ver a página de boas-vindas do NGINX predefinida. Certifique-se de que utiliza o `publicIpAddress` que documentou acima para visitar a página predefinida. 

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>Eliminar máquina virtual

Quando já não for necessário, pode ser utilizado o seguinte comando para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar exemplos do PowerShell de implementação de VM](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

