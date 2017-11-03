---
title: Ligar a sua rede no local ao Azure | Microsoft Docs
description: "Explica e compara os diferentes métodos disponíveis para ligar aos serviços em nuvem da Microsoft, tais como o Azure, Office 365 e o Dynamics CRM Online."
services: 
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: 
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Ligar a sua rede no local para o Azure
A Microsoft fornece vários tipos de serviços em nuvem. Enquanto pode ligar a todos os serviços através da Internet pública, também pode ligar para alguns dos serviços através de um túnel de rede privada virtual (VPN) através da Internet ou através de uma ligação direta, privada para a Microsoft. Este artigo ajuda-o a determinar que opção de conectividade melhor satisfaz as necessidades com base nos tipos de cloud services da Microsoft que consumir. A maioria das organizações utilizar vários tipos de ligação descritos abaixo.

## <a name="connecting-over-the-public-internet"></a>Ligar através da Internet pública
Este tipo de ligação fornece acesso aos serviços em nuvem da Microsoft diretamente através da Internet, conforme mostrado abaixo.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Esta ligação é, geralmente, o primeiro tipo utilizado para ligar ao cloud services da Microsoft. A tabela abaixo lista os profissionais de TI e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Requer sem modificações à sua rede no local, desde que todos os dispositivos de cliente tem acesso ilimitado a todos os endereços IP e portas na Internet. |Apesar de tráfego, muitas vezes, é encriptado através de HTTPS, este pode ser intercetado em trânsito, uma vez que atravessa-lo da Internet pública. |
| Pode ligar a todos os cloud services da Microsoft expostos à Internet pública. |Latência imprevisível porque a ligação atravessa da Internet. |
| Utiliza a ligação à Internet existente. | |
| Não necessita de gestão de dispositivos qualquer conectividade. | |

Esta ligação não tem conectividade ou os custos de largura de banda, uma vez que utilize a ligação à Internet existente.

## <a name="connecting-with-a-point-to-site-connection"></a>Ligar com uma ligação ponto a site
Este tipo de ligação fornece acesso a alguns serviços de nuvem da Microsoft através de um túnel Secure Socket Tunneling Protocol (SSTP) através da Internet, conforme mostrado abaixo.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "ligaçãoponto a site")

A ligação é efetuada através da ligação à Internet existente, mas requer a utilização de um Gateway de VPN do Azure. A tabela abaixo lista os profissionais de TI e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Requer sem modificações à sua rede no local, desde que todos os dispositivos de cliente tem acesso ilimitado a todos os endereços IP e portas na Internet. |Apesar do tráfego é encriptado utilizando o IPSec, este pode ser intercetado em trânsito, uma vez que atravessa-lo da Internet pública. |
| Utiliza a ligação à Internet existente. |Latência imprevisível porque a ligação atravessa da Internet. |
| Débito até 200 Mb/s por gateway. |Requer a criação e gestão de separado ligações entre cada dispositivo na sua rede no local e cada gateway que tem de ligar a cada dispositivo. |
| Pode ser utilizado para ligar aos serviços do Azure que podem ser ligados a um redes virtuais do Azure (VNet), tais como a Azure Virtual Machines e Cloud Services do Azure. |Requer uma administração mínima em curso de um Gateway de VPN do Azure. |
| Não pode ser utilizado para ligar ao Microsoft Office 365 ou o Dynamics CRM Online. | |
| Não pode ser utilizado para ligar aos serviços do Azure que não podem ser ligados a uma VNet. | |

Saiba mais sobre o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) serviço, o [preços](https://azure.microsoft.com/pricing/details/vpn-gateway)e a transferência de dados de saída [preços](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Ligar com uma ligação site a site
Este tipo de ligação fornece acesso a alguns serviços de nuvem da Microsoft através de um túnel IPSec através da Internet, conforme mostrado abaixo.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "ligação Site a site")

A ligação é efetuada através da ligação à Internet existente, mas requer a utilização de um Gateway de VPN do Azure com o respetivo preços associados e a transferência de dados de saída de preço. A tabela abaixo lista os profissionais de TI e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Todos os dispositivos na sua rede no local podem comunicar com serviços do Azure ligados a uma VNet, pelo que não é necessário para configurar ligações individuais para cada dispositivo. |Apesar do tráfego é encriptado utilizando o IPSec, este pode ser intercetado em trânsito, uma vez que atravessa-lo da Internet pública. |
| Utiliza a ligação à Internet existente. |Latência imprevisível porque a ligação atravessa da Internet. |
| Pode ser utilizado para ligar a serviços do Azure que podem ser ligados a uma VNet, tais como máquinas virtuais e serviços em nuvem. |Tem de configurar e gerir um validados VPN dispositivo * no local. |
| Débito até 200 Mb/s por gateway. |Requer uma administração mínima em curso de um Gateway de VPN do Azure. |
| Pode forçar iniciado a partir de máquinas virtuais na nuvem através da rede no local para o registo a utilizar rotas definidas pelo utilizador ou o Border Gateway Protocol (BGP) e de inspeção de tráfego de saída * *. |Não pode ser utilizado para ligar ao Microsoft Office 365 ou o Dynamics CRM Online. |
| Não pode ser utilizado para ligar aos serviços do Azure que não podem ser ligados a uma VNet. | |
| Se utilizar os serviços que iniciam ligações para dispositivos no local e requerem que as políticas de segurança, poderá ter uma firewall entre a rede no local e o Azure. | |

* * Ver uma lista de [validado dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* * * Saiba mais sobre como utilizar [rotas definidas pelo utilizador](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) para forçar o encaminhamento das VNets do Azure para um dispositivo no local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Ligar com uma ligação privada dedicada
Este tipo de ligação fornece acesso a todos os serviços de nuvem da Microsoft através de uma ligação privada dedicada para a Microsoft não atravessar da Internet, conforme mostrado abaixo.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ligação do ExpressRoute")

A ligação requer a utilização do serviço ExpressRoute e uma ligação a um fornecedor de conectividade. A tabela abaixo lista os profissionais de TI e contras deste tipo de ligação.

| **Benefícios** | **Considerações** |
| --- | --- |
| Tráfego não pode ser intercetado em trânsito através da Internet pública, uma vez que é utilizada uma ligação dedicada através de um fornecedor de serviços. |Requer a gestão de router no local. |
| A largura de banda até 10 Gb/s por circuito do ExpressRoute e débito até 2 Gb/s para cada gateway. |Requer uma ligação dedicada para um fornecedor de conectividade. |
| Latência previsível porque se trata de uma ligação dedicada para a Microsoft não atravessar da Internet. |Pode exigir uma administração mínima em curso de um ou vários Gateways de VPN do Azure (se ligar o circuito a VNets). |
| Não necessita de comunicação encriptada, embora pode encriptar o tráfego, se assim o desejar. |Se estiver a utilizar serviços em nuvem que iniciam ligações para dispositivos no local, terá uma firewall entre a rede no local e o Azure. |
| Pode ligar diretamente a todos os cloud services da Microsoft, com algumas exceções *. |Necessita de tradução de endereços de rede (NAT) de endereços IP local introduzir centros de dados Microsoft para os serviços que não podem ser ligados a um VNet.* * |
| Pode forçar o tráfego de saída iniciado a partir de máquinas virtuais na nuvem através da rede no local para inspeção e registo utilizando o BGP. | |

* * Ver um [lista de serviços](../expressroute/expressroute-faqs.md#supported-services) que não pode ser utilizado com o ExpressRoute. Deve ser aprovada a sua subscrição do Azure para ligar ao Office 365.  Consulte o [Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) artigo para obter detalhes.
* * * Saiba mais sobre o ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisitos.

Saiba mais sobre [ExpressRoute](../expressroute/expressroute-introduction.md), associado [preços](https://azure.microsoft.com/pricing/details/expressroute), e [fornecedores de conectividade](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Considerações adicionais
* Tem várias opções acima várias os limites máximos que podem suportar para ligações VNet, ligações de gateway e outros critérios. É recomendado que reveja o Azure [limites de rede](../azure-subscription-service-limits.md#networking-limits) compreender se qualquer um deles impacto nos tipos de conectividade optar por utilizar.
* Se planeia ligar um gateway a partir de uma ligação de VPN de site a site para a mesma VNet como um gateway do ExpressRoute, pode deve familiarizar-se com limitações importantes primeiro. Consulte o [Site a Site e ExpressRoute de configurar ligações coexistentes](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) artigo para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Os recursos abaixo explicam como implementar os tipos de ligação abordados neste artigo.

* [Implementar uma ligação ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementar uma ligação site a site](guidance-hybrid-network-vpn.md)
* [Implementar uma ligação privada dedicada](guidance-hybrid-network-expressroute.md)
* [Implementar uma ligação privada dedicada com uma ligação site a site para elevada disponibilidade](guidance-hybrid-network-expressroute-vpn-failover.md)
