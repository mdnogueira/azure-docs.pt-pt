---
title: "Criar uma rede virtual com uma ligação de Rede de VPNs com o Azure Resource Manager e o PowerShell | Microsoft Docs"
description: "Este artigo explica como criar uma VNet com o modelo de implementação Resource Manager e como ligá-la à sua rede local no local através de uma ligação de gateway de VPN S2S."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: e2c1a99a23f8d2d8ceb1fd8a9fe3c0e86abc0b02


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Criar uma VNet com uma ligação de Rede de VPNs com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássica – Portal Clássico](vpn-gateway-site-to-site-create.md)
>
>

Este artigo explica como criar uma rede virtual e uma ligação de gateway de Rede de VPNs para a sua rede no local com o modelo de implementação Azure Resource Manager. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas.

![Diagrama Site a Site](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site-to-site")

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos e métodos de implementação atualmente disponíveis para configurações Site a Site. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se pretender ligar VNets entre si, mas não estiver a criar uma ligação para uma localização no local, veja [Configurar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se pretender adicionar uma ligação Site a Site a uma VNet que já tem uma ligação, veja [Adicionar uma ligação S2S a uma VNet com uma ligação de gateway de VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
* A versão mais recente dos cmdlets do Azure Resource Manager PowerShell. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações sobre como instalar os cmdlets PowerShell.

## <a name="a-namelogina1-connect-to-your-subscription"></a><a name="Login"></a>1. Estabelecer a ligação à subscrição
Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

    Login-AzureRmAccount

Verifique as subscrições da conta.

    Get-AzureRmSubscription

Especifique a subscrição que pretende utilizar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="a-namevneta2-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>2. Criar uma rede virtual e uma sub-rede de gateway
Os exemplos utilizam uma sub-rede de gateway /28. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando pelo menos /28 ou /27. Desta forma, se quiser ter mais configurações no futuro, haverá endereços suficientes para as acomodar.

Se já tiver uma rede virtual com uma sub-rede de gateway /29 ou superior, pode ir diretamente para [Adicionar o gateway da rede local](#localnet).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para criar uma rede virtual e uma sub-rede de gateway
Utilize o exemplo seguinte para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus.

Comece por criar um grupo de recursos:

    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, cria a sua rede virtual. Verifique se os espaços de endereços que especifica não se sobrepõem a qualquer um dos espaços de endereços que tem na rede no local.

O exemplo seguinte cria uma rede virtual denominada *testvnet* e duas sub-redes: *GatewaySubnet* e *Subnet1*. É importante criar uma sub-rede designada especificamente *GatewaySubnet*. Se lhe der outro nome, a configuração da ligação falhará.

Defina as variáveis.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Crie a VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="a-namegatewaysubnetato-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma rede virtual já criada
Este passo é necessário apenas se tiver de adicionar uma sub-rede de gateway a uma VNet criada anteriormente.

Pode criar a sub-rede de gateway utilizando o exemplo seguinte. O nome da sub-rede do gateway deverá ser "GatewaySubnet". Se lhe der outro nome, está a criar uma sub-rede, mas o Azure não a tratará como uma sub-rede de gateway.

Defina as variáveis.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Crie a sub-rede de gateway.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Defina a configuração.

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## <a name="3-a-namelocalnetaadd-your-local-network-gateway"></a>3. <a name="localnet"></a>Adicionar o gateway de rede local
Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Deve dar ao site um nome que o Azure possa utilizar para se referir a ele e deverá especificar também o prefixo do espaço de endereços do gateway da rede local.

O Azure utiliza o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Ou seja, tem de especificar cada prefixo de endereço que pretende que seja associado ao gateway da sua rede local. Pode atualizar facilmente estes prefixos caso a sua rede no local seja alterada.

Ao utilizar os exemplos do PowerShell, tenha em atenção o seguinte:

* O *GatewayIPAddress* é o endereço IP do seu dispositivo VPN no local. O dispositivo VPN não pode estar localizado atrás de um NAT.
* O *AddressPrefix* é o seu espaço de endereços no local.

Para adicionar um gateway de rede local com um prefixo de endereço único:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um gateway de rede local com vários prefixos de endereço:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Para modificar prefixos de endereços IP do gateway da sua rede local
Às vezes, os prefixos de gateway da sua rede local mudam. Os passos a efetuar para modificar os prefixos de endereços IP dependem de se criou ou não uma ligação de gateway de VPN. Veja a secção [Modificar os prefixos de endereços IP para um gateway de rede local](#modify) deste artigo.

## <a name="a-namepublicipa4-request-a-public-ip-address-for-the-vpn-gateway"></a><a name="PublicIP"></a>4. Pedir um endereço IP público para o gateway de VPN
Em seguida, peça um endereço IP público para atribuir ao gateway de VPN da VNet do Azure. Não é o mesmo endereço IP que foi atribuído ao seu dispositivo VPN; é, isso sim, atribuído ao próprio gateway de VPN do Azure. Não é possível especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway. Utilize este endereço IP ao configurar o dispositivo VPN no local para ligar ao gateway.

Atualmente, o gateway de VPN do Azure do modelo de implementação do Resource Manager suporta apenas endereços IP públicos com o método Alocação Dinâmica. No entanto, não significa que o endereço IP será alterado. O endereço IP do gateway de VPN do Azure só é alterado quando o gateway é eliminado e recriado. O endereço IP público do gateway não será alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN do Azure.

Utilize o seguinte exemplo do PowerShell:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="a-namegatewayipconfiga5-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>5. Criar a configuração de endereçamento IP do gateway
A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do gateway.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id

## <a name="a-namecreategatewaya6-create-the-virtual-network-gateway"></a><a name="CreateGateway"></a>6. Criar o gateway de rede virtual
Neste passo, cria o gateway de rede virtual. A criação de um gateway pode demorar bastante tempo. 45 minutos ou mais.

Utilize os seguintes valores:

* O *-GatewayType* para uma configuração Site a Site é *Vpn*. O tipo de gateway é sempre específico da configuração que estiver a implementar. Por exemplo, outras configurações de gateway poderão exigir o ExpressRoute -GatewayType.
* O *-VpnType* pode ser *RouteBased* (conhecido como Gateway Dinâmico em alguma documentação) ou *PolicyBased* (referido como Gateway Estático em alguma documentação). Para obter mais informações sobre os tipos de gateways de VPN, veja [Acerca dos Gateways de VPN](vpn-gateway-about-vpngateways.md).
* O *-GatewaySku* pode ser *Básico*, *Standard* ou *HighPerformance*.     

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="a-nameconfigurevpndevicea7-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>7. Configurar o dispositivo VPN
Nesta fase, precisa do endereço IP público do gateway de rede virtual para configurar o dispositivo VPN no local. Contacte com o fabricante do dispositivo para obter as informações de configuração específicas. Para obter mais informações, veja [Dispositivos VPN](vpn-gateway-about-vpn-devices.md).

Para localizar o endereço IP público do gateway de rede virtual, utilize o seguinte exemplo:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="a-namecreateconnectiona8-create-the-vpn-connection"></a><a name="CreateConnection"></a>8. Criar a ligação VPN
Em seguida, crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN. Não se esqueça de substituir os valores pelos seus. A chave partilhada tem de corresponder ao valor utilizado na configuração do dispositivo VPN. Tenha em atenção que o `-ConnectionType` para Site a Site é *IPsec*.

Defina as variáveis.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Crie a ligação.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após uns breves instantes, a ligação será estabelecida.

## <a name="a-nametoverifyato-verify-a-vpn-connection"></a><a name="toverify"></a>Para verificar uma ligação VPN
A verificação da ligação VPN pode ser feita de várias formas.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="a-namemodifyato-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Para modificar os prefixos de endereços IP de um gateway de rede local
Se precisar de alterar os prefixos do gateway de rede local, utilize as instruções seguintes. São fornecidos dois conjuntos de instruções. As instruções a escolher dependem se já criou ou não a ligação de gateway.

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="a-namemodifygwipaddressato-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Para modificar o endereço IP do gateway de um gateway de rede local
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes
*  Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).



<!--HONumber=Dec16_HO1-->


