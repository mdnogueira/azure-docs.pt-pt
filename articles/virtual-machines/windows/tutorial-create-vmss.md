---
title: "Criar conjuntos de dimensionamento de Máquina Virtual para o Windows no Azure | Microsoft Docs"
description: "Criar e implementar uma aplicação altamente disponível em VMs do Windows utilizando um conjunto de dimensionamento de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7fc2e841a193c219822e232fbc994df5e934ddc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Criar um conjunto de dimensionamento de Máquina Virtual e implementar uma aplicação altamente disponível no Windows
Um conjunto de dimensionamento de máquina virtual permite-lhe implementar e gerir um conjunto de máquinas virtuais idênticas de dimensionamento automático. Pode aumentar o número de VMs no conjunto de dimensionamento manualmente ou definir as regras para dimensionar automaticamente com base na utilização de recursos, tais como CPU, a pedido de memória ou tráfego de rede. Neste tutorial, implementa um conjunto no Azure de dimensionamento de máquina virtual. Saiba como:

> [!div class="checklist"]
> * Utilizar a extensão de Script personalizado para definir um site IIS a dimensionar
> * Criar um balanceador de carga para o conjunto de dimensionamento
> * Criar um conjunto de dimensionamento de máquina virtual
> * Aumentar ou reduzir o número de instâncias de um conjunto de dimensionamento
> * Criar regras de dimensionamento automático

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).


## <a name="scale-set-overview"></a>Descrição geral do conjunto de dimensionamento
Um conjunto de dimensionamento de máquina virtual permite-lhe implementar e gerir um conjunto de máquinas virtuais idênticas de dimensionamento automático. VMs num conjunto de dimensionamento estão distribuídas por domínios de falhas e de atualização de lógica num ou mais *grupos colocação*. Estes são os grupos de VMs configurados da mesma forma, semelhantes à [conjuntos de disponibilidade](tutorial-availability-sets.md).

As VMs são criadas conforme necessário de um conjunto de dimensionamento. Pode definir regras de dimensionamento automático para controlar como e quando VMs são adicionadas ou removidas do conjunto de dimensionamento. Estas regras podem acionar com base nas métricas, tais como a carga de CPU, utilização de memória ou tráfego de rede.

A conjuntos de dimensionamento suporte até 1000 VMs ao utilizar uma imagem de plataforma do Azure. Para cargas de trabalho com requisitos de personalização de VM ou instalação significativa, poderá pretender [criar uma imagem VM personalizada](tutorial-custom-images.md). Pode criar até 300 VMs em escala definida quando utilizar uma imagem personalizada.


## <a name="create-an-app-to-scale"></a>Criar uma aplicação a dimensionar
Antes de poder criar um conjunto de dimensionamento, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupAutomate* no *EastUS* localização:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupScaleSet -Location EastUS
```

Um tutorial anterior, aprendeu como [configuração de VM automatizar](tutorial-automate-vm-deployment.md) utilizando a extensão de Script personalizado. Criar uma configuração de conjunto de dimensionamento, em seguida, aplicar uma extensão de Script personalizado para instalar e configurar o IIS:

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location EastUS `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
```

## <a name="create-scale-load-balancer"></a>Criar Balanceador de carga de escala
Um balanceador de carga do Azure é um balanceador de carga de camada 4 (TCP, UDP) que fornece elevada disponibilidade, distribuição de tráfego de entrada entre VMs em bom estado. Uma sonda de estado de funcionamento do Balanceador de carga monitoriza uma porta especificada em cada VM e apenas distribui o tráfego para uma VM operacional. Para obter mais informações, consulte o próximo tutorial sobre [como carregar equilibrar as máquinas virtuais Windows](tutorial-load-balancer.md).

Crie um balanceador de carga que tenha um endereço IP público e distribui o tráfego da web na porta 80:

```powershell
# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupScaleSet `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Agora criar um conjunto com de dimensionamento de máquina virtual [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm). O exemplo seguinte cria um conjunto nomeado de dimensionamento *myScaleSet*:

```powershell
# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword P@ssword! `
  -ComputerNamePrefix myVM

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myScaleSet `
  -VirtualMachineScaleSet $vmssConfig
```

Demora alguns minutos para criar e configurar todos os recursos de conjunto de dimensionamento e VMs.


## <a name="test-your-app"></a>Testar a aplicação
Para ver o Web site do IIS em ação, obter o endereço IP público do seu Balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIP* criada como parte do conjunto de dimensionamento:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupScaleSet -Name myPublicIP | select IpAddress
```

Introduza o endereço IP público para um web browser. A aplicação é apresentada, incluindo o nome do anfitrião da VM que o Balanceador de carga distribuído tráfego para:

![Site do IIS em execução](./media/tutorial-create-vmss/running-iis-site.png)

Para ver o conjunto na ação de dimensionamento, pode forçar atualizar o web browser para ver o Balanceador de carga distribuir o tráfego entre todas as VMs com a sua aplicação.


## <a name="management-tasks"></a>Tarefas de gestão
Ao longo do ciclo de vida do conjunto de dimensionamento, poderá ter de executar um ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizem várias tarefas de ciclo de vida. O Azure PowerShell fornece uma forma rápida para realizar essas tarefas. Seguem-se algumas tarefas comuns.

### <a name="view-vms-in-a-scale-set"></a>Vista VMs num conjunto de dimensionamento
Para ver uma lista de VMs em execução no seu conjunto de dimensionamento, utilize [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) da seguinte forma:

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Loop through the instanaces in your scale set
for ($i=1; $i -le ($scaleset.Sku.Capacity - 1); $i++) {
    Get-AzureRmVmssVM -ResourceGroupName myResourceGroupScaleSet `
      -VMScaleSetName myScaleSet `
      -InstanceId $i
}
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentar ou diminuir instâncias de VM
Para ver o número de instâncias tem atualmente num conjunto de dimensionamento, utilize [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) e consultar no *sku.capacity*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -VMScaleSetName myScaleSet | `
    Select -ExpandProperty Sku
```

Pode, em seguida, manualmente aumentar ou reduzir o número de máquinas virtuais da escala com [atualização AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). O exemplo a seguir define o número de VMs no seu dimensionamento definido como *5*:

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -Name myScaleSet `
    -VirtualMachineScaleSet $scaleset
```

Se utiliza um alguns minutos a atualizar o número especificado de instâncias no seu dimensionamento definido.


### <a name="configure-autoscale-rules"></a>Configurar regras de dimensionamento automático
Em vez de conjunto de dimensionamento manualmente o número de instâncias no seu dimensionamento, pode definir regras de dimensionamento automático. Estas regras monitorizar instâncias do conjunto de dimensionamento e respondem em conformidade com base nas métricas e os limiares que define. O exemplo seguinte aumenta horizontalmente de forma o número de instâncias por um quando a carga média da CPU é superior a 60% durante um período de 5 minutos. Se a carga de CPU média, em seguida, descerem abaixo de 30% durante um período de 5 minutos, as instâncias são ampliadas por uma instância:

```powershell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5 minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5 minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```

Para obter mais informações de conceção sobre a utilização de dimensionamento automático, consulte [melhores práticas de dimensionamento automático](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, vai criar um conjunto de dimensionamento de máquina virtual. Aprendeu a:

> [!div class="checklist"]
> * Utilizar a extensão de Script personalizado para definir um site IIS a dimensionar
> * Criar um balanceador de carga para o conjunto de dimensionamento
> * Criar um conjunto de dimensionamento de máquina virtual
> * Aumentar ou reduzir o número de instâncias de um conjunto de dimensionamento
> * Criar regras de dimensionamento automático

Avançar para o próximo tutorial para saber mais sobre conceitos para máquinas virtuais de balanceamento de carga.

> [!div class="nextstepaction"]
> [Máquinas virtuais de balanceamento de carga](tutorial-load-balancer.md)
