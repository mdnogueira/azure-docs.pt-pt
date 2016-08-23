<properties
   pageTitle="Configurar ligações ExpressRoute e de Rede de VPNs que podem coexistir para o modelo de implementação Resource Manager | Microsoft Azure"
   description="Este artigo explica-lhe como configurar o ExpressRoute e uma ligação de Rede de VPNs que pode coexistir para o modelo do Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="charleywen"/>

# Configurar ligações ExpressRoute e de Site a Site coexistentes para o modelo de implementação Resource Manager

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell – Clássico](expressroute-howto-coexist-classic.md)

A capacidade de configurar o ExpressRoute e a Rede de VPNs tem várias vantagens. Pode configurar a Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que se encontram ligados através do ExpressRoute. Abordaremos os passos para configurar ambos os cenários neste artigo. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager. Esta configuração não está disponível no Portal do Azure.


**Acerca dos modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Os circuitos ExpressRoute têm de ser previamente configurados antes de seguir as instruções abaixo. Confirme que seguiu os guias para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [configurar o encaminhamento](expressroute-howto-routing-arm.md) antes de seguir os passos abaixo.

## Limites e limitações

- **O encaminhamento de tráfego não é suportado:** não pode criar um encaminhamento (através do Azure) entre a sua rede local ligada através da Rede de VPNs e a sua rede local ligada por ExpressRoute.
- **Não pode ativar a imposição do túnel no gateway da Rede de VPNs:** pode apenas “forçar” todo o tráfego da Internet a voltar à sua rede no local através do ExpressRoute. 
- **Apenas gateways standard ou de elevado desempenho:** tem de utilizar um gateway standard ou de elevado desempenho para o gateway ExpressRoute e o gateway de Rede de VPNs. Veja [SKUs de Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) para obter informações sobre SKUs de gateway.
- **Apenas gateways de VPN baseados em rota:** tem de utilizar um gateway de VPN baseado na rota. Veja [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) para obter informações sobre o gateway de VPN baseado na rota.
- **Requisito da rota estática:** se a sua rede local está ligada ao ExpressRoute e a uma Rede de VPNs, tem de ter uma rota estática configurada na sua rede local para encaminhar a ligação de Rede de VPNs para a Internet pública.
- **O gateway do ExpressRoute tem de ser configurado primeiro:** tem de criar primeiro o gateway do ExpressRoute antes de adicionar o gateway de Rede de VPNs.


## Estruturas de configuração

### Configurar uma Rede de VPNs como um caminho de ativação pós-falha para o ExpressRoute

Pode configurar uma ligação de Rede de VPNs como uma cópia de segurança para o ExpressRoute. Isto aplica-se apenas às redes virtuais ligadas ao caminho de peering privado do Azure. Não existe qualquer solução de ativação pós-falha baseada em VPN para os serviços acessíveis através dos peerings público do Azure e da Microsoft. O circuito ExpressRoute é sempre a ligação primária. Os dados percorrerão o caminho da Rede de VPNs apenas se o circuito ExpressRoute falhar. 

![Coexistir](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Configurar uma Rede de VPNs para se ligar a sites não ligados através do ExpressRoute

Pode configurar a sua rede para um local no qual alguns sites se ligam diretamente ao Azure através da Rede de VPNs e alguns sites estabelecem ligação através do ExpressRoute. 

![Coexistir](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Não pode configurar uma rede virtual como um router de tráfego.

## Selecionar os passos a utilizar

Existem dois conjuntos diferentes de procedimentos à sua escolha para configurar ligações que podem coexistir. O procedimento de configuração que selecionou dependerá do facto de pretender ligar-se a uma rede virtual já existente ou criar uma nova rede virtual.


- Não tenho uma VNet e preciso de criar uma.
    
    Se ainda não tem uma rede virtual, este procedimento irá orientá-lo durante a criação de uma nova rede virtual com o modelo de implementação Resource Manager e de novas ligações de Rede de VPNs e ExpressRoute. Para configurar, siga os passos na secção do artigo [Para criar uma nova rede virtual e ligações coexistentes](#new).

- Já tenho uma VNet do modelo de implementação Resource Manager.

    Pode já ter uma rede virtual no local com uma ligação ExpressRoute ou de Rede de VPNs existente. A secção [Para configurar ligações coexistentes para uma VNet já existente](#add) irá orientá-lo através da eliminação do gateway e, em seguida, a criação de novas ligações ExpressRoute e de Rede de VPNs. Tenha em atenção que, quando criar novas ligações, os passos têm de ser concluídos por uma ordem muito específica. Não utilize as instruções de outros artigos para criar os seus gateways e ligações.

    Neste procedimento, a criação de ligações coexistentes implicará eliminar o seu gateway e, em seguida, configurar os novos gateways. Isto significa que terá período de inatividade para as suas ligações entre locais enquanto elimina e recria o seu gateway e as suas ligações, mas não terá de migrar qualquer uma das suas VMs ou serviços para uma nova rede virtual. As VMs e os serviços continuarão a poder comunicar através do balanceador de carga enquanto configura o seu gateway, se estiverem configurados para tal.


## <a name="new"></a>Para criar uma nova rede virtual e ligações coexistentes

Este procedimento irá explicar-lhe como criar uma VNet e criar ligações coexistentes ExpressRoute e de Rede de VPNs.
    
1. Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets PowerShell. Tenha em atenção que os cmdlets que irá utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções.

2. Inicie a sessão na sua conta e configure o ambiente.
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Crie uma rede virtual, incluindo uma Sub-Rede do Gateway. Para obter mais informações sobre a configuração da rede virtual, veja [Configuração da Virtual Network do Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

    >[AZURE.IMPORTANT] A sub-rede do Gateway tem de ser /27 ou ter um prefixo mais curto (como /26 ou /25).
    
    Crie uma nova VNet.

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    Adicione sub-redes.

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Guarde a configuração da VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Crie um gateway ExpressRoute. Para obter mais informações sobre a configuração do gateway ExpressRoute, veja [Configuração do gateway ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). O SKU do Gateway deve ser *Standard* ou de *Elevado Desempenho*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Ligue o gateway ExpressRoute ao circuito ExpressRoute. Quando tiver concluído este passo, a ligação entre a sua rede no local e do Azure, através do ExpressRoute, é estabelecida. Para obter mais informações sobre a operação de ligação, veja [Ligar VNets ao ExpressRoute](expressroute-howto-linkvnet-arm.md).

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>Em seguida, crie o gateway de Rede de VPNs. Para obter mais informações sobre a configuração do gateway de VPN, veja [Configurar uma VNet na ligação de VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). O SKU do Gateway deve ser *Standard* ou de *Elevado Desempenho*. O VpnType deve ser *RouteBased*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Crie uma entidade de gateway de VPN de site local. Este comando não configurar o seu gateway de VPN no local. Em vez disso, este permite-lhe fornecer as definições do gateway local, tal como o IP público e o espaço de endereço no local, para que o gateway de VPN do Azure se possa ligar a este. 
    >[AZURE.NOTE] Se a sua rede local tiver várias rotas, pode passá-las a todas como uma matriz.  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")  

        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Configure o seu dispositivo VPN local para estabelecer ligação com o novo gateway de VPN do Azure. Para obter mais informações sobre a configuração do dispositivo VPN, veja [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Ligue o gateway de Rede de VPNs no Azure ao gateway local.

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Para configurar ligações coexistentes a uma VNet já existente

Se tiver uma rede virtual existente, verifique o tamanho da sub-rede do gateway. Se a sub-rede do gateway é /28 ou /29, tem primeiro de eliminar o gateway da rede virtual e aumentar o tamanho da sub-rede do gateway. Os passos nesta secção mostrarão como o fazer.

Se a sub-rede do gateway é /27 ou superior e a rede virtual está ligada através do ExpressRoute, pode ignorar os passos abaixo e avançar para [“Passo 6 – Criar um gateway de Rede de VPNs”](#vpngw) na secção anterior. 

>[AZURE.NOTE] Ao eliminar o gateway existente, o local irá perder a ligação à sua rede virtual enquanto estiver a trabalhar nesta configuração. 

1. Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets PowerShell. Tenha em atenção que os cmdlets que irá utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções. 

2. Elimine o gateway ExpressRoute ou de Rede de VPNs existente. 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Elimine a sub-Rede do Gateway.
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Adicione uma Sub-Rede do Gateway que é /27 ou superior.
    >[AZURE.NOTE] Se não tem endereços IP suficientes na sua rede virtual para aumentar o tamanho da sub-rede do gateway, tem de adicionar mais espaço de endereços IP.

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Guarde a configuração da VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. Neste momento, terá uma VNet sem quaisquer gateways. Para criar gateways novos e concluir as suas ligações, pode continuar com [Passo 4 – Criar um gateway ExpressRoute](#gw), presente no conjunto de passos anterior.

## Para adicionar uma configuração ponto a site para o gateway de VPN
Pode seguir os passos abaixo para adicionar a configuração Ponto a Site para o seu gateway de VPN numa configuração de coexistência.

1. Adicione um conjunto de endereços de Cliente de VPN. 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Carregue o certificado de raiz VPN para o Azure para o seu gateway de VPN. Neste exemplo, é assumido que o certificado de raiz é armazenado no computador local onde são executados os seguintes cmdlets PowerShell. 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Para obter mais informações sobre VPNs Ponto a Site, veja [Configurar uma ligação Ponto a Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## Passos seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).



<!--HONumber=Aug16_HO1-->


