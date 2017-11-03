---
title: "Configurações de dispositivo VPN para estabelecer a ligação para gateways de VPN do Azure de parceiros | Microsoft Docs"
description: "Este artigo fornece uma descrição geral das configurações de dispositivo VPN de parceiro para ligar a gateways de VPN do Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b3806d16d3b78347e183ecbd2ab5a463a2142110
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Descrição geral das configurações de dispositivo VPN de parceiro
Este artigo fornece uma descrição geral da configuração de dispositivos VPN no local para estabelecer a ligação para gateways de VPN do Azure. A rede virtual do Azure de exemplo e configuração do gateway VPN é utilizada para lhe mostrar como ligar a configurações de dispositivos VPN de diferentes no local utilizando os mesmos parâmetros.

## <a name="device-requirements"></a>Requisitos de dispositivos
Gateways de VPN do Azure utilizam conjuntos de protocolo de IPsec/IKE padrão para VPN de site a site (S2S) túneis. Para obter uma lista dos parâmetros de IPsec/IKE e algoritmos criptográficos para gateways de VPN do Azure, consulte [acerca dos dispositivos VPN](vpn-gateway-about-vpn-devices.md). Também pode especificar os algoritmos exatos e força da codificação chave para uma ligação específica, tal como descrito no [sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Túnel VPN único
A configuração do primeiro na amostra é composta por um único túnel de S2S VPN entre um gateway de VPN do Azure e um dispositivo VPN no local. Opcionalmente, pode configurar o [Border Gateway Protocol (BGP) através do túnel VPN](#bgp).

![Diagrama de um túnel S2S VPN único](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Para obter instruções passo a passo configurar um único túnel VPN, consulte [configurar uma ligação site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). As secções seguintes especificar os parâmetros de ligação para a configuração de exemplo e fornecem um script do PowerShell para o ajudar a começar a utilizar.

### <a name="connection-parameters"></a>Parâmetros de ligação
Esta secção lista os parâmetros para os exemplos que são descritos nas secções anteriores.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereços de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway VPN do Azure         | Gateway VPN do Azure IP         |
| Prefixos de endereço no local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN no local    | IP do dispositivo VPN no local    |
| * Rede virtual ASN de BGP                | 65010                        |
| * IP do elemento BGP do azure           | 10.12.255.30                 |
| * No local ASN de BGP         | 65050                        |
| * IP do elemento BGP no local     | 10.52.255.254                |

\*Parâmetro opcional para BGP apenas.

### <a name="sample-powershell-script"></a>Exemplo de script do PowerShell
Esta secção fornece um script de exemplo para ajudar a começar. Para obter instruções detalhadas, consulte [criar uma ligação S2S VPN utilizando o PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Opcional) Utilizar a política personalizada do IPsec/IKE com UsePolicyBasedTrafficSelectors
Se os dispositivos VPN não suportam seletores de qualquer a qualquer tráfego, tais como configurações baseado em VTI ou baseadas na rota, crie uma política de IPsec/IKE personalizada com o [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) opção.

> [!IMPORTANT]
> Tem de criar uma política de IPsec/IKE para ativar o **UsePolicyBasedTrafficSelectors** opção na ligação.


O script de exemplo cria uma política de IPsec/IKE com os parâmetros e os algoritmos seguintes:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256 SHA1, PFS24, SA duração 7,200 segundos e 20,480,000 KB (20 GB)

O script aplica-se a política de IPsec/IKE e ativa o **UsePolicyBasedTrafficSelectors** opção na ligação.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Opcional) Utilizar o BGP numa ligação S2S VPN
Ao criar a ligação S2S VPN, opcionalmente, pode utilizar [BGP para o gateway VPN](vpn-gateway-bgp-resource-manager-ps.md). Esta abordagem tem duas diferenças:

* Os prefixos de endereço no local podem ser um endereço de anfitrião único. O endereço IP do elemento de rede BGP no local é especificado da seguinte forma:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Ao criar a ligação, tem de definir o **- EnableBGP** opção para $True:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo configurar os gateways de VPN de ativo-ativo, consulte [configurar gateways de VPN de ativo-ativo em vários locais e ligações VNet a VNet](vpn-gateway-activeactive-rm-powershell.md).

