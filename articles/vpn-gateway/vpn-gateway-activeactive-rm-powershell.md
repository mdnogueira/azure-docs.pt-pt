---
title: "Configurar as ligações de S2S VPN de ativo-ativo para Gateways de VPN: o Gestor de recursos do Azure: PowerShell | Microsoft Docs"
description: "Este artigo explica como configurar ligações de ativo-ativo com Gateways de VPN do Azure com o Azure Resource Manager e o PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2017
ms.author: yushwang
ms.openlocfilehash: a9f71b566ffdb163f95634835f64589a700d712f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Configurar ligações de S2S VPN de ativo-ativo com Gateways de VPN do Azure

Este artigo explica os passos para criar ativo-ativo em vários locais e ligações VNet a VNet com o modelo de implementação do Resource Manager e o PowerShell.

## <a name="about-highly-available-cross-premises-connections"></a>Acerca das ligações em vários locais elevada
Para alcançar a elevada disponibilidade para conectividade de VNet a VNet e em vários locais, deve implementar vários gateways VPN e estabelecer várias ligações paralelas entre as suas redes e do Azure. Consulte [altamente disponível em vários locais e VNet a VNet conectividade](vpn-gateway-highlyavailable.md) para uma descrição geral da topologia e as opções de conectividade.

Este artigo fornece instruções para configurar uma ligação de VPN de ativo-ativo em vários locais e a ligação de ativo-ativo entre duas redes virtuais:

* [Parte 1 – criar e configurar o gateway de VPN do Azure no modo ativo-ativo](#aagateway)
* [Parte 2 - estabelecer ligações de ativo-ativo em vários locais](#aacrossprem)
* [Parte 3 - estabelecer ligações VNet a VNet de ativo-ativo](#aav2v)
* [Parte 4 - atualizar gateway existente entre ativo-ativo e modo de espera ativo](#aaupdate)

Pode combinar estas em conjunto para criar uma topologia de rede mais complexa, altamente disponíveis que satisfaça as suas necessidades.

> [!IMPORTANT]
> Tenha em atenção que o modo ativo-ativo utiliza apenas os SKUs seguintes: 
  * VpnGw1, VpnGw2, VpnGw3
  * HighPerformance (para SKUs legados antigos)
> 
> 

## <a name ="aagateway"></a>Parte 1 – criar e configurar os gateways de VPN de ativo-ativo
Os passos seguintes irão configurar o gateway de VPN do Azure nos modos de ativo-ativo. As principais diferenças entre os gateways ativo-ativo e modo de espera de Active Directory:

* Tem de criar duas configurações de IP do Gateway com dois endereços IP públicos
* Precisa de definir o sinalizador de EnableActiveActiveFeature
* O SKU de gateway tem de ser VpnGw1, VpnGw2, VpnGw3 ou HighPerformance (legado SKU).

As outras propriedades são os mesmos que os gateways de não-ativo-ativo. 

### <a name="before-you-begin"></a>Antes de começar
* Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Terá de instalar os cmdlets PowerShell do Azure Resource Manager. Consulte [descrição geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Passo 1 – criar e configurar VNet1
#### <a name="1-declare-your-variables"></a>1. Declarar as variáveis
Para este exercício, vamos começar por declarar as nossas variáveis. O exemplo abaixo declara as variáveis com os valores deste exercício. Verifique se substitui os valores pelos seus quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modifique as variáveis e, em seguida, copie e cole a consola do PowerShell.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Ligar à sua subscrição e crie um novo grupo de recursos
Confirme que muda para o modo do PowerShell para utilizar os cmdlets do Resource Manager. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Criar a TestVNet1
O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, Front-end e Back-end. Quando estiver a substituir os valores, é importante que dê sempre à sub-rede do gateway o nome específico GatewaySubnet. Se der outro nome, a criação da gateway falhará.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Passo 2 - criar o VPN gateway da TestVNet1 com modo ativo-ativo
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Criar o gateway e endereços IP públicos configurações de IP
O pedido dois endereços IP públicos para ser atribuído ao gateway que criará para a sua VNet. Também irá definir a sub-rede e configurações de IP necessárias.

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Criar o gateway VPN com a configuração de ativo-ativo
Crie o gateway de rede virtual para TestVNet1. Tenha em atenção que existem duas entradas GatewayIpConfig e o sinalizador de EnableActiveActiveFeature está definido. A criação de um gateway pode demorar algum tempo (45 minutos ou mais).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Obter os endereços IP públicos do gateway e o endereço IP do elemento BGP
Assim que for criado o gateway, terá de obter o endereço IP do elemento BGP no Gateway de VPN do Azure. Este endereço é necessária para configurar o Gateway de VPN do Azure como um elemento de rede BGP para os seus dispositivos VPN no local.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Utilize os seguintes cmdlets para mostrar dois endereços IP públicos alocados para o gateway de VPN e os respetivos endereços de IP do elemento BGP correspondentes para cada instância de gateway:

```powershell

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }
```

A ordem do IP público endereços para as instâncias de gateway e os endereços de Peering de BGP correspondentes são os mesmos. Neste exemplo, o gateway VM com o IP público de 40.112.190.5 irá utilizar 10.12.255.4 como o respetivo endereço de Peering de BGP e o gateway com 138.91.156.129 utilizará 10.12.255.5. Esta informação é necessária quando configura os dispositivos VPN no local ao ligar ao gateway ativo-ativo. O gateway é mostrado no diagrama abaixo com todos os endereços:

![gateway de ativo-ativo](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Assim que for criado o gateway, pode utilizar este gateway para estabelecer a ligação de VNet a VNet ou ativo-ativo em vários locais. As secções seguintes irão percorrer os passos para concluir o exercício.

## <a name ="aacrossprem"></a>Parte 2 - estabelecer uma ligação de ativo-ativo em vários locais
Para estabelecer uma ligação entre locais, terá de criar um Gateway de rede Local para representar o dispositivo VPN no local e uma ligação para ligar o gateway de VPN do Azure com o gateway de rede local. Neste exemplo, o gateway de VPN do Azure está no modo ativo-ativo. Como resultado, mesmo apesar de não existir apenas um no local o dispositivo VPN (gateway de rede local) e de recursos de uma ligação, ambas as instâncias de gateway de VPN do Azure irão estabelecer túneis S2S VPN com o dispositivo no local.

Antes de continuar, certifique-se de que concluiu [parte 1](#aagateway) neste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passo 1 – criar e configurar o gateway de rede local
#### <a name="1-declare-your-variables"></a>1. Declarar as variáveis
Neste exercício, irá continuar criar a configuração mostrada no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Algumas coisas a salientar sobre os parâmetros de gateway de rede local:

* O gateway de rede local pode estar no mesma ou outra localização e grupo de recursos como o gateway VPN. Este exemplo mostra-los em grupos de recursos diferente, mas na mesma localização do Azure.
* Se houver apenas um dispositivo VPN no local, conforme mostrado acima, a ligação de ativo-ativo pode trabalhar com ou sem protocolo BGP. Este exemplo utiliza o BGP para a ligação entre locais.
* Se o BGP está ativado, o prefixo que tem de indicar para o gateway de rede local é o endereço do anfitrião do seu endereço de IP do elemento BGP no seu dispositivo VPN. Neste caso, é um /32 prefixo do nome de "10.52.255.253/32".
* Como um lembrete, tem de utilizar ASNs diferentes de BGP entre as redes no local e a VNet do Azure. Se estes são os mesmos, terá de alterar o ASN de VNet, se o dispositivo VPN no local que já utiliza o ASN para peering com outros vizinhos BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Criar o gateway de rede local para Site5
Antes de continuar, verifique se ainda está ligado à Subscrição 1. Crie o grupo de recursos se ainda não é criado.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2 - ligar o gateway de VNet e o gateway de rede local
#### <a name="1-get-the-two-gateways"></a>1. Obter os dois gateways

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Criar a ligação da TestVNet1 para Site5
Neste passo, vai criar a ligação da TestVNet1 para Site5_1 com "EnableBGP" definida como $True.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parâmetros VPN e BGP para o dispositivo VPN no local
O exemplo abaixo lista os parâmetros que irá introduzir na secção de configuração de BGP no dispositivo VPN no local para este exercício:

    - Site5 ASN: 65050
    - IP do BGP Site5: 10.52.255.253
    - Prefixos a anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - ASN de VNet do Azure: 65010
    - IP de BGP do Azure VNet 1: 10.12.255.4 para túnel para 40.112.190.5
    - IP de BGP do Azure VNet 2: 10.12.255.5 para túnel para 138.91.156.129
    - Rotas estáticas: destino 10.12.255.4/32, nexthop da interface de túnel VPN para 40.112.190.5 destino 10.12.255.5/32, nexthop da interface do túnel VPN para 138.91.156.129
    - eBGP Multihop: Certifique-se a opção "multihop" para eBGP está ativado no seu dispositivo, se for necessário

Deve ser estabelecida a ligação após alguns minutos e a sessão de peering de BGP será iniciado depois de é estabelecida a ligação IPsec. Neste exemplo, até ao momento tiver configurado apenas um dispositivo VPN no local, resultando no diagrama abaixo:

![ativo-ativo-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Passo 3 – ligar os dois dispositivos VPN no local para o gateway VPN de ativo-ativo
Se tiver dois dispositivos VPN na mesma rede no local, pode conseguir redundância dupla ao ligar o gateway de VPN do Azure para o segundo dispositivo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Crie o segundo gateway de rede local para Site5
Tenha em atenção que o endereço IP do gateway, o prefixo de endereço e o endereço peering de BGP para o segundo gateway de rede local não pode sobrepor com o gateway de rede local anterior para a mesma rede no local.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"

New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Ligar o gateway de VNet e o segundo gateway de rede local
Criar a ligação da TestVNet1 para Site5_2 com "EnableBGP" definida como $True

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parâmetros VPN e BGP para o segundo dispositivo VPN no local
Da mesma forma, abaixo apresenta uma lista de parâmetros que irá introduzir no segundo dispositivo VPN:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Assim que são estabelecida a ligação (túneis), terá duplos dispositivos VPN redundantes e túneis ligar a rede no local e o Azure:

![Dual-redundância-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Parte 3 - estabelecer uma ligação de VNet a VNet ativo-ativo
Esta secção cria uma ligação de VNet a VNet ativo-ativo com o BGP. 

As instruções abaixo são a continuação dos passos anteriores listados acima. Tem de concluir [parte 1](#aagateway) para criar e configurar a TestVNet1 e o Gateway de VPN com o BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passo 1 – criar TestVNet2 e o gateway VPN
É importante certificar-se de que o espaço de endereços IP da rede virtual novo, TestVNet2, não se sobreponha a nenhum dos intervalos sua VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet a VNet entre subscrições diferentes; consulte [configurar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md) para obter mais detalhes. Certifique-se de que adiciona o "-EnableBgp $True" quando criar as ligações para ativar o BGP.

#### <a name="1-declare-your-variables"></a>1. Declarar as variáveis
Confirme que substitui os valores pelos que pretende utilizar para a configuração.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Criar o gateway VPN de ativo-ativo para TestVNet2
O pedido dois endereços IP públicos para ser atribuído ao gateway que criará para a sua VNet. Também irá definir a sub-rede e configurações de IP necessárias.

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Crie o gateway VPN com o número AS e o sinalizador "EnableActiveActiveFeature". Tenha em atenção que tem de substituir a predefinição ASN em gateways de VPN do Azure. Os ASNs para as VNets ligados têm de ser diferentes para ativar o BGP e o encaminhamento de trânsito.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2 – ligar os gateways TestVNet1 e TestVNet2
Neste exemplo, ambos os gateways estão na mesma subscrição. Pode concluir este passo na mesma sessão do PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obter ambas as gateways
Confirme que inicia sessão e se liga à Subscrição 1.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Criar ambas as ligações
Neste passo, criará a ligação da TestVNet1 para TestVNet2 e a ligação de TestVNet2 para a TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> É necessário ativar o BGP para ambas as ligações.
> 
> 

Depois de concluir estes passos, irá ser estabelecer a ligação em alguns minutos e o BGP sessão peering será cópias de segurança depois de concluída a ligação VNet a VNet com redundância dupla:

![ativo-ativo-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Parte 4 - atualizar gateway existente entre ativo-ativo e modo de espera ativo
A última seção descreve como pode configurar um gateway de VPN do Azure existente do modo de espera ativo para o modo ativo-ativo, ou vice versa.

> [!NOTE]
> Esta secção inclui os passos para redimensionar um SKU legado (antigo SKU) de um gateway de VPN já criou de Standard para HighPerformance. Estes passos não atualizar um SKU de legado antigo dos SKUs de novo.
> 
> 

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configurar um gateway de modo de espera ativo para o gateway de ativo-ativo
#### <a name="1-gateway-parameters"></a>1. Parâmetros de gateway
O exemplo seguinte converte um gateway de modo de espera ativo de um gateway de ativo-ativo. Terá de criar outro endereço IP público, em seguida, adicionar uma segunda configuração de IP do Gateway. Abaixo mostra os parâmetros utilizados:

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"

$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Criar o endereço IP público, em seguida, adicionar a segunda configuração de IP do gateway

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Ativar o modo ativo-ativo e atualizar o gateway
Tem de definir o objeto do gateway no PowerShell para acionar a atualização real. O SKU do gateway de rede virtual tem também de ser alterado (redimensionado) para HighPerformance desde que foi criado anteriormente como padrão.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

Esta atualização pode demorar 30 para 45 minutos.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configurar um gateway de ativo-ativo para o gateway de modo de espera ativo
#### <a name="1-gateway-parameters"></a>1. Parâmetros de gateway
Utilizar os mesmos parâmetros conforme apresentado acima, obter o nome da configuração de IP que pretende remover.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"

$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Remova a configuração de IP do gateway e desativar o modo ativo-ativo
Da mesma forma, tem de definir o objeto do gateway no PowerShell para acionar a atualização real.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Esta atualização pode demorar até 30 45 minutos.

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.