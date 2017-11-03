---
title: "Ligar os gateways de VPN do Azure para dispositivos VPN baseado na política vários locais: o Gestor de recursos do Azure: PowerShell | Microsoft Docs"
description: "Este artigo explica como configurar o gateway de VPN baseado na rota do Azure para vários baseadas em políticas dispositivos VPN com o Azure Resource Manager e o PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/27/2017
ms.author: yushwang
ms.openlocfilehash: db4d8837fb5c5d15364422e957e4914966215674
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Ligar os gateways de VPN do Azure para vários dispositivos no local baseadas em políticas VPN com o PowerShell

Este artigo ajuda-o a configurar um Azure baseado na rota gateway de VPN para ligar a vários dispositivos no local baseadas em políticas VPN tirar partido das políticas de IPsec/IKE personalizadas em ligações de S2S VPN.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>Acerca dos gateways de VPN baseada em políticas e baseado na rota

Política - *vs* dispositivos VPN baseado na rota diferem em como os seletores de tráfego IPsec são definidas numa ligação:

* **Baseado na política** dispositivos VPN utilizam as combinações de prefixos de ambas as redes para definir a forma como o tráfego é encriptado/desencriptar através de túneis IPsec. Baseia-se normalmente em dispositivos de firewall que efetuem a filtragem de pacotes. Encriptação de túnel IPsec e desencriptação são adicionados ao pacote filtragem e de motor de processamento.
* **Baseado na rota** dispositivos VPN utilizam seletores de tráfego de qualquer a qualquer (carateres universais) e permitir que o encaminhamento/reencaminhamento tabelas direta tráfego diferentes de túneis IPsec. Baseia-se normalmente em plataformas de router onde cada túnel IPsec é modelada como uma interface de rede ou VTI (interface de túnel virtual).

Os diagramas seguintes realce dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo VPN baseada em políticas
![baseado na política](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo VPN baseado na rota
![baseado na rota](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Suporte do Azure para VPN baseada em políticas
Atualmente, o Azure suporta ambos os modos de gateways VPN: gateways de VPN baseado na rota e gateways de VPN baseado na política. Estes são criadas em diferentes plataformas internas, que resultam numa especificações diferentes:

|                          | **Gateway de PolicyBased VPN** | **Gateway de RouteBased VPN**               |
| ---                      | ---                         | ---                                      |
| **SKU de Gateway do Azure**    | Básica                       | Básico, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Versão do IKE**          | IKEv1                       | IKEv2                                    |
| **Máx. Ligações S2S** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Com a política personalizada do IPsec/IKE, agora pode configurar gateways de VPN baseado na rota do Azure para utilizar seletores de tráfego com base no prefixo com a opção "**PolicyBasedTrafficSelectors**", ligue a dispositivos VPN baseado na política no local. Esta capacidade permite-lhe ligar a partir de uma rede virtual do Azure e gateway VPN para vários baseadas em políticas VPN/firewall dispositivos locais, a remover o limite de ligação única dos atuais do Azure baseada em políticas gateways de VPN.

> [!IMPORTANT]
> 1. Para ativar esta conetividade, tem de suportar os dispositivos VPN baseado na política local **IKEv2** para estabelecer ligação com os gateways VPN baseado na rota do Azure. Verifique as especificações de dispositivo VPN.
> 2. As redes no local ligar através de dispositivos VPN baseado na política com este mecanismo apenas poderá ligar à rede virtual do Azure; **não é possível transitar para outras redes no local e as redes virtuais através do mesmo gateway de VPN do Azure**.
> 3. A opção de configuração faz parte da política personalizada de ligação do IPsec/IKE. Se ativar a opção de Seletor de tráfego baseadas em políticas, tem de especificar a política concluída (algoritmos de encriptação e a integridade do IPsec/IKE, força da codificação chave e durações de SA).

O diagrama seguinte mostra o motivo pelo qual o encaminhamento de trânsito através do gateway de VPN do Azure não funciona com a opção baseado na política:

![trânsito baseadas em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Como é mostrado no diagrama, o gateway de VPN do Azure tem seletores de tráfego de rede virtual para cada um dos prefixos de rede no local, mas não os prefixos de ligação cruzada. Por exemplo, no local site 2, 3 do site e site 4 podem cada comunicar para a VNet1, respetivamente, mas não é possível ligar através do gateway de VPN do Azure uns aos outros. O diagrama mostra os seletores de tráfego de cross-connect que não estão disponíveis no gateway de VPN do Azure nesta configuração.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>Configurar seletores de tráfego baseado na política numa ligação

As instruções neste artigo siga o mesmo exemplo, conforme descrito em [política IPsec/IKE configurar para ligações S2S ou VNet a VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) estabelecer uma ligação S2S VPN. É mostrado no diagrama seguinte:

![política de s2s](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para ativar esta conectividade:
1. Criar a rede virtual, o gateway VPN e o gateway de rede local para a ligação entre locais
2. Criar uma política de IPsec/IKE
3. Aplicar a política quando cria uma ligação S2S ou VNet a VNet e **ativar os seletores de tráfego baseadas em políticas** na ligação
4. Se a ligação já está a ser criada, pode aplicar ou atualizar a política para uma ligação existente

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>Ativar seletores de tráfego baseado na política numa ligação

Certifique-se de que concluiu [parte 3 do artigo de política IPsec/IKE configurar](vpn-gateway-ipsecikepolicy-rm-powershell.md) para esta secção. O exemplo seguinte utiliza os mesmos parâmetros e passos:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passo 1 – criar a rede virtual, o gateway VPN e o gateway de rede local

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. Declarar as variáveis & ligar à sua subscrição
Para este exercício, vamos começar por declarar as nossas variáveis. Verifique se substitui os valores pelos seus quando configurar para produção.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```
Para utilizar os cmdlets Resource Manager, certifique-se de que muda para o modo do PowerShell. Para obter mais informações, veja [Utilizar o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar na ligação:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Criar a rede virtual, o gateway VPN e o gateway de rede local
O exemplo seguinte cria a rede virtual, TestVNet1 com três sub-redes e o gateway VPN. Quando estiver a substituir os valores, é importante que pode sempre nome à sub-rede do gateway especificamente "GatewaySubnet". Se der outro nome, a criação da gateway falha.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passo 2 - criar uma ligação S2S VPN com uma política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

> [!IMPORTANT]
> Terá de criar uma política de IPsec/IKE para ativar a opção "UsePolicyBasedTrafficSelectors" na ligação.

O exemplo seguinte cria uma política de IPsec/IKE com estes algoritmos e parâmetros:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, SA duração 3600 segundos & 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Criar a ligação S2S VPN com seletores de tráfego baseadas em políticas e a política de IPsec/IKE
Criar uma ligação S2S VPN e aplicar a política de IPsec/IKE que criou no passo anterior. Tenha em atenção o parâmetro adicional "-UsePolicyBasedTrafficSelectors $True" que permite que os seletores de tráfego baseadas em políticas na ligação.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Depois de concluir os passos, a ligação S2S VPN irá utilizar a política de IPsec/IKE definida e para permitir seletores de tráfego baseadas em políticas na ligação. Pode repetir os mesmos passos para adicionar mais ligações de dispositivos VPN baseado na política no local adicionais do mesmo gateway de VPN do Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Seletores de tráfego baseado na política para uma ligação de atualização
A última seção mostra-lhe como atualizar a opção de seletores de tráfego baseado na política para uma ligação S2S VPN existente.

### <a name="1-get-the-connection"></a>1. Obter a ligação
Obter o recurso de ligação.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Selecione a opção de seletores de tráfego baseadas em políticas
A seguinte linha mostra se os seletores de tráfego baseadas em políticas são utilizadas para a ligação:

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

Se a linha devolve "**verdadeiro**", em seguida, seletores de tráfego baseado na política são configurados na ligação; caso contrário, devolve "**falso**."

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. Os seletores de tráfego baseado na política numa ligação de atualização
Depois de obter o recurso de ligação, pode ativar ou desativar a opção.

#### <a name="disable-usepolicybasedtrafficselectors"></a>Desativar UsePolicyBasedTrafficSelectors
O exemplo a seguir desativa a opção de seletores de tráfego baseadas em políticas, mas deixa a política de IPsec/IKE inalterada:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>Ativar UsePolicyBasedTrafficSelectors
O exemplo seguinte ativa a opção de seletores de tráfego baseadas em políticas, mas deixa a política de IPsec/IKE inalterada:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.

Reveja também [política IPsec/IKE configurar para ligações S2S VPN ou VNet a VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre as políticas personalizadas do IPsec/IKE.
