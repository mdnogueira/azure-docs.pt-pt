<properties 
   pageTitle="Acerca da conetividade segura em vários locais para as redes virtuais | Microsoft Azure"
   description="Saiba mais sobre os tipos de ligações seguras em vários locais para as redes virtuais, incluindo ligações Site a Site, Ponto a Site e ExpressRoute."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Acerca da conetividade segura em vários locais para as redes virtuais

Este artigo aborda as diferentes formas de ligar o seu site no local a uma rede virtual do Azure. Este artigo aplica-se aos modelos de implementação clássica e Resource Manager. Se estiver à procura de diagramas de ligação do VPN Gateway, veja [Topologias de ligação do VPN Gateway do Azure](vpn-gateway-topology.md).

Há três opções de ligação disponíveis: Site a Site, Ponto a Site e ExpressRoute. A opção que escolher pode depender de uma variedade de considerações, tais como:


- Que tipo de débito requer a sua solução?
- Pretende comunicar através da Internet pública via VPN segura ou através de uma ligação privada?
- Tem um endereço IP público disponível para utilização?
- Está a planear utilizar um dispositivo VPN? Se assim for, é compatível?
- Está a ligar apenas alguns computadores ou pretende uma ligação persistente para o seu site?
- Que tipo de gateway de VPN é necessário para a solução que pretende criar?

A tabela abaixo pode ajudá-lo a decidir a melhor opção de conectividade para a sua solução.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]                                                                    

## Ligações Site a Site

Uma Rede de VPNs permite-lhe criar uma ligação segura entre o seu site no local e a sua rede virtual. Para criar uma ligação Site a Site, deve configurar um dispositivo VPN localizado na sua rede no local para criar uma ligação segura ao VPN Gateway do Azure. Após criar a ligação, os recursos na rede local e os recursos localizados na rede virtual podem comunicar diretamente e de forma segura. As ligações Site a Site não exigem que estabeleça uma ligação separada para que cada computador cliente na sua rede local possa aceder aos recursos na rede virtual.

**Utilize uma ligação Site a Site quando:**

- Pretender criar uma solução híbrida.
- Pretender uma ligação entre a localização no local e a rede virtual sem necessidade de configurações do lado do cliente.
- Pretender uma ligação persistente. 

**Requisitos**

- O dispositivo VPN no local tem de ter um endereço IP IPv4 com acesso à Internet. Não pode estar atrás de um NAT.
- Tem de ter um dispositivo VPN compatível. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- O dispositivo VPN que utilizar tem de ser compatível com o tipo de gateway necessário para a sua solução. Veja [Acerca do VPN Gateway](vpn-gateway-about-vpngateways.md).
- O SKU de Gateway afetará também o débito agregado. Veja [SKUs de Gateway](vpn-gateway-about-vpngateways.md#gwsku) para obter mais informações. 

**Métodos e modelos de implementação disponíveis para S2S**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 


## Ligações Ponto a Site

Uma VPN Ponto a Site permite-lhe também criar uma ligação segura à sua rede virtual. Numa configuração Ponto a Site, a ligação é configurada individualmente em cada computador cliente que pretende ligar à rede virtual. As ligações Ponto a Site não necessitam de dispositivos VPN. Este tipo de ligação utiliza um cliente VPN que deverá instalar em cada computador cliente. A VPN é estabelecida iniciando manualmente a ligação no computador cliente no local.

As configurações Site a Site e Ponto a Site podem existir em simultâneo, mas, ao contrário das ligações Site a Site, as ligações Ponto a Site não podem ser configuradas em simultâneo com uma ligação ExpressRoute à mesma rede virtual.

**Utilize uma ligação Ponto a Site quando:**

- Pretender apenas configurar alguns clientes para se ligarem a uma rede virtual.

- Pretender ligar à sua rede virtual a partir de uma localização remota. Por exemplo, ligar a partir de um café ou de uma conferência.

- Tem uma ligação Site a Site, mas tem alguns clientes que precisam de ligar a partir de uma localização remota.

- Não tem acesso a um dispositivo VPN que cumpra os requisitos mínimos para uma ligação Site a Site.

- Não tem um endereço IP IPv4 com acesso à Internet para o dispositivo VPN.

**Métodos e modelos de implementação disponíveis para P2S**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## Ligações ExpressRoute

O ExpressRoute do Azure permite-lhe criar ligações privadas entre os datacenters do Azure e a infraestrutura no local ou num ambiente de colocalização. As ligações ExpressRoute não passam pela Internet pública e oferecem mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança em relação às ligações típicas através da Internet.

Em determinados casos, a utilização de ligações ExpressRoute para transferir dados entre o local e o Azure pode também ter vantagens significativas a nível dos custos. Com o ExpressRoute, pode estabelecer ligações ao Azure numa localização ExpressRoute (instalação do Fornecedor Exchange) ou ligar diretamente ao Azure a partir da rede WAN existente (por exemplo, uma VPN MPLS) fornecida por um fornecedor de serviços de rede.

Para obter mais informações sobre o ExpressRoute, veja a [Descrição Geral Técnica](../expressroute/expressroute-introduction.md) do ExpressRoute.


## Passos seguintes

- Para obter mais informações sobre o VPN Gateway, veja os artigos [Acerca do VPN Gateway](vpn-gateway-about-vpngateways.md), [FAQ do VPN Gateway](vpn-gateway-vpn-faq.md) e [Planeamento e Design](vpn-gateway-plan-design.md).

- Para mais informações sobre o ExpressRoute, veja a [Descrição Geral Técnica](../expressroute/expressroute-introduction.md), as [FAQ](../expressroute/expressroute-faqs.md) e os [Fluxos de Trabalho](../expressroute/expressroute-workflows.md) do ExpressRoute.







<!--HONumber=Jun16_HO2-->


