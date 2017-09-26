---
title: "Descrição Geral do Gateway de VPN: Criar ligações VPN entre locais para redes virtuais do Azure | Microsoft Docs"
description: "Esta Descrição Geral do Gateway de VPN explica as formas de ligar às redes virtuais do Azure através de uma ligação VPN pela Internet. Estão incluídos diagramas de configurações de ligação básicas."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 9c1e4199132af737fb7af628aaa7952a5c90818d
ms.contentlocale: pt-pt
ms.lasthandoff: 09/22/2017

---
# <a name="about-vpn-gateway"></a>Acerca do VPN Gateway

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego encriptado através de uma ligação pública para uma localização no local. Também pode utilizar gateways de VPN para enviar tráfego encriptado entre as redes virtuais do Azure através da rede da Microsoft. Para enviar tráfego de rede encriptado entre a sua rede virtual do Azure e o seu site no local, tem de criar um gateway de VPN para a sua rede virtual.

Cada rede virtual apenas pode ter um gateway de VPN. No entanto, pode criar várias ligações ao mesmo gateway de VPN. Um exemplo disto é uma configuração de ligação multilocal. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN, incluindo as VPNs Ponto a Site, partilham a largura de banda que está disponível para o gateway.

### <a name="whatis"></a>O que é um gateway de rede virtual?

Um gateway de rede virtual é composto por duas ou mais máquinas virtuais implementadas numa sub-rede específica denominada GatewaySubnet. As VMs localizadas no GatewaySubnet são criadas quando criar o gateway de rede virtual. As VMs do gateway de rede virtual são configuradas para conter tabelas de encaminhamento e serviços de gateway específicos do gateway. Não pode configurar diretamente as VMs que fazem parte do gateway de rede virtual e nunca deve implementar recursos adicionais no GatewaySubnet.

Quando criar um gateway de rede virtual com o tipo de gateway 'Vpn', é criado um tipo específico de gateway de rede virtual que encripta o tráfego: um gateway de VPN. Um gateway de VPN pode demorar até 45 minutos a ser criado. Isto deve-se ao facto de as VMs do gateway de VPN estarem a ser implementadas no GatewaySubnet e configuradas com as definições que especificou. O SKU de Gateway que selecionar determina o poder de processamento das VMs.

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

## <a name="configuring"></a>Configurar um Gateway de VPN

Uma ligação de gateway de VPN depende de vários recursos que estão configurados com definições específicas. A maior parte dos recursos podem ser configurados em separado, embora estes, em alguns casos, devam ser configurados numa determinada ordem.

### <a name="settings"></a>Definições

As definições que escolheu para cada recurso são essenciais para a criação de uma ligação com êxito. Para obter informações sobre recursos individuais e sobre definições para o Gateway de VPN, consulte [definições sobre o Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md). O artigo contém informações para ajudar a compreender os tipos de gateways, os tipos de VPN, os tipos de ligação, as sub-redes de gateways, os gateways de rede local e várias outras definições de recursos que poderá querer considerar.

### <a name="tools"></a>Ferramentas de implementação

Pode começar por criar e configurar recursos utilizando uma ferramenta de configuração, como o portal do Azure. Mais tarde, pode decidir mudar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não pode configurar todos os recursos e definições de recursos no portal do Azure. As instruções nos artigos para cada topologia de ligação especificam quando uma ferramenta de configuração especifica é necessária. 

### <a name="models"></a>Modelo de implementação

Ao configurar um gateway de VPN, os passos seguidos dependem do modelo de implementação que utilizou para criar a rede virtual. Por exemplo, se tiver criado a VNet com o modelo de implementação clássica, utilize as diretrizes e as instruções do modelo de implementação clássica para criar e configurar as definições do gateway de VPN. Para mais informações sobre os modelos de implementação, consulte [Compreender o Gestor de Recursos e modelos de implementação clássicos](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="diagrams"></a>Diagramas de topologia de ligação

É importante saber que existem configurações diferentes disponíveis para as ligações de gateway VPN. Deve determinar qual das configurações se adequa melhor às suas necessidades. Nas secções abaixo, pode ver informações e diagramas de topologias relativos às ligações de gateways de VPN seguintes. As secções seguintes contêm tabelas que listam:

* O modelo de implementação disponível
* As ferramentas de configuração disponíveis
* Ligações que o levam diretamente para um artigo, se disponível

Utilize os diagramas e as descrições para ajudar a selecionar a topologia de ligação para corresponder aos requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar configurações mais complexas utilizando os diagramas como orientação.

## <a name="s2smulti"></a>Rede Site a Site e Multilocal (túnel VPN IPsec/IKE)

### <a name="S2S"></a>Rede de VPN

Uma ligação de gateway de VP de Site a Site (S2S) é uma ligação através do túnel VPN IPsec/IKE (S2S ou IKEv1). A ligação S2S requer um dispositivo VPN localizado no local que tenha um endereço IP público atribuído ao mesmo e não se encontre protegido por um NAT. As ligações S2S podem ser utilizadas para configurações em vários locais e híbridas.   

![Exemplo de ligação Site a Site de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Multilocal

Este tipo de ligação é uma variação da ligação Rede de VPNs. Cria mais de uma ligação de VPN a partir do gateway de rede virtual, ligando, geralmente, a vários sites no local. Quando trabalha com várias ligações, tem de utilizar um tipo de VPN RouteBased (conhecido como gateway dinâmico ao trabalhar com VNets clássicas). Uma vez que cada rede virtual só pode ter um gateway de VPN, todas as ligações através do mesmo partilham a largura de banda disponível. Isto é frequentemente designado como ligação “multilocal”.

![Exemplo de ligação Multilocal de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Métodos de implementação e métodos de Site para Site e Multilocal

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Ponto a Site (VPN por IKEv2 ou SSTP)

Uma ligação de gateway de VPN Ponto a Site (P2S) permite-lhe criar uma ligação segura à sua rede virtual a partir de um computador cliente individual. É estabelecida uma ligação P2S ao iniciá-la a partir do computador cliente. Esta solução é útil para as pessoas que trabalham à distância que queiram ligar às VNets do Azure a partir de uma localização remota, como, por exemplo, a partir de casa ou de uma conferência. Uma VPN P2S também é uma solução útil para utilizar em vez de uma VPN S2S, quando são poucos os clientes que precisam de ligar a uma VNet.

Ao contrário das ligações S2S, as ligações P2S não requerem um endereço IP destinado ao público ou um dispositivo VPN no local. As ligações P2S podem ser utilizadas com as ligações S2S através do mesmo gateway de VPN, desde que todos os requisitos de configuração para ambas as ligações sejam compatíveis.

Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).

![Exemplo de ligação Ponto a Site de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/point-to-site.png)

## <a name="V2V"></a>Ligações VNet a VNet (túnel VPN IPsec/IKE)

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode, inclusive, combinar uma comunicação VNet a VNet com configurações de ligação multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

As VNets que liga podem estar:

* nas mesmas regiões ou em diferentes
* nas mesmas subscrições ou em diferentes 
* nos mesmos modelos de implementação ou em diferentes

![Exemplo de ligação VNet a VNet de Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Ligações entre modelos de implementação

Atualmente, o Azure tem dois modelos de implementação: clássico e Resource Manager. Se tem utilizado o Azure durante algum tempo, provavelmente as VMs do Azure e as funções de instância estão em execução numa VNet clássica. As VMs mais recentes e as instâncias da função poderão estar em execução numa VNet criada no Resource Manager. Pode criar uma ligação entre as VNets para permitir que os recursos numa VNet comuniquem diretamente com recursos de outra.

### <a name="vnet-peering"></a>VNet peering

Poderá utilizar o VNet peering para criar a ligação, desde que a rede virtual cumpra determinados requisitos. O VNet peering não utiliza um gateway de rede virtual. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementação e métodos para VNet para VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (ligação privada dedicada)

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta.

As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

As ligações ExpressRoute não utilizam um gateway de VPN, embora utilizem um gateway de rede virtual como parte da configuração necessária. Numa ligação ExpressRoute, o gateway de rede virtual está configurado com o tipo de gateway "ExpressRoute", em vez de "Vpn". Para obter mais informações sobre o ExpressRoute, veja a [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Ligações Site a Site e ExpressRoute coexistentes

O ExpressRoute é uma ligação dedicada direta a partir da WAN (e não através da Internet pública) aos Serviços Microsoft, incluindo ao Azure. O tráfego da Rede de VPNs circula de forma encriptada através da Internet pública. Poder configurar ligações ExpressRoute e de Rede de VPNs para a mesma rede virtual tem várias vantagens.

Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar a Rede de VPNs para ligar a sites que não fazem parte da sua rede, mas que se encontram ligados através do ExpressRoute. Repare que esta configuração requer dois gateways de rede virtual para a mesma rede virtual, um que utilize o tipo de gateway “Vpn” e o outro o tipo de gateway “ExpressRoute”.

![Exemplo de ligações coexistentes do ExpressRoute e Gateway de VPN](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Métodos e modelos de implementação para S2S e para ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Preços

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Para obter mais informações sobre os SKUs de gateway para o Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a>FAQ

Para perguntas mais frequentes sobre o Gateway de Aplicação, veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Passos seguintes

- Planeie a configuração do gateway de VPN. Veja [VPN Gateway Planning and Design (Planear e Conceber o Gateway de VPN)](vpn-gateway-plan-design.md)
- Veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações.
- Veja [Subscription and service limits (Subscrições e limites do serviço)](../azure-subscription-service-limits.md#networking-limits).
- Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
