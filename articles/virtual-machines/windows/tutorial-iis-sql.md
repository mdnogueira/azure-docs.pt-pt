---
title: "Crie VMs em execução um SQL Server &#92; IIS &#92;. Pilha de rede no Azure | Microsoft Docs"
description: "Tutorial - instalar um Azure SQL, o IIS, o .NET pilha em máquinas de virtuais do Windows."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Instale um SQL Server &#92; IIS &#92;. Pilha de rede no Azure

Neste tutorial, vamos instalar SQL &#92; IIS &#92;. Pilha de rede com o Azure PowerShell. Este pilha é constituído por dois as VMs com Windows Server 2016, IIS e o .NET e outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma VM com o New-AzVM
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM com o SQL Server
> * Instale a extensão do SQL Server



## <a name="create-a-iis-vm"></a>Criar uma VM de IIS 

Neste exemplo, utilizamos o [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) cmdlet na Shell de nuvem do PowerShell para criar rapidamente uma VM do Windows Server 2016 e, em seguida, instalar o IIS e o .NET Framework. O IIS e as VMs de SQL partilham um grupo de recursos e a rede virtual, pelo que iremos criar variáveis para esses nomes.

Clique em de **tente-** botão para o canto superior direito do bloco de código para iniciar a Shell de nuvem nesta janela. Será pedido para fornecer credenciais para a máquina virtual na linha de comandos.

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Instale o IIS e o .NET framework com a extensão de script personalizado.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>VM do SQL do Azure

Utilizamos uma imagem do marketplace Azure previamente configurada do SQL server para criar a VM do SQL Server. Primeiro, criamos a VM, em seguida, é instalar a extensão de servidor do SQL Server na VM. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Utilize [conjunto AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) para adicionar o [extensão SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) à VM do SQL Server.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, é instalado um SQL Server &#92; IIS &#92;. Pilha de rede com o Azure PowerShell. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM com o New-AzVM
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM com o SQL Server
> * Instale a extensão do SQL Server

Avançar para o próximo tutorial para saber como proteger o servidor web IIS com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor web IIS com certificados SSL](tutorial-secure-web-server.md)

