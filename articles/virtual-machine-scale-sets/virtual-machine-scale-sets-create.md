---
title: "Criar um conjunto de dimensionamento da máquina virtual do Azure | Microsoft Docs"
description: "Criar e implementar um dimensionamento da máquina virtual Linux ou do Windows Azure com a CLI do Azure, o PowerShell, um modelo ou Visual Studio."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.openlocfilehash: 32af01aa545c541688128a7ae6bbb82a0e046f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>Criar e implementar um conjunto de dimensionamento de máquina virtual
Conjuntos de dimensionamento de máquina virtual tornam mais fácil para implementar e gerir máquinas virtuais idênticas, como um conjunto. Conjuntos de dimensionamento de fornecem uma camada de computação altamente personalizáveis e escalável para aplicações de hiperescala e suportam as imagens de plataforma do Windows, Linux plataforma imagens, imagens personalizadas e as extensões. Para mais informações sobre conjuntos de dimensionamento, consulte [conjuntos de dimensionamento de Máquina Virtual](virtual-machine-scale-sets-overview.md).

Este tutorial mostra como criar um conjunto de dimensionamento de máquina virtual **sem** no portal do Azure. Para obter informações sobre como utilizar o portal do Azure, consulte [definido como criar um dimensionamento da máquina virtual com o portal do Azure](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Para obter mais informações sobre os recursos do Azure Resource Manager, consulte [vs. de implementação clássica do Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Se estiver a utilizar o Azure CLI 2.0 ou o Azure PowerShell para criar um conjunto de dimensionamento, primeiro terá de iniciar sessão na sua subscrição.

Para obter mais informações sobre como instalar, configurar e iniciar sessão no Azure com o Azure CLI ou PowerShell, consulte [introdução ao Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) ou [introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Terá primeiro de criar um grupo de recursos que o conjunto de dimensionamento de máquina virtual está associado.

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

## <a name="create-from-azure-cli"></a>Criar a partir da CLI do Azure

Com a CLI do Azure, pode criar um conjunto sem qualquer esforço de dimensionamento de máquina virtual. Se omitir valores predefinidos, são fornecidos por si. Por exemplo, se não especificar quaisquer informações de rede virtual, uma rede virtual é criada por si. Se omitir as seguintes partes, estes são criados por si: 
- Um balanceador de carga
- Uma rede virtual
- Um endereço IP público

Quando escolher a imagem de máquina virtual que pretende utilizar no conjunto de dimensionamento de máquina virtual, tem algumas opções:

- URN  
O identificador de um recurso:  
**Win2012R2Datacenter**

- Alias URN  
O nome amigável de um URN:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- Id de recurso personalizado  
O caminho para um recurso do Azure:  
**/subscriptions/Subscription-GUID/resourceGroups/MyResourceGroup/Providers/Microsoft.Compute/Images/MyImage**

- Recurso Web  
O caminho para um URI de HTTP:  
**http://contoso.blob.Core.Windows.NET/VHDs/osdiskimage.vhd**

>[!TIP]
>Pode obter uma lista de imagens disponíveis com `az vm image list`.

Para criar um conjunto de dimensionamento de máquina virtual, tem de especificar o seguinte:

- Grupo de recursos 
- Nome
- Imagem do sistema operativo
- Informações de autenticação 
 
O exemplo seguinte cria um conjunto de dimensionamento de máquinas virtuais básica (este passo pode demorar alguns minutos).

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

Depois do comando terminar tem agora o conjunto criado de dimensionamento de máquina virtual. Terá de obter o endereço IP da máquina virtual para que possam ligar à mesma. Pode obter muitas informações diferentes sobre a máquina virtual (incluindo o endereço IP) com o seguinte comando. 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>Criar a partir do PowerShell

PowerShell é mais complicado utilizar a CLI do Azure. Enquanto a CLI do Azure fornece predefinições para recursos relacionados com o funcionamento em rede (por exemplo, balanceadores de carga, endereços IP e redes virtuais), PowerShell não o faz. Referenciar uma imagem com o PowerShell é ligeiramente mais complicada demasiado. Pode obter as imagens com os seguintes cmdlets:

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

O trabalho de cmdlets pode ser direcionado na sequência. Eis um exemplo de como obter todas as imagens para a **EUA oeste 2** região com um fabricante com o nome **microsoft** no mesmo.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

O fluxo de trabalho para criar um conjunto de dimensionamento de máquina virtual é o seguinte:

1. Crie um objeto de configuração que contém informações sobre o conjunto de dimensionamento.
2. Referência da imagem do SO base.
3. Configurar as definições do sistema operativo: autenticação, o prefixo de nome de VM e o utilizador/passagem.
4. Configure o funcionamento em rede.
5. Crie conjunto de dimensionamento.

Este exemplo cria uma escala de instância de dois básica definido para um computador que tenha instalado o Windows Server 2016.

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

### <a name="using-a-custom-virtual-machine-image"></a>Utilizar uma imagem de máquina virtual personalizada
Se estiver a criar uma escala definir a partir da sua imagem personalizada, em vez de referenciar uma imagem de máquina virtual a partir da galeria do _conjunto AzureRmVmssStorageProfile_ comando deverá ter o seguinte aspeto:
```PowerShell
Set-AzureRmVmssStorageProfile -OsDiskCreateOption FromImage -ManagedDisk PremiumLRS -OsDiskCaching "None" -OsDiskOsType Linux -ImageReferenceId (Get-AzureRmImage -ImageName $VMImage -ResourceGroupName $rg).id
```

## <a name="create-from-a-template"></a>Criar a partir de um modelo

Pode implementar um conjunto, utilizando um modelo Azure Resource Manager de dimensionamento de máquina virtual. Pode criar o seu próprio modelo ou utilizar um do [repositório de modelo](https://azure.microsoft.com/resources/templates/?term=vmss). Estes modelos podem ser implementados diretamente à sua subscrição do Azure.

>[!NOTE]
>Para criar o seu próprio modelo, crie um ficheiro de texto JSON. Para obter informações gerais sobre como criar e personalizar um modelo, consulte [modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Um modelo de exemplo está disponível [no GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set). Para obter mais informações sobre como criar e utilizar esse exemplo, consulte [conjunto de dimensionamento viável mínimo](.\virtual-machine-scale-sets-mvss-start.md).

## <a name="create-from-visual-studio"></a>Criar a partir do Visual Studio

Com o Visual Studio, pode criar um projeto do grupo de recursos do Azure e adicionar que um máquina virtual conjunto de dimensionamento de modelo ao mesmo. Pode escolher se pretende importá-lo a partir do GitHub ou à Galeria de aplicações Web do Azure. Também é gerada uma implementação de script do PowerShell para si. Para obter mais informações, consulte [definido como criar um dimensionamento da máquina virtual com o Visual Studio](virtual-machine-scale-sets-vs-create.md).

## <a name="create-from-the-azure-portal"></a>Criar a partir do portal do Azure

O portal do Azure fornece uma maneira conveniente para criar rapidamente um conjunto de dimensionamento. Para obter mais informações, consulte [definido como criar um dimensionamento da máquina virtual com o portal do Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [discos de dados](virtual-machine-scale-sets-attached-disks.md).

Saiba como [gerir as suas aplicações](virtual-machine-scale-sets-deploy-app.md).
