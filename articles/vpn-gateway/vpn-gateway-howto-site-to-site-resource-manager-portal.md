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
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Criar uma ligação Site a Site no portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássica – Portal do Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássica – Portal Clássico](vpn-gateway-site-to-site-create.md)
>
>


Uma ligação de gateway de VP de Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído ao mesmo e não se encontre protegido por um NAT. As ligações site a site podem ser utilizadas para configurações em vários locais e híbridas.

Este artigo explica como criar uma rede virtual e uma ligação de gateway de Rede de VPNs para sua rede no local com o modelo de implementação Azure Resource Manager e o Portal do Azure. 

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos e métodos de implementação atualmente disponíveis para configurações Site a Site. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se pretender ligar VNets entre si, mas não estiver a criar uma ligação para uma localização no local, veja [Configurar uma ligação VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se pretender adicionar uma ligação Site a Site a uma VNet que já tem uma ligação, veja [Adicionar uma ligação S2S a uma VNet com uma ligação de gateway de VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens:

* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Se não estiver familiarizado com os espaços de endereços IP localizados na rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. As ligações de Site a Site não podem ter espaços de endereços sobrepostos.
* Um endereço IP público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Valores de exemplo
Quando utiliza estes passos como um exercício, pode utilizar os seguintes valores de exemplo:

* **Nome da VNet:** TestVNet1
* **Espaço de Endereços:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (opcional para este exercício)
* **Sub-redes:**
  * Front-End: 10.11.0.0/24
  * Back-End: 10.12.0.0/24 (opcional para este exercício)
  * GatewaySubnet: 10.11.255.0/27
* **Grupo de Recursos:** TestRG1
* **Localização:** E.U.A. Leste
* **Servidor DNS:** o endereço IP do seu servidor DNS
* **Nome do Gateway de Rede Virtual:** VNet1GW
* **IP Público:** VNet1GWIP
* **Tipo de VPN:** Baseada na rota
* **Tipo de Ligação:** Ste a site (IPsec)
* **Tipo de Gateway:** VPN
* **Nome do Gateway de Rede Local:** Site2
* **Nome da Ligação:** VNet1toSite2

## <a name="CreatVNet"></a>1. Criar uma rede virtual

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. Especificar um servidor DNS
O DNS não é necessário para ligações de Site a Site. No entanto, se pretender ter a resolução de nomes para recursos que são implementados para a sua rede virtual, deve especificar um servidor DNS. Esta definição permite-lhe especificar o servidor DNS que pretende utilizar para a resolução de nomes desta rede virtual. Não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Criar uma sub-rede do gateway
Tem de criar uma sub-rede de gateway para o gateway de VPN. A sub-rede do gateway contém os endereços IP que os serviços do gateway de VPN irão utilizar. Se possível, crie uma sub-rede de gateway com um bloco CIDR de /28 ou /27. Isto irá garantir que tem endereços IP suficientes para acomodar quaisquer requisitos de configuração de gateway futuros.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Criar um gateway de rede virtual

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Criar um gateway de rede local
O gateway de rede local refere-se à sua localização no local. As definições que especificou para o gateway de rede local determinam os espaços de endereços que são encaminhados para o seu dispositivo VPN no local.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Configurar o dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Criar uma ligação de Rede de VPNs

Neste passo, irá criar a ligação de VPNs de Site a Site entre o gateway de rede virtual e o dispositivo VPN no local. Antes de iniciar esta secção, verifique se foi concluída a criação do gateway de rede virtual e dos gateways de rede local.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verificar a ligação VPN

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes
*  Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).


