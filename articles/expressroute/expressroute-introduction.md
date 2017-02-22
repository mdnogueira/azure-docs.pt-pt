---
title: "Introdução ao ExpressRoute | Microsoft Docs"
description: "Esta página fornece uma descrição geral do serviço ExpressRoute, incluindo como funciona uma ligação ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 57b95d5018fa197096992f7cd255a54501875dea


---
# <a name="expressroute-technical-overview"></a>Descrição geral técnica do ExpressRoute
O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta. As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Os principais benefícios incluem:**

* A conectividade da Camada 3 entre a sua rede no local e o Microsoft Cloud através de um fornecedor de conectividade. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma ligação Ethernet ponto a ponto ou através de uma ligação cruzada virtual por meio de uma troca de Ethernet.
* Conectividade de serviços em nuvem da Microsoft em todas as regiões na região geopolítica.
* Conectividade global com os serviços da Microsoft em todas as regiões com o suplemento ExpressRoute Premium.
* Encaminhamento dinâmico entre a sua rede e a Microsoft através de protocolos standard da indústria (BGP).
* Redundância incorporada em cada localização de peering para maior fiabilidade.
* Tempo de atividade da ligação [SLA](https://azure.microsoft.com/support/legal/sla/).
* QoS e suporte para várias classes do serviço para aplicações especiais, tal como o Skype para Empresas.

Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.

## <a name="a-namehowtoconnectahow-can-i-connect-my-network-to-microsoft-using-expressroute"></a><a name="howtoconnect"></a>Como ligar a minha rede à Microsoft através do ExpressRoute?
Pode criar uma ligação entre a sua rede no local e a nuvem da Microsoft de três formas diferentes:

### <a name="co-located-at-a-cloud-exchange"></a>Localizado conjuntamente numa troca de nuvem
Se estiver localizado conjuntamente num local com uma troca de nuvem, pode organizar ligações cruzadas virtuais para a nuvem da Microsoft através da troca de Ethernet do fornecedor de localização conjunta. Os fornecedores de localização conjunta podem oferecer ligações cruzadas de Camada 2 ou ligações cruzadas geridas de Camada 3 entre a sua infraestrutura na localização conjunto e a nuvem da Microsoft.

### <a name="point-to-point-ethernet-connections"></a>Ligações de Ethernet ponto a ponto
Pode ligar os seus centros de dados/escritórios no local à nuvem da Microsoft através de ligações de Ethernet ponto a ponto. Os fornecedores de Ethernet ponto a ponto podem oferecer ligações de Camada 2 ou ligações geridas de Camada 3 entre o seu site e a nuvem da Microsoft.

### <a name="any-to-any-ipvpn-networks"></a>Redes qualquer a qualquer (VPN de IP)
Pode integrar a sua WAN com a nuvem da Microsoft. Os fornecedores de VPN de IP (normalmente VPN de MPLS) oferecem uma conectividade qualquer a qualquer entre as suas sucursais e os centros de dados. A nuvem da Microsoft pode ser interligada à sua WAN para torná-la mais semelhante a qualquer outra sucursal. Os fornecedores de WAN oferecem normalmente uma conectividade gerida de Camada 3. As funcionalidades e capacidades do ExpressRoute são idênticas em todos os modelos de conetividade acima. 

Os fornecedores de conectividade podem oferecer um ou mais modelos de conetividade. Pode trabalhar com o seu fornecedor de conectividade para escolher o modelo que funciona melhor para si.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)

## <a name="expressroute-features"></a>Funcionalidades do ExpressRoute
O ExpressRoute suporta as seguintes funcionalidades e capacidades: 

### <a name="layer-3-connectivity"></a>Conectividade de Camada 3
A Microsoft utiliza um protocolo de encaminhamento dinâmico (BGP) padrão da indústria para trocar rotas entre a sua rede no local, as instâncias no Azure e os endereços públicos da Microsoft.  Estabelecemos várias sessões BGP com a sua rede para diferentes perfis de tráfego. Pode obter mais detalhes no artigo [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).

### <a name="redundancy"></a>Redundância
Cada circuito ExpressRoute é composto por duas ligações para dois routers de limite da Microsoft Enterprise (MSEEs) a partir do fornecedor de conectividade/o seu limite de rede. A Microsoft irá necessitar de uma ligação BGP dupla a partir do fornecedor de conectividade/o seu lado – um para cada MSEE. Pode optar por não implementar dispositivos redundantes/Circuitos Ethernet na sua parte. No entanto, os fornecedores de conectividade utilizam dispositivos redundantes para verificar que as ligações são entregues à Microsoft de forma redundante. Uma configuração de conectividade redundante de Camada 3 é um requisito para que o nosso [SLA](https://azure.microsoft.com/support/legal/sla/) seja válido. 

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade com serviços em nuvem da Microsoft
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

As ligações ExpressRoute permitem o acesso aos seguintes serviços:

* Serviços do Microsoft Azure
* Serviços do Microsoft Office 365
* Serviços online do Microsoft CRM 

Pode visitar a página [FAQ do ExpressRoute](expressroute-faqs.md) para uma lista detalhada dos serviços suportada através do ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade a todas as regiões numa região geopolítica
Pode ligar-se à Microsoft a partir de uma das nossas [localizações de peering](expressroute-locations.md) e ter acesso a todas as regiões numa região geopolítica. 

Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute, terá acesso a todos os serviços em nuvem da Microsoft alojados na Europa do Norte e na Europa Ocidental. Veja o artigo [Parceiros ExpressRoute e localizações de peering](expressroute-locations.md) para obter uma descrição geral das regiões geopolíticas, das regiões associadas em nuvem da Microsoft e as localizações de peering do ExpressRoute correspondentes.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Conectividade global com o suplemento do ExpressRoute Premium
Pode ativar a funcionalidade do suplemento ExpressRoute Premium para expandir a conectividade ao longo dos limites geopolíticos. Por exemplo, se estiver ligado à Microsoft em Amesterdão através do ExpressRoute, terá acesso a todos os serviços em nuvem da Microsoft alojados em todas as regiões pelo mundo (exceto nuvens nacionais). Pode aceder aos serviços implementados na América do Sul ou Austrália da mesma forma que acede às regiões da Europa do Norte e Europa Ocidental.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema de parceiro de conectividade avançada
O ExpressRoute tem um crescente ecossistema de fornecedores de conectividade e parceiros de SI. Pode ver o artigo [Fornecedores e localizações do ExpressRoute](expressroute-locations.md) para obter as informações mais recentes.

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais
A Microsoft funciona em ambientes de nuvem isolados para regiões geopolíticas especiais e segmentos de cliente. Veja a página [Fornecedores e localizações do ExpressRoute](expressroute-locations.md) para obter uma lista de nuvens e fornecedores nacionais.

### <a name="supported-bandwidth-options"></a>Opções de largura de banda suportadas
Pode comprar circuitos ExpressRoute para uma vasta gama de larguras de banda. A lista das larguras de banda suportadas é apresentada abaixo. Confirme que consulta o seu fornecedor de conectividade para determinar a lista de larguras de banda suportadas fornecidas.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico da largura de banda
Tem a capacidade de aumentar a largura de banda do circuito ExpressRoute (na base de melhor esforço) sem ter de fechar as suas ligações. 

### <a name="flexible-billing-models"></a>Modelos de faturação flexíveis
Pode escolher um modelo de faturação que funciona melhor para si. Escolha entre os modelos de faturação listados abaixo. Veja a página [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes. 

* **Dados ilimitados**. O circuito ExpressRoute é cobrado com base numa taxa mensal e todas as transferências de dados de entrada e saída estão incluídas de forma gratuita. 
* **Dados limitados**. O circuito ExpressRoute é cobrado com base numa taxa mensal. Todas as transferências de dados de entrada são gratuitas. A transferência de dados de saída é cobrada por GB da transferência de dados. As taxas da transferência de dados variam consoante a região.
* **Suplemento do ExpressRoute Premium**. O ExpressRoute Premium é um suplemento do circuito ExpressRoute. O suplemento do ExpressRoute Premium oferece as seguintes capacidades: 
  * Aumento dos limites de rota para peering público e privado Azure de 4000 rotas para 10 000 rotas.
  * Conectividade global para os serviços. Um circuito ExpressRoute criado em qualquer região (excluindo as nuvens nacionais) terá acesso aos recursos a partir de qualquer outra região do mundo. Por exemplo, uma rede virtual criada na Europa Ocidental pode ser acedida através de um circuito ExpressRoute aprovisionado em Silicon Valley.
  * Aumento do número de ligações VNet por circuito ExpressRoute, de 10 até um limite superior, dependendo da largura de banda do circuito.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre ligações ExpressRoute e domínios de encaminhamento. Veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Encontre um fornecedor de serviços. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Veja os requisitos para [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configure o encaminhamento](expressroute-howto-routing-classic.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Dec16_HO1-->


