<properties 
   pageTitle="Acerca do VPN Gateway do Azure | Microsoft Azure"
   description="Saiba mais sobre o VPN Gateway da Rede Virtual do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2016"
   ms.author="cherylmc" />

# Acerca do VPN Gateway

O Gateway de VPN é uma coleção de definições que é utilizada para enviar tráfego de rede entre redes virtuais e localizações no local. As secções neste artigo discutem as definições relacionadas com o Gateway de VPN. O Gateway de VPN é utilizado para ligações Site a Site, Ponto a Site e ExpressRoute. O Gateway de VPN também é utilizado para enviar tráfego entre várias redes virtuais no Azure (VNet a VNet). 

O Gateway de VPN pode ser adicionado a uma rede virtual para criar uma ligação. Cada rede virtual pode ter apenas um Gateway de VPN e existem passos de configuração específicos para cada ligação. Para diagramas de ligação, veja [Topologias da ligação do Gateway de VPN](vpn-gateway-topology.md). 

## <a name="gwsku"></a>SKUs de gateway

Quando cria um gateway de VPN, tem de especificar o SKU de gateway que pretende utilizar. Os SKUs de gateway aplicam-se aos tipos de gateway ExpressRoute e de VPN. Há diferença de preços entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços dos Gateways de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Para obter mais informações sobre ExpressRoute, veja a [Descrição Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

Há três SKUs do VPN Gateway:

- Básico
- Standard
- HighPerformance

O exemplo abaixo especifica o `-GatewaySku` como *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>Débito agregado estimado por tipo de SKU e de gateway


A tabela abaixo mostra os tipos de gateway e o débito agregado estimado. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="gwtype"></a>Tipos de gateway

O tipo de gateway especifica o modo como o gateway em si se liga e é uma configuração obrigatória no modelo de implementação Resource Manager. Não confunda tipo de gateway com tipo de VPN, que especifica o tipo de encaminhamento para a VPN. Os valores disponíveis para `-GatewayType` são: 

- Vpn
- ExpressRoute


Este exemplo do modelo de implementação Resource Manager especifica o GatewayType como *Vpn*. Quando estiver a criar um gateway, tem verificar se o tipo de gateway está correto para a sua configuração. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Tipos de ligação

Cada configuração exige um tipo específico de ligação. Os valores disponíveis do PowerShell do Resource Manager para `-ConnectionType` são:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

No exemplo abaixo, estamos a criar uma ligação Site a Site, que exige que o tipo de ligação seja “IPsec”.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="vpntype"></a>Tipos de VPN

Cada configuração exige um tipo específico de VPN para que possa funcionar. Se está a combinar duas configurações, por exemplo, a criar uma ligação Site a Site e uma ligação Ponto a Site para a mesma VNet, tem de utilizar um tipo de VPN que cumpra ambos os requisitos de ligação. 

No caso de ligações coexistentes de Site a Site e Ponto a Site, tem de utilizar um tipo de VPN baseado na rota ao trabalhar com o modelo de implementação Azure Resource Manager ou um gateway dinâmico se estiver a trabalhar com o modo de implementação clássica.

Ao criar a configuração, deverá selecionar o tipo de VPN que é exigido para a sua ligação. 

Há dois tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Este exemplo do modelo de implementação Resource Manager especifica `-VpnType` como * RouteBased*. Quando estiver a criar um gateway, tem de confirmar se o -VpnType está correto para a sua configuração. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos do gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Sub-rede de gateway

Para configurar um gateway de VPN, terá de começar por criar uma sub-rede de gateway para a sua VNet. Para funcionar corretamente, a sub-rede do gateway tem de ter o nome *GatewaySubnet*. Este nome permite ao Azure saber que esta sub-rede deve ser utilizada para o gateway.<BR>Se estiver a utilizar o portal clássico, a sub-rede do gateway recebe automaticamente o nome *Gateway* na interface do portal. Esta ação é específica para ver apenas a sub-rede do gateway no portal clássico. Neste caso, a sub-rede é realmente criada no Azure como *GatewaySubnet* e pode ser visualizada deste modo no Portal do Azure e no PowerShell.

O tamanho mínimo da sub-rede do gateway depende totalmente da configuração que pretende criar. Apesar de poder criar uma sub-rede do gateway tão pequena como /29 para algumas configurações, recomendamos que crie uma sub-rede do gateway de /28 ou superior (/ 28, /27, /26, etc.). 

A criação de um gateway de tamanho superior impede os problemas devido às limitações de tamanho do gateway. Por exemplo, se tiver criado um gateway com um tamanho de sub-rede de gateway /29 e pretender definir uma configuração de coexistência Site a Site/ExpressRoute, terá de eliminar o gateway, eliminar a sub-rede do gateway, criar a sub-rede do gateway como /28 ou superior e, em seguida, recriar o gateway. 

Ao criar logo de início uma sub-rede de gateway com tamanho superior, pode poupar tempo posteriormente quando adicionar novas funcionalidades de configuração ao seu ambiente de rede. 

O exemplo abaixo mostra uma sub-rede de gateway com o nome GatewaySubnet. Pode ver que a notação CIDR especifica /27, o que permite endereços IP suficientes para a maioria das configurações que existem neste momento.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Verifique se não há um Grupo de Segurança de Rede (NSG) aplicado à GatewaySubnet, uma vez que essa opção pode levar à falha das ligações.



## <a name="lng"></a>Gateways de rede local

O gateway de rede local refere-se normalmente à sua localização no local. No modelo de implementação clássica, o gateway de rede local foi referido como Site Local. Terá de atribuir um nome ao gateway de rede local, o endereço IP público do dispositivo VPN no local e, em seguida, especificar os prefixos de endereços que se encontram na localização no local. O Azure examinará os prefixos de endereço de destino do tráfego de rede, consultará a configuração que especificou para o gateway de rede local e encaminhará os pacotes em conformidade. Pode modificar estas prefixos de endereço, conforme necessário.


### Modificar os prefixos de endereços – Resource Manager

Para modificar os prefixos de endereço, o procedimento difere consoante tiver ou não criado já o gateway de VPN. Veja a secção do artigo [Modificar os prefixos de endereço para um gateway de rede local](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

No exemplo abaixo, pode ver que está a ser especificado um gateway de rede local com o nome MyOnPremiseWest, que irá conter dois prefixos de endereço IP.

    New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24') 

### Modificar os prefixos de endereços – implementação clássica

Se precisar de modificar os sites locais quando estiver a utilizar o modelo de implementação clássica, pode utilizar a página de configuração Redes Locais no portal clássico ou modificar diretamente o ficheiro Configuração de Rede, NETCFG.XML.



## Passos seguintes

Antes de prosseguir com o planeamento e a estruturação da sua configuração, veja o artigo [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações.





 



<!--HONumber=Aug16_HO1-->


