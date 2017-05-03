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
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: e839f99d599d682c31bd4c6d03a6a58268f05b7c
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Criar uma ligação Site a Site no portal do Azure

Este artigo mostra-lhe como utilizar o portal do Azure para criar uma ligação de gateway de VPN de Site para Site a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Clássica – portal do Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássica – portal clássico](vpn-gateway-site-to-site-create.md)
> 
>

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Certifique-se de que pretende trabalhar com o modelo de implementação Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Um dispositivo VPN compatível e alguém que consiga configurá-lo. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Um endereço IP IPv4 público com acesso exterior para o dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar. 

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

## <a name="gatewaysubnet"></a>3. Criar a sub-rede de gateway

O gateway da rede virtual utiliza uma sub-rede do gateway que contém os endereços IP que os serviços do gateway de VPN utilizam. Quando cria uma sub-rede de gateway, esta tem de ter o nome “GatewaySubnet”. Se lhe der outro nome, a configuração da ligação falha.

O tamanho da sub-rede de gateway que especificar depende da configuração do gateway de VPN que pretende criar. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando /27 ou /28. O tamanho da sub-rede de gateway que especificar depende da configuração do gateway de VPN que pretende criar. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior que inclui mais endereços selecionando /27 ou /28. Utilizar uma sub-rede de gateway maior permite que os endereços IP suficientes suportem possíveis configurações futuras.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4. Criar o gateway de VPN

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Criar o gateway de rede local

O gateway de rede local refere-se normalmente à sua localização no local. Dê um nome ao site pelo qual o Azure se possa referir ao mesmo e especifique o endereço IP do dispositivo VPN no local para o qual vai criar uma ligação. Também pode especificar os prefixos do endereço IP que vai ser encaminhado através do gateway de VPN para o dispositivo VPN. Os prefixos do endereço que especificar são os que estão localizados na sua rede no local. Se a rede no local se alterar, pode atualizar facilmente os prefixos.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Configurar o dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

Para encontrar o endereço IP Público do seu gateway de VPN através do portal do Azure, navegue para **Gateways de rede virtual** e, em seguida, clique no nome do gateway.

## <a name="CreateConnection"></a>7. Criar a ligação VPN

Crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN no local.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verificar a ligação VPN

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes
*  Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).


