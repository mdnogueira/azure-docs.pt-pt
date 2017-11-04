---
title: "Redes virtuais do Azure e máquinas virtuais do Windows | Microsoft Docs"
description: "Tutorial - Gerir redes virtuais do Azure e máquinas virtuais do Windows com o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/12/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 21f2d586a4c468071bec55c65005b35baf323fe7
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gerir redes virtuais do Azure e máquinas virtuais do Windows com o Azure PowerShell

Máquinas virtuais do Azure utilizar redes do Azure para a comunicação de rede internos e externos. Este tutorial explica duas máquinas virtuais a implementar e configurar redes do Azure para estas VMs. Os exemplos neste tutorial partem do princípio de que as VMs estão a alojar uma aplicação web com um back-end da base de dados, no entanto, uma aplicação não está implementada no tutorial. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual e sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteja o tráfego de rede
> * Criar a VM de back-end

Ao concluir este tutorial, pode ver estes recursos criados:

![Rede virtual com duas sub-redes](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* -a rede virtual que as VMs utilizam para comunicar entre si e a internet.
- *myFrontendSubnet* -a sub-rede no *myVNet* utilizado por recursos front-end.
- *myPublicIPAddress* -o endereço IP público utilizado para acesso *myFrontendVM* da internet.
- *myFrontentNic* -interface de rede utilizado pelo *myFrontendVM* para comunicar com *myBackendVM*.
- *myFrontendVM* -VM o utilizada para comunicar entre a internet e *myBackendVM*.
- *myBackendNSG* -o grupo de segurança de rede que controla a comunicação entre o *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet* -a sub-rede associada *myBackendNSG* e utilizados pelos recursos de back-end.
- *myBackendNic* -interface de rede utilizado pelo *myBackendVM* para comunicar com *myFrontendVM*.
- *myBackendVM* -a VM que utiliza a porta 1433 para comunicar com *myFrontendVM*.

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Para localizar a versão, execute `Get-Module -ListAvailable AzureRM`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

## <a name="vm-networking-overview"></a>Descrição geral de redes VM

Redes virtuais do Azure permitem ligações de rede seguro entre máquinas virtuais, a internet e outros serviços do Azure, tais como a base de dados SQL do Azure. Redes virtuais são divididas em segmentos lógicos denominados sub-redes. Sub-redes são utilizadas para o fluxo de controlo de rede e, como um limite de segurança. Quando implementar uma VM, geralmente inclui uma interface de rede virtual, o que está ligada a uma sub-rede.

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e sub-rede

Para este tutorial, é criada uma única rede virtual com duas sub-redes. Uma sub-rede do front-end para alojar uma aplicação web e uma sub-rede de back-end para o alojamento de um servidor de base de dados.

Antes de poder criar uma rede virtual, crie um grupo de recursos utilizando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo seguinte cria um grupo de recursos denominado *myRGNetwork* no *EastUS* localização:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Criar configurações de sub-rede

Criar uma configuração de sub-rede com o nome *myFrontendSubnet* utilizando [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

E, criar uma configuração de sub-rede com o nome *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Criar a rede virtual

Criar uma VNET com o nome *myVNet* utilizando *myFrontendSubnet* e *myBackendSubnet* utilizando [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Neste momento, uma rede foi criada e segmentada em duas sub-redes, um para serviços front-end e outra para serviços de back-end. Na secção seguinte, as máquinas virtuais são criadas e ligadas a estas sub-redes.

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Um endereço IP público permite que os recursos do Azure esteja acessível na internet. O método de atribuição do endereço IP público pode ser configurado como dinâmico ou estático. Por predefinição, um endereço IP público é dinamicamente atribuído. Endereços IP dinâmicos são lançados quando uma VM é desalocada. Este comportamento faz com que o endereço IP alterar durante qualquer operação que inclua uma Desalocação da VM.

O método de alocação pode ser definido como estático, que garante que o endereço IP permanecerá atribuído a uma VM, mesmo durante um Estado desalocado. Quando utilizar um endereço IP estaticamente atribuído, não é possível especificar o endereço IP de si próprio. Em vez disso, é atribuído partir de um conjunto de endereços disponíveis.

Criar um endereço IP público com o nome *myPublicIPAddress* utilizando [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Pode alterar o parâmetro - AllocationMethod para `Static` para atribuir um endereço IP público estático.

## <a name="create-a-front-end-vm"></a>Criar uma VM de front-end

Para uma VM comunicar numa rede virtual, necessita de uma interface de rede virtual (NIC). Criar uma NIC utilizando [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Definir o nome de utilizador e palavra-passe necessária para a conta de administrador sobre a utilização de VM [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Utilizar estas credenciais para ligar à VM nos passos adicionais:

```azurepowershell-interactive
$cred = Get-Credential
```

Criar as VMs com [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [conjunto AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [conjunto AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [AzureRmVMNetworkInterface adicionar](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface), e [novo-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="secure-network-traffic"></a>Proteja o tráfego de rede

Os grupos de segurança de rede (NSG) contêm uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a Redes Virtuais do Azure (VNet). Os NSGs podem ser associados a sub-redes ou interfaces de rede individuais. Quando um NSG é associado uma interface de rede, aplica-se apenas a VM associada. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma.

### <a name="network-security-group-rules"></a>Regras do grupo de segurança de rede

Regras do NSG definem portas de rede durante o qual o tráfego é permitido ou negado. As regras podem incluir intervalos de endereços IP de origem e de destino para que o tráfego é controlado entre sistemas específicos ou sub-redes. Regras do NSG também incluem uma prioridade de (entre 1 — e 4096). As regras são avaliadas por ordem de prioridade. Uma regra com uma prioridade de 100 é avaliada antes de uma regra com prioridade 200.

Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar.

- **Rede virtual** - tráfego com origem e de fim numa rede virtual é permitido nas direções de entrada e saídas.
- **Internet** - o tráfego de saída é permitido, mas o tráfego de entrada está bloqueado.
- **O Balanceador de carga** -Balanceador de carga permitir do Azure sonde o estado de funcionamento das VMs e instâncias de função. Se não estiver a utilizar um conjunto com balanceamento de carga, pode substituir esta regra.

### <a name="create-network-security-groups"></a>Criar grupos de segurança de rede

Criar uma regra de entrada com o nome *myFrontendNSGRule* para permitir o tráfego web recebido em *myFrontendVM* utilizando [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Pode limitar o tráfego interno para *myBackendVM* de apenas *myFrontendVM* criando um NSG para a sub-rede de back-end. O exemplo seguinte cria uma regra NSG designada *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Adicionar um grupo de segurança de rede com o nome *myFrontendNSG* utilizando [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Agora, adicione um grupo de segurança de rede com o nome *myBackendNSG* utilizando New-AzureRmNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Adicione os grupos de segurança de rede para as sub-redes:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Criar uma VM de back-end

É a forma mais fácil para criar a VM de back-end para este tutorial, utilizando uma imagem do SQL Server. Este tutorial só cria a VM com o servidor de base de dados, mas não fornece informações sobre como aceder à base de dados.

Criar *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Defina o nome de utilizador e palavra-passe necessária para a conta de administrador na VM com o Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Criar *myBackendVM*:

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

A imagem que é utilizada com o SQL Server instalada, mas não é utilizada neste tutorial. Está incluído para lhe mostrar como pode configurar uma VM para processar o tráfego web e uma VM processe a gestão de base de dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou e redes do Azure como relacionados para máquinas virtuais protegidas. 

> [!div class="checklist"]
> * Criar uma rede virtual e sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteja o tráfego de rede
> * Criar uma VM de back-end

Avançar para o próximo tutorial para saber mais sobre a monitorização de proteger dados em máquinas virtuais utilizando cópia de segurança do Azure.

> [!div class="nextstepaction"]
> [Cópia de segurança de máquinas virtuais do Windows no Azure](./tutorial-backup-vms.md)
