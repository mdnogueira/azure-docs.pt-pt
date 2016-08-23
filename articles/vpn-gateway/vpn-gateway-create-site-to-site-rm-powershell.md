<properties
   pageTitle="Criar uma rede virtual com uma ligação de Rede de VPNs com o Azure Resource Manager e o PowerShell | Microsoft Azure"
   description="Este artigo explica como criar uma VNet com o modelo Resource Manager e como ligá-la à sua rede local no local através de uma ligação de gateway de VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2016"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma ligação de Rede de VPNs com o PowerShell e o Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portal Clássico do Azure](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artigo explica como criar uma rede virtual e uma ligação de Rede de VPNs para a sua rede no local com o modelo de implementação Azure Resource Manager. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas. 


**Acerca dos modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Diagrama de ligação 

![Diagrama Site a Site](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "site-to-site")

**Modelos de implementação e ferramentas para ligações Site a Site**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Se pretender ligar VNets entre si, mas não estiver a criar uma ligação para uma localização no local, veja [Configurar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se estiver à procura de um tipo de configuração de ligação diferente, veja o artigo [Topologias da ligação do VPN Gateway](vpn-gateway-topology.md).


## Antes de começar

Antes de iniciar a configuração, verifique se tem os seguintes itens.

- Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, terá de se coordenar com alguém que consiga fornecer esses detalhes.

- Um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
    
- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Terá de instalar a versão mais recente dos cmdlets PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets PowerShell.


## 1. Estabelecer a ligação à subscrição 

Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

    Login-AzureRmAccount

Verifique as subscrições da conta.

    Get-AzureRmSubscription 

Especifique a subscrição que pretende utilizar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2. Criar uma rede virtual e uma sub-rede de gateway

Os nossos exemplos abaixo mostram uma sub-rede de gateway de /28. Embora seja possível criar uma sub-rede de gateway tão como pequena como /29, não recomendamos que o faça. É recomendada a criação de uma sub-rede de gateway /27 ou superior (/26, /25, etc.) para acomodar os requisitos de funcionalidades adicionais. 

Se já tiver uma rede virtual com uma sub-rede de gateway /29 ou superior, pode ir diretamente para [Adicionar o gateway da rede local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### Para criar uma rede virtual e uma sub-rede de gateway

Utilize o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway Substitua os valores pelos seus. 

Comece por criar um grupo de recursos:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, cria a sua rede virtual. Verifique se os espaços de endereços que especifica não se sobrepõem a qualquer um dos espaços de endereços que tem na rede no local.

O exemplo abaixo cria uma rede virtual denominada *testvnet* e duas sub-redes: *GatewaySubnet* e *Subnet1*. É importante criar uma sub-rede designada especificamente *GatewaySubnet*. Se lhe der outro nome, a configuração da ligação falhará. 

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma rede virtual já criada

Este passo é necessário apenas se tiver de adicionar uma sub-rede de gateway a uma VNet criada anteriormente.

Pode criar a sub-rede de gateway utilizando o exemplo abaixo. O nome da sub-rede do gateway deverá ser “GatewaySubnet”. Se lhe der outro nome, estará a criar uma sub-rede, mas o Azure não a tratará como uma sub-rede de gateway.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Agora, defina a configuração. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"></a>Adicionar o gateway de rede local

Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Deverá dar a esse site um nome que o Azure utilizará para se referir a ele e deverá especificar também o prefixo do espaço de endereços do gateway da rede local. 

O Azure utilizará o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Ou seja, terá de especificar cada prefixo de endereço que pretende que seja associado ao gateway da sua rede local. Pode atualizar facilmente estes prefixos caso a sua rede no local for alterada. 

Ao utilizar os exemplos do PowerShell, tenha em atenção o seguinte:
    
- O *GatewayIPAddress* é o endereço IP do seu dispositivo VPN no local. O dispositivo VPN não pode estar localizado atrás de um NAT. 
- O *AddressPrefix* é o seu espaço de endereços no local.

Para adicionar um gateway de rede local com um prefixo de endereço único:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um gateway de rede local com vários prefixos de endereço:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Para modificar prefixos de endereços IP do gateway da sua rede local

Às vezes, os prefixos de gateway da sua rede local mudam. Os passos a efetuar para modificar os prefixos de endereços IP dependem de ter ou não criado uma ligação de gateway de VPN. Veja a secção [Modificar os prefixos de endereços IP para um gateway de rede local](#modify) deste artigo.


## 4. Pedir um endereço IP público para o gateway de VPN

Em seguida, deverá pedir um endereço IP público para atribuir ao gateway de VPN da VNet do Azure. Não é o mesmo endereço IP que foi atribuído ao seu dispositivo VPN; é, isso sim, atribuído ao próprio gateway de VPN do Azure. Não pode especificar o endereço IP que pretende utilizar; é dinamicamente atribuído ao gateway. Utilizará este endereço IP ao configurar o dispositivo VPN no local para ligar ao gateway.

Utilize o exemplo do PowerShell abaixo. O Método de Alocação deste endereço tem de ser Dinâmico. 

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] Atualmente, o gateway de VPN do Azure do modelo de implementação do Resource Manager suporta apenas endereços IP públicos com o método Alocação Dinâmica. No entanto, não significa que o endereço IP será alterado. O endereço IP do gateway de VPN do Azure só é alterado quando o gateway é eliminado e recriado. O endereço IP público do gateway não será alterado ao redimensionar, ao repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN do Azure.

## 5. Criar a configuração de endereçamento IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo abaixo para criar a configuração do gateway. 

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6. Criar o gateway de rede virtual

Neste passo, irá criar o gateway de rede virtual. Tenha em atenção que a criação de um gateway pode demorar bastante tempo: 20 minutos ou mais. 

Utilize os seguintes valores:

- O *-GatewayType* para uma configuração Site a Site é *Vpn*. O tipo de gateway é sempre específico da configuração que estiver a implementar. Por exemplo, outras configurações de gateway poderão exigir o ExpressRoute -GatewayType. 

- O *-VpnType* pode ser *RouteBased* (conhecido como Gateway Dinâmico em alguma documentação) ou *PolicyBased* (referido como Gateway Estático em alguma documentação). Para obter mais informações sobre os tipos de gateway de VPN, veja [Acerca dos Gateways de VPN](vpn-gateway-about-vpngateways.md#vpntype).
- O *-GatewaySku* pode ser *Básico*, *Standard* ou *HighPerformance*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7. Configurar o dispositivo VPN

Nesta fase, precisará do endereço IP público do gateway de rede virtual para configurar o dispositivo VPN no local. Contacte com o fabricante do dispositivo para obter as informações de configuração específicas. Veja também [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações.

Para localizar o endereço IP público do gateway de rede virtual, utilize o seguinte exemplo:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8. Criar a ligação VPN

Em seguida, vai criar a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN. Não se esqueça de substituir os valores pelos seus. A chave partilhada tem de corresponder ao valor utilizado na configuração do dispositivo VPN. Tenha em atenção que o `-ConnectionType` para Site a Site é *IPsec*. 

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após uns breves instantes, a ligação será estabelecida. 

## 9. Verificar uma ligação VPN

A verificação da ligação VPN pode ser feita de várias formas. Abaixo, iremos falar sobre como efetuar a verificação básica com o Portal do Azure e com o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Para modificar os prefixos de endereços IP de um gateway de rede local

Se precisar de alterar os prefixos do gateway de rede local, utilize as instruções abaixo.  São fornecidos dois conjuntos de instruções. As instruções a escolher dependem se já criou ou não a ligação de gateway VPN. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Passos seguintes

- Pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.

- Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Aug16_HO1-->


