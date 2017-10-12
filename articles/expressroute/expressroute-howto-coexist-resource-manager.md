---
title: "Configurar as ligações de Rede de VPNs e ExpressRoute que podem coexistir: Resource Manager: Azure | Microsoft Docs"
description: "Este artigo explica-lhe como configurar o ExpressRoute e uma ligação de Rede de VPNs que pode coexistir para o modelo do Resource Manager."
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: charwen,cherylmc
ms.openlocfilehash: b29147a37f9a90fc80e16b350ac9b91daac1d7f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>Configurar ligações coexistentes do ExpressRoute e de Site a Site
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Clássica](expressroute-howto-coexist-classic.md)
> 
> 

A configuração de ligações de Rede de VPNs e ExpressRoute coexistentes tem várias vantagens. Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que se encontram ligados através do ExpressRoute. Abordamos os passos para configurar ambos os cenários neste artigo. Este artigo aplica-se ao modelo de implementação do Resource Manager e utiliza o PowerShell. Esta configuração não está disponível no Portal do Azure.

> [!IMPORTANT]
> Os circuitos ExpressRoute têm de ser previamente configurados antes de seguir as instruções abaixo. Confirme que seguiu os guias para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [configurar o encaminhamento](expressroute-howto-routing-arm.md) antes de continuar.
> 
> 

## <a name="limits-and-limitations"></a>Limites e limitações
* **Encaminhamento de tráfego não suportado.** Não pode encaminhar (através do Azure) entre a sua rede local ligada através da Rede de VPNs e a sua rede local ligada através do ExpressRoute.
* **Gateway do SKU Básico não suportado.** Tem de utilizar um gateway do SKU não Básico para o [Gateway do ExpressRoute](expressroute-about-virtual-network-gateways.md) e para o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **É apenas suportado o Gateway de VPN baseado na rota.** Tem de utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) baseado na rota.
* **A rota estática deve ser configurada para o seu Gateway de VPN.** Se a sua rede local estiver ligada ao ExpressRoute e a uma Rede de VPNs, terá de ter uma rota estática configurada na rede local para encaminhar a ligação de Rede de VPNs para a Internet pública.
* **O gateway do ExpressRoute tem de ser configurado primeiro e ligado a um circuito.** Tem de criar primeiro o Gateway do ExpressRoute e ligá-lo a um circuito antes de adicionar o Gateway de Rede de VPNs.

## <a name="configuration-designs"></a>Estruturas de configuração
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma Rede de VPNs como um caminho de ativação pós-falha para o ExpressRoute
Pode configurar uma ligação de Rede de VPNs como uma cópia de segurança para o ExpressRoute. Isto aplica-se apenas às redes virtuais ligadas ao caminho de peering privado do Azure. Não existe qualquer solução de ativação pós-falha baseada em VPN para os serviços acessíveis através dos peerings público do Azure e da Microsoft. O circuito ExpressRoute é sempre a ligação primária. Os dados percorrem o caminho da Rede de VPNs apenas se o circuito ExpressRoute falhar.

> [!NOTE]
> Apesar de o circuito do ExpressRoute ser preferível face à Rede de VPNs quando ambas as rotas são as mesmas, o Azure irá utilizar a correspondência de prefixo mais longo para escolher a rota de acordo com o destino do pacote.
> 
> 

![Coexistir](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma Rede de VPNs para se ligar a sites não ligados através do ExpressRoute
Pode configurar a sua rede para um local no qual alguns sites se ligam diretamente ao Azure através da Rede de VPNs e alguns sites estabelecem ligação através do ExpressRoute. 

![Coexistir](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Não pode configurar uma rede virtual como um router de tráfego.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selecionar os passos a utilizar
Existem dois conjuntos de procedimentos diferentes à escolha. O procedimento de configuração que selecionar depende do facto de pretender ligar-se a uma rede virtual já existente ou criar uma nova.

* Não tenho uma VNet e preciso de criar uma.
  
    Se ainda não tem uma rede virtual, este procedimento orienta-o durante a criação de uma rede virtual nova com o modelo de implementação Resource Manager e a criação de ligações de Rede de VPNs e ExpressRoute novas. Para configurar uma rede virtual, siga os passos em [Para criar uma rede virtual nova e ligações coexistentes](#new).
* Já tenho uma VNet do modelo de implementação Resource Manager.
  
    Pode já ter uma rede virtual no local com uma ligação ExpressRoute ou de Rede de VPNs existente. A secção [Para configurar ligações coexistentes a uma VNet já existente](#add) orienta-o através da eliminação do gateway e, em seguida, da criação de novas ligações ExpressRoute e de Rede de VPNs. Quando criar as ligações novas, os passos têm de ser concluídos por uma ordem específica. Não utilize as instruções de outros artigos para criar os seus gateways e ligações.
  
    Neste procedimento, a criação de ligações que podem coexistir implica eliminar o seu gateway e, em seguida, configurar novos gateways. Terá um período de inatividade para as suas ligações entre locais enquanto elimina e recria o seu gateway e as suas ligações, mas não terá de migrar nenhuma das suas VMs ou serviços para uma nova rede virtual. As VMs e os serviços continuarão a poder comunicar através do balanceador de carga enquanto configura o seu gateway, se estiverem configurados para tal.

## <a name="new"></a>Para criar uma nova rede virtual e ligações coexistentes
Este procedimento orienta-o ao longo da criação de uma VNet e de ligações ExpressRoute e de Rede de VPNs que vão coexistir.

1. Instale a versão mais recente dos cmdlets do Azure PowerShell. Para obter mais informações sobre como instalar os cmdlets do PowerShell, veja [How to install and configure Azure PowerShell](/powershell/azure/overview) (Como instalar e configurar o Azure PowerShell). Os cmdlets que vai utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções.
2. Inicie sessão na sua conta e configure o ambiente.

  ```powershell
  login-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65010
  ```
3. Crie uma rede virtual, incluindo uma Sub-Rede do Gateway. Para obter mais informações sobre a configuração da rede virtual, veja [Configuração da Virtual Network do Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).
   
   > [!IMPORTANT]
   > A sub-rede do Gateway tem de ser /27 ou ter um prefixo mais curto (como /26 ou /25).
   > 
   > 
   
    Crie uma nova VNet.

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    Adicione sub-redes.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Guarde a configuração da VNet.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="gw"></a>Crie um Gateway do ExpressRoute. Para obter mais informações sobre a configuração do gateway ExpressRoute, veja [Configuração do gateway ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). O GatewaySKU deve ser *Standard*, *HighPerformance* ou *UltraPerformance*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
5. Ligue o gateway ExpressRoute ao circuito ExpressRoute. Quando tiver concluído este passo, a ligação entre a sua rede no local e do Azure, através do ExpressRoute, é estabelecida. Para obter mais informações sobre a operação de ligação, veja [Ligar VNets ao ExpressRoute](expressroute-howto-linkvnet-arm.md).

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```
6. <a name="vpngw"></a>Em seguida, crie o Gateway de Rede de VPNs. Para obter mais informações sobre a configuração do Gateway de VPN, veja [Configurar uma VNet com uma ligação de Rede de VPNs](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). O GatewaySKU deve ser *Standard*, *HighPerformance* ou *UltraPerformance*. O VpnType deve ser *RouteBased*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
  ```
   
    O gateway de VPN do Azure oferece suporte ao protocolo de encaminhamento BGP. Pode especificar o ASN (Número AS) para essa Rede Virtual, ao adicionar o comutador -Asn no comando seguinte. Se não especificar esse parâmetro, o padrão do número AS será 65515.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -Asn $VNetASN
  ```
   
    Pode encontrar o IP do peering de BGP e o número AS que o Azure utiliza para o Gateway de VPN em $azureVpn.BgpSettings.BgpPeeringAddress e $azureVpn.BgpSettings.Asn. Para obter mais informações, veja o artigo [Configure BGP (Configurar o BGP)](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para o Gateway de VPN do Azure.
7. Crie uma entidade de gateway de VPN de site local. Este comando não configurar o seu gateway de VPN no local. Em vez disso, este permite-lhe fornecer as definições do gateway local, tal como o IP público e o espaço de endereço no local, para que o gateway de VPN do Azure se possa ligar a este.
   
    Se o seu dispositivo VPN local suportar apenas o encaminhamento estático, pode configurar as rotas estáticas da seguinte forma:

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    Se o seu dispositivo VPN local suportar o BGP e se pretender ativar o encaminhamento dinâmico, terá de conhecer o IP do peering de BGP e o número AS que o seu dispositivo VPN local utiliza.

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
8. Configure o seu dispositivo VPN local para estabelecer ligação com o novo gateway de VPN do Azure. Para obter mais informações sobre a configuração do dispositivo VPN, veja [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Ligue o gateway de Rede de VPNs no Azure ao gateway local.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```

## <a name="add"></a>Para configurar ligações coexistentes a uma VNet já existente
Se tiver uma rede virtual existente, verifique o tamanho da sub-rede do gateway. Se a sub-rede do gateway é /28 ou /29, tem primeiro de eliminar o gateway da rede virtual e aumentar o tamanho da sub-rede do gateway. Os passos nesta secção mostram-lhe como o fazer.

Se a sub-rede do gateway é /27 ou superior e a rede virtual está ligada através do ExpressRoute, pode ignorar os passos abaixo e avançar para [“Passo 6 – Criar um gateway de Rede de VPNs”](#vpngw) na secção anterior. 

> [!NOTE]
> Ao eliminar o gateway existente, o local irá perder a ligação à sua rede virtual enquanto estiver a trabalhar nesta configuração. 
> 
> 

1. Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Para obter mais informações sobre como instalar os cmdlets, veja [How to install and configure Azure PowerShell](/powershell/azure/overview) (Como instalar e configurar o Azure PowerShell). Os cmdlets que vai utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções. 
2. Elimine o gateway ExpressRoute ou de Rede de VPNs existente.

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. Elimine a sub-Rede do Gateway.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. Adicione uma Sub-Rede do Gateway que é /27 ou superior.
   
   > [!NOTE]
   > Se não tem endereços IP suficientes na sua rede virtual para aumentar o tamanho da sub-rede do gateway, tem de adicionar mais espaço de endereços IP.
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Guarde a configuração da VNet.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. Nesta fase, já tem uma VNet sem quaisquer gateways. Para criar gateways novos e concluir as suas ligações, pode continuar com [Passo 4 – Criar um gateway ExpressRoute](#gw), presente no conjunto de passos anterior.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Para adicionar uma configuração ponto a site para o gateway de VPN
Pode seguir os passos abaixo para adicionar a configuração Ponto a Site para o seu gateway de VPN numa configuração de coexistência.

1. Adicione um conjunto de endereços de Cliente de VPN.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. Carregue o certificado de raiz VPN para o Azure para o seu gateway de VPN. Neste exemplo, é assumido que o certificado de raiz é armazenado no computador local onde são executados os seguintes cmdlets PowerShell.

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

Para obter mais informações sobre VPNs Ponto a Site, veja [Configurar uma ligação Ponto a Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
