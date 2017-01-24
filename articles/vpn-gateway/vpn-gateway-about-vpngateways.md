---
title: Acerca do Gateway de VPN do Azure | Microsoft Docs
description: "Saiba mais sobre as ligações do Gateway de VPN das Redes Virtuais do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f74d531006a2c2cc4b12aac846c20c30317cc013


---
# <a name="about-vpn-gateway"></a>Acerca do VPN Gateway
Um gateway de rede virtual é utilizado para enviar tráfego de rede entre redes virtuais do Azure e localizações no local, assim como entre redes virtuais dentro do Azure (VNet a VNet). Se configurar um gateway de VPN, tem de criar e configurar um gateway de rede virtual e uma ligação ao mesmo.

No modelo de implementação Resource Manager, quando cria um recurso de gateway de rede virtual, são especificadas várias definições. Uma das definições necessárias é "-GatewayType". Existem dois tipos de gateway de rede virtual: Vpn e ExpressRoute. 

Quando o tráfego de rede é enviado numa ligação privada dedicada, está a utilizar o tipo de gateway "ExpressRoute". Também se refere a este como um gateway ExpressRoute. Quando o tráfego de rede é enviado encriptado através de uma ligação pública, está a utilizar o tipo de gateway "Vpn". Isto é referido como um gateway de VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType ExpressRoute e outro que utilize -GatewayType Vpn. Este artigo centra-se, principalmente, no Gateway de VPN. Para obter mais informações sobre ExpressRoute, veja a [Descrição Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Preços
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>SKUs de gateway
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para obter mais informações sobre os SKUs de gateway para o Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Débito agregado estimado por SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>Configurar um Gateway de VPN
Ao configurar um gateway de VPN, as instruções que utilizar dependem do modelo de implementação que utilizou para criar a rede virtual. Por exemplo, se tiver criado a VNet com o modelo de implementação clássica, utilize as diretrizes e as instruções do modelo de implementação clássica para criar e configurar as definições do gateway de VPN. Para mais informações sobre os modelos de implementação, consulte [Compreender o Gestor de Recursos e modelos de implementação clássicos](../azure-resource-manager/resource-manager-deployment-model.md).

Uma ligação de gateway de VPN depende de vários recursos que estão configurados com definições específicas. A maior parte dos recursos podem ser configurados em separado, embora estes, em alguns casos, devam ser configurados numa determinada ordem. Pode começar por criar e configurar recursos utilizando uma ferramenta de configuração, como o portal do Azure. Mais tarde, em seguida, pode decidir mudar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou para modificar os recursos existentes, quando aplicável. Atualmente, não pode configurar todos os recursos e definições de recursos no portal do Azure. As instruções nos artigos para cada topologia de ligação especificam quando uma ferramenta de configuração especifica é necessária. Para obter informações sobre recursos individuais e sobre definições para o Gateway de VPN, consulte [definições sobre o Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).

As secções abaixo contêm tabelas que listam:

* modelos de implementação disponíveis
* ferramentas de configuração disponíveis
* ligações que o levam diretamente para um artigo, se disponível

Utilize os diagramas e as descrições para ajudar a selecionar a topologia de ligação para corresponder aos requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar configurações mais complexas utilizando os diagramas como orientação.

## <a name="site-to-site-and-multi-site"></a>Site a Site e Multilocal
### <a name="site-to-site"></a>Site a Site
Uma ligação de gateway de VP de Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído ao mesmo e não se encontre protegido por um NAT. As ligações S2S podem ser utilizadas para configurações em vários locais e híbridas.   

![Ligação S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "site para site")

### <a name="multi-site"></a>Multilocal
Pode criar e configurar uma ligação de gateway de VPN entre a sua VNet e várias redes locais. Quando trabalha com várias ligações, tem de utilizar um tipo de VPN Baseado em Rota (gateway dinâmico para VNets clássicas). Uma vez que uma VNet só pode ter um gateway de VPN, todas as ligações do gateway partilham a largura de banda disponível. Isto é frequentemente designado como ligação “multilocal”.

![Ligação multilocal](./media/vpn-gateway-about-vpngateways/demomulti.png "multilocal")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Métodos de implementação e métodos de Site para Site e Multilocal
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnet-to-vnet"></a>VNet a VNet
A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações de ligação multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

As VNets que liga podem estar:

* nas mesmas regiões ou em diferentes
* nas mesmas subscrições ou em diferentes 
* nos mesmos modelos de implementação ou em diferentes

![Ligação VNet a VNet](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet a vnet")

#### <a name="connections-between-deployment-models"></a>Ligações entre modelos de implementação
Atualmente, o Azure tem dois modelos de implementação: clássico e Resource Manager. Se tem utilizado o Azure durante algum tempo, provavelmente as VMs do Azure e as funções de instância estão em execução numa VNet clássica. As VMs mais recentes e as instâncias da função poderão estar em execução numa VNet criada no Resource Manager. Pode criar uma ligação entre as VNets para permitir que os recursos numa VNet comuniquem diretamente com recursos de outra.

#### <a name="vnet-peering"></a>VNet peering
Poderá utilizar o VNet peering para criar a ligação, desde que a rede virtual cumpra determinados requisitos. O VNet peering não utiliza um gateway de rede virtual. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementação e métodos para VNet para VNet
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="point-to-site"></a>Ponto a Site
Uma ligação de gateway de VPN de Ponto a Site (P2S) permite-lhe criar uma ligação segura para a sua rede virtual a partir de um computador cliente individual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol). As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público para funcionar. O utilizador estabelece a ligação VPN iniciando-a a partir do computador cliente. Esta solução é útil quando pretende ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma VNet. As ligações P2S podem ser utilizadas em conjunto com as ligações S2S através do mesmo gateway de VPN, desde que todos os requisitos de configuração para ambas as ligações sejam compatíveis.

![Ligação ponto a site](./media/vpn-gateway-about-vpngateways/demop2s.png "ponto a site")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Métodos e modelos de implementação de Ponto para Site
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>ExpressRoute
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Numa ligação ExpressRoute, um gateway de rede virtual está configurado com o tipo de gateway "ExpressRoute", em vez de "Vpn". Para obter mais informações sobre o ExpressRoute, veja a [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Ligações Site a Site e ExpressRoute coexistentes
O ExpressRoute é uma ligação dedicada direta a partir da WAN (e não através da Internet pública) aos Serviços Microsoft, incluindo ao Azure. O tráfego da Rede de VPNs circula de forma encriptada através da Internet pública. Poder configurar ligações ExpressRoute e de Rede de VPNs para a mesma rede virtual tem várias vantagens.

Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que não fazem parte da sua rede, mas que se encontram ligados através do ExpressRoute. Tenha em atenção que isto requer dois gateways de rede virtual para a mesma rede virtual, um que utilize -GatewayType Vpn e o outro -GatewayType ExpressRoute.

![Ligação coexistente](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Métodos e modelos de implementação para S2S e para ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>Passos seguintes
Planeie a configuração do gateway de VPN. Consulte [Design e Planeamento de Gateway de VPN](vpn-gateway-plan-design.md) e [Ligar a rede no local ao Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).




<!--HONumber=Jan17_HO1-->


