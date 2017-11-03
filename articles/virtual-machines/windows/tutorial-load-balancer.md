---
title: Como carregar equilibrar as virtual machines Windows no Azure | Microsoft Docs
description: "Saiba como utilizar o Balanceador de carga do Azure para criar uma aplicação de elevada disponibilidade e segura entre três VMs do Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6738d88d5a0430abaf3855dbf97a618e4c83617f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Como carregar equilibrar as virtual machines Windows no Azure para criar uma aplicação altamente disponível
Balanceamento de carga fornece um nível mais elevado de disponibilidade propagando-se os pedidos recebidos em várias máquinas virtuais. Neste tutorial, pode saber mais sobre os diferentes componentes do Balanceador de carga do Azure que distribui o tráfego de e para fornecem elevada disponibilidade. Saiba como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do Balanceador de carga
> * Criar regras de tráfego de Balanceador de carga
> * Utilizar a extensão de Script personalizado para criar um site do IIS básico
> * Criar máquinas virtuais e ligue a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover as VMs a partir de um balanceador de carga

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).


## <a name="azure-load-balancer-overview"></a>Descrição geral do Balanceador de carga do Azure
Um balanceador de carga do Azure é um balanceador de carga de camada 4 (TCP, UDP) que fornece elevada disponibilidade, distribuição de tráfego de entrada entre VMs em bom estado. Uma sonda de estado de funcionamento do Balanceador de carga monitoriza uma porta especificada em cada VM e apenas distribui o tráfego para uma VM operacional.

É possível definir uma configuração de IP Front-end que contém um ou mais endereços IP públicos. Esta configuração de IP Front-end permite que o Balanceador de carga e aplicações para estar acessível através da Internet. 

As máquinas virtuais ligar a um balanceador de carga com o seu cartão de interface de rede virtual (NIC). Para distribuir o tráfego para as VMs, um conjunto de endereços de back-end contém o IP endereços dos virtuais (NICs) ligadas ao balanceador de carga.

Para controlar o fluxo de tráfego, é possível definir as regras de Balanceador de carga para portas específicas e protocolos que mapeiam para as suas VMs.


## <a name="create-azure-load-balancer"></a>Criar Balanceador de carga do Azure
Esta secção descreve em detalhe como pode criar e configurar cada componente do Balanceador de carga. Antes de poder criar seu Balanceador de carga, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupLoadBalancer* no *EastUS* localização:

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS
```

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público
Para aceder à sua aplicação na Internet, terá um endereço IP público para o Balanceador de carga. Criar um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). O exemplo seguinte cria um endereço IP público com o nome *myPublicIP* no *myResourceGroupLoadBalancer* grupo de recursos:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Criar um endereço IP de front-end com [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). O exemplo seguinte cria um endereço IP de front-end com o nome *myFrontEndPool*: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

Criar um conjunto de endereços de back-end com [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). O exemplo seguinte cria um conjunto de endereços de back-end com o nome *myBackEndPool*:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Agora, crie o Balanceador de carga com [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). O exemplo seguinte cria um balanceador de carga com o nome *myLoadBalancer* utilizando o *myPublicIP* endereço:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Criar uma sonda do Estado de funcionamento
Para permitir que o Balanceador de carga monitorizar o estado da sua aplicação, pode utilizar uma sonda do Estado de funcionamento. A sonda de estado de funcionamento dinamicamente adiciona ou remove as VMs a rotação de Balanceador de carga com base na respetiva resposta para verificações de estado de funcionamento. Por predefinição, uma VM é removida da distribuição do Balanceador de carga após duas falhas consecutivas em intervalos de 15 segundo. Criar uma sonda do Estado de funcionamento com base num protocolo ou uma página de verificação do Estado de funcionamento específico para a sua aplicação. 

O exemplo seguinte cria uma sonda TCP. Também pode criar das sondas personalizadas do HTTP para obter mais detalhada do Estado de funcionamento verificações. Quando utilizar uma pesquisa HTTP personalizada, tem de criar a página de verificação do Estado de funcionamento, tal como *healthcheck.aspx*. A sonda tem de devolver um **HTTP 200 OK** resposta para o Balanceador de carga manter o anfitrião no rotação.

Para criar uma sonda do Estado de funcionamento TCP, utilize [adicionar AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). O exemplo seguinte cria uma sonda do Estado de funcionamento com o nome *myHealthProbe* que monitoriza a cada VM:

```powershell
Add-AzureRmLoadBalancerProbeConfig `
  -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Atualizar o Balanceador de carga com [conjunto AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de Balanceador de carga
Uma regra de Balanceador de carga é utilizada para definir a forma como o tráfego é distribuído para as VMs. É possível definir a configuração de IP Front-end o tráfego de entrada e o conjunto IP back-end para receber o tráfego, juntamente com a porta de origem e de destino necessário. Para garantir que apenas as VMs bom receberem tráfego, também é possível definir a sonda de estado de funcionamento para utilizar.

Criar uma regra de Balanceador de carga com [adicionar AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). O exemplo seguinte cria uma regra de Balanceador de carga com o nome *myLoadBalancerRule* e equilibrar o tráfego na porta *80*:

```powershell
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name myHealthProbe

Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Atualizar o Balanceador de carga com [conjunto AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Antes de implementar algumas VMs e pode testar o seu balanceador, crie os suporte recursos de rede virtual. Para obter mais informações sobre as redes virtuais, consulte o [gerir redes virtuais do Azure](tutorial-virtual-network.md) tutorial.

### <a name="create-network-resources"></a>Criar recursos de rede
Criar uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo seguinte cria uma rede virtual denominada *myVnet* com *mySubnet*:

```powershell
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Criar uma regra de grupo de segurança de rede com [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig), em seguida, crie um grupo de segurança de rede com [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Adicionar o grupo de segurança de rede para a sub-rede com [conjunto AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) e, em seguida, atualize a rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). 

O exemplo seguinte cria uma regra de grupo de segurança de rede com o nome *myNetworkSecurityGroup* e aplica-se de que *mySubnet*:

```powershell
# Create security rule config
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create the network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule

# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

NICs virtuais são criados com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). O exemplo seguinte cria três NICs virtuais. (Uma NIC virtual para cada VM que criar para a aplicação nos passos seguintes). Pode criar o NICs virtuais adicionais e VMs em qualquer altura e adicioná-los para o Balanceador de carga:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location EastUS `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a elevada disponibilidade da sua aplicação, coloque as suas VMs num conjunto de disponibilidade.

Criar um conjunto de disponibilidade com [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). O exemplo seguinte cria um conjunto nomeado de disponibilidade *myAvailabilitySet*:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location EastUS `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Definir um administrador do nome de utilizador e palavra-passe para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Agora pode criar as VMs com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo seguinte cria três VMs:

```powershell
for ($i=1; $i -le 3; $i++)
{
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_D1 `
    -AvailabilitySetId $availabilitySet.Id
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk$i `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
  $nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic$i
  $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
  New-AzureRmVM `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Location EastUS `
    -VM $vm
}
```

Demora alguns minutos para criar e configurar todos os três VMs.

### <a name="install-iis-with-custom-script-extension"></a>Instalar o IIS com a extensão de Script personalizado
Um tutorial anterior em [como personalizar uma máquina virtual do Windows](tutorial-automate-vm-deployment.md), aprendeu a automatizar a personalização de VM com o personalizada Script extensão para Windows. Pode utilizar a mesma abordagem para instalar e configurar o IIS nas suas VMs.

Utilize [conjunto AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar a extensão de Script personalizado. As execuções de extensão `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web do IIS e, em seguida, as atualizações a *Default.htm* página para mostrar o nome do anfitrião da VM:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Balanceador de carga de teste
Obter o endereço IP público do seu Balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criado anteriormente:

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

Em seguida, pode introduzir o endereço IP público para um web browser. O Web site é apresentado, incluindo o nome do anfitrião da VM que o Balanceador de carga distribuído tráfego como no exemplo seguinte:

![Web site do IIS em execução](./media/tutorial-load-balancer/running-iis-website.png)

Para ver o Balanceador de carga distribuir o tráfego entre todas as três VMs executar a sua aplicação, pode forçar atualizar o browser.


## <a name="add-and-remove-vms"></a>Adicionar e remover VMs
Terá de efetuar a manutenção em VMs executar a sua aplicação, como a instalação de atualizações do SO. Para lidar com maior tráfego para a sua aplicação, poderá ter de adicionar VMs adicionais. Esta secção mostra como remover ou adicionar uma VM do Balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do Balanceador de carga
Obter o cartão de interface de rede com [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface), em seguida, defina o *LoadBalancerBackendAddressPools* propriedade de NIC virtual para *$null*. Por fim, atualize o NIC virtual.:

```powershell
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Para ver o Balanceador de carga distribuir o tráfego entre as restantes duas VMs, executar a sua aplicação, pode forçar-atualizar o browser. Agora pode efetuar a manutenção da VM, tais como instalar atualizações do SO ou efetuar um reinício VM.

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM para o Balanceador de carga
Após efetuar a manutenção VM ou se precisa de expandir a capacidade, defina o *LoadBalancerBackendAddressPools* propriedade de NIC virtual para o *BackendAddressPool* de [ Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

Obter o Balanceador de carga:

```powershell
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um balanceador de carga e anexados VMs. Aprendeu a:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma sonda de estado de funcionamento do Balanceador de carga
> * Criar regras de tráfego de Balanceador de carga
> * Utilizar a extensão de Script personalizado para criar um site do IIS básico
> * Criar máquinas virtuais e ligue a um balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover as VMs a partir de um balanceador de carga

Avançar para o próximo tutorial para saber como gerir redes VM.

> [!div class="nextstepaction"]
> [Gerir VMs e redes virtuais](./tutorial-virtual-network.md)
