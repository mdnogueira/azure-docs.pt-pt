---
title: 'Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs: Portal | Microsoft Docs'
description: "Passos para criar uma ligação IPsec da sua rede no local a uma rede virtual do Azure através da Internet pública. Estes passos ajudam-no a criar uma ligação de Gateway de Rede de VPNs em vários sites com o portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eea00841708212789e14fa8717d83dd81d472bac
ms.openlocfilehash: c99788aded849933289555dd133b8146d6b2bdf2


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Criar uma VNet com uma ligação de Rede de VPNs no portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássica – Portal Clássico](vpn-gateway-site-to-site-create.md)
> 
> 

Este artigo explica como criar uma rede virtual e uma ligação de gateway de Rede de VPNs para sua rede no local com o modelo de implementação Azure Resource Manager e o Portal do Azure. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas.

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos e métodos de implementação atualmente disponíveis para configurações Site a Site. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se pretender ligar VNets entre si, mas não estiver a criar uma ligação para uma localização no local, veja [Configurar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se pretender adicionar uma ligação Site a Site a uma VNet que já tem uma ligação, veja [Adicionar uma ligação S2S a uma VNet com uma ligação de gateway de VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens:

* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](http://azure.microsoft.com/pricing/free-trial).

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>Valores de configuração de exemplo para este exercício
Quando utiliza estes passos como um exercício, pode utilizar os valores de configuração de exemplo:

* **Nome da VNet:** TestVNet1
* **Espaço de Endereços:** 10.11.0.0/16 e 10.12.0.0/16
* **Sub-redes:**
  * Front-End: 10.11.0.0/24
  * Back-End: 10.12.0.0/24
  * GatewaySubnet: 10.12.255.0/27
* **Grupo de Recursos:** TestRG1
* **Localização:** E.U.A. Leste
* **Servidor DNS:** 8.8.8.8
* **Nome do Gateway:** VNet1GW
* **IP Público:** VNet1GWIP
* **Tipo de VPN:** Baseada na rota
* **Tipo de Ligação:** Ste a site (IPsec)
* **Tipo de Gateway:** VPN
* **Nome do Gateway de Rede Local:** Site2
* **Nome da Ligação:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Criar uma rede virtual
Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente. Se a VNet estiver configurada com as definições corretas, pode iniciar os passos da secção [Especificar um servidor DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2. Adicionar sub-redes e espaços de endereços adicionais
Pode adicionar sub-redes e espaços de endereços adicionais à VNet depois de ter sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3. Especificar um servidor DNS
### <a name="to-specify-a-dns-server"></a>Para especificar um servidor DNS
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4. Criar uma sub-rede do gateway
Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Se possível, é melhor criar uma sub-rede do gateway com um bloco CIDR de /28 ou /27, de modo a fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao criar a sub-rede do gateway.

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Criar um gateway de rede virtual
Se estiver a criar esta configuração como um exercício, pode ver os [valores de configuração de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6. Criar um gateway de rede local
O “gateway de rede local” refere-se à sua localização no local. Atribua um nome ao gateway de rede local para o Azure o identificar. 

Se estiver a criar esta configuração como um exercício, pode ver os [valores de configuração de exemplo](#values).

### <a name="to-create-a-local-network-gateway"></a>Para criar um gateway de rede local
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7. Configurar o dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8. Criar uma ligação de Rede de VPNs
Crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN. Não se esqueça de substituir os valores pelos seus. A chave partilhada tem de corresponder ao valor utilizado na configuração do dispositivo VPN. 

Antes de iniciar esta secção, verifique se foi concluída a criação do gateway de rede virtual e dos gateways de rede local. Se estiver a criar esta configuração como um exercício, veja estes [valores](#values) ao criar a ligação.

### <a name="to-create-the-vpn-connection"></a>Para criar a ligação VPN
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9. Verificar a ligação VPN
Pode verificar a ligação VPN no portal ou com o PowerShell.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes
*  Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Jan17_HO4-->


