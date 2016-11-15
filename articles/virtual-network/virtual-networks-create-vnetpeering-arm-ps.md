---
title: Criar VNet Peering com cmdlets do Powershell | Microsoft Docs
description: Saiba como criar uma rede virtual com o portal do Azure no Resource Manager.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai; annahar
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 348b23b277c80867f600a408736e13b8ceb665f4


---
# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Criar VNet Peering com cmdlets do Powershell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet peering com o PowerShell, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.

> [!NOTE]
> O cmdlet PowerShell para gerir o VNet Peering é fornecido com o [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
> 
> 

1. Ler objetos de rede virtual:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
2. Para estabelecer VNet peering, terá de criar duas ligações, uma para cada direção. O passo seguinte irá criar primeiro uma ligação VNet peering da VNet1 para a VNet2:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
   
    O resultado indica:
   
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
3. Este passo irá criar uma ligação VNet peering da VNet2 para a VNet1:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
   
    O resultado indica:
   
        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
4. Quando a ligação VNet peering for criada, pode ver o estado da ligação abaixo:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
   
    O resultado indica:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Existem algumas propriedades configuráveis para o VNet peering:
   
   | Opção | Descrição | Predefinição |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Se o espaço de endereços da VNet em modo de peering vai ser incluído como parte da Etiqueta Virtual_network |Sim |
   | AllowForwardedTraffic |Se o tráfego que não provém de uma VNet em modo de peering seja aceite ou removido |Não |
   | AllowGatewayTransit |Permite que a VNet em modo de peering utilize o seu gateway de VNet |Não |
   | UseRemoteGateways |Utilize o gateway da VNet em modo de peering. A VNet em modo de peering tem de ter um gateway configurado e AllowGatewayTransit selecionado. Não pode utilizar esta opção se tiver um gateway configurado |Não |
   
    Cada ligação no VNet peering tem o conjunto das propriedades acima indicadas. Por exemplo, pode definir AllowVirtualNetworkAccess para Verdadeiro para a ligação VNet peering da VNet1 para a VNet2 e defini-lo como Falso para a ligação VNet peering na outra direção.
   
        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
   
    Pode executar Get-AzureRmVirtualNetworkPeering para verificar de novo o valor da propriedade após a alteração. No resultado, pode ver que AllowForwardedTraffic está definido como True após executar os cmdlets acima.
   
        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
   
    Uma vez estabelecido o peering neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer outra máquina virtual de ambas as VNets. Por predefinição, AllowVirtualNetworkAccess é Verdadeiro e VNet peering irá aprovisionar as ACLs adequadas para permitir a comunicação entre VNets. Continua a poder aplicar as regras do grupo de segurança de rede (NSG) para bloquear a conectividade entre sub-redes ou máquinas virtuais específicas, para obter um controlo mais refinado do acesso entre duas redes virtuais.  Para obter mais informações sobre a criação de regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar VNet peering entre subscrições através do PowerShell, siga os passos abaixo:

1. Inicie sessão no Azure com a conta do User-A privilegiada para a Subscription-A e execute o seguinte cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
   
    Isto não é um requisito. O peering pode ser estabelecido mesmo se os utilizadores emitirem individualmente pedidos de peering para as respetivas VNets, desde que os pedidos correspondam. A adição de um utilizador com privilégios da outra VNet como utilizador na VNet local facilita a configuração.
2. Inicie sessão no Azure com a conta do User-B privilegiada para a Subscription-B e execute o seguinte cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
3. No início de sessão do User-A, execute o cmdlet abaixo:
   
        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
4. No início de sessão do User-B, execute o cmdlet abaixo:
   
        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
5. Uma vez estabelecido o peering, qualquer máquina virtual na VNet3 deverá conseguir comunicar com qualquer máquina virtual na VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, pode executar os cmdlets do PowerShell abaixo para estabelecer o VNet peering.  Tem de definir a propriedade AllowForwardedTraffic como True e associar a VNET1 à HubVNet, que permite o tráfego de entrada de fora do espaço de endereços do VNet peering.
   
        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
2. Uma vez estabelecido o peering, pode consultar este [artigo](virtual-network-create-udr-arm-ps.md) e definir a rota definida pelo utilizador (UDR) para redirecionar o tráfego da VNet1 através de uma aplicação virtual, de modo a utilizar as respetivas capacidades. Quando especificar o endereço do próximo salto na rota, pode configurá-lo para o endereço IP da aplicação virtual na VNet HubVNet em modo de peering. Abaixo está um exemplo:
   
        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5
   
        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar um VNet Peering entre uma rede virtual clássica e uma rede virtual do Azure Resource Manager no PowerShell, siga os passos abaixo:

1. Leia o objeto de rede virtual para **VNET1**, a rede virtual do Azure Resource Manager é a seguinte:
   
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
2. Só precisa de uma ligação para estabelecer um VNet Peering neste cenário, especificamente uma ligação da **VNET1** para a **VNET2**. Para realizar este passo, precisa conhecer o ID do recurso da VNet clássica. O formato do ID do grupo de recursos tem o aspeto:
   
        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}
   
    Não se esqueça de substituir SubscriptionID, ResourceGroupName e VirtualNetworkName pelos nomes adequados.
   
    Para isso, utilize o seguinte:
   
        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
3. Quando a ligação VNet Peering for criada, pode ver o estado da ligação, conforme mostrado no resultado abaixo:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Remover VNet Peering
1. Para remover o VNet peering, tem de executar o seguinte cmdlet:
   
     Remove-AzureRmVirtualNetworkPeering  
   
     Remover ambas as ligações, com os comandos seguintes:
   
     Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2   Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
2. Depois de remover uma ligação num VNET peering, o estado da ligação de peering passa a desligado. Neste estado, não é possível recriar a ligação até que o estado da ligação de peering mude para Iniciado. Recomendamos que remova ambas as ligações antes de recriar o VNet peering.




<!--HONumber=Nov16_HO2-->


