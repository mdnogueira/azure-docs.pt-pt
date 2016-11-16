---
title: Requisitos NAT dos circuitos do ExpressRoute | Microsoft Docs
description: "Esta página fornece os requisitos detalhados para configurar e gerir o NAT para circuitos do ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 867bf936-c851-485f-84c8-d8d6e33fee9f
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d16326340f668285e185a6e8adb1a8316d8bd0eb


---
# <a name="expressroute-nat-requirements"></a>Requisitos do NAT do ExpressRoute
Para ligar aos serviços em nuvem da Microsoft com o ExpressRoute, terá de configurar e gerir os NATs. Alguns fornecedores de conectividade oferecem a configuração e a gestão do NAT como um serviço gerido. Contacte o seu fornecedor de conectividade para ver se oferece tal serviço. Se não for possível, terá de cumprir os requisitos descritos abaixo. 

Reveja a página [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md) para obter uma descrição geral de vários domínios de encaminhamento. Para cumprir os requisitos públicos de endereço IP para o Azure público e peering da Microsoft, recomendamos que configure o NAT entre a sua rede e a Microsoft. Esta secção fornece uma descrição detalhada da infraestrutura do NAT que tem de configurar.

## <a name="nat-requirements-for-azure-public-peering"></a>Requisitos do NAT para o peering público Azure
O caminho de peering público Azure permite-lhe ligar a todos os serviços alojados no Azure ao longo dos respetivos endereços IP públicos. Estes incluem os serviços listados nas [FAQ do ExpressRoute](expressroute-faqs.md) e quaisquer serviços alojados pelos ISVs no Microsoft Azure. A conectividade aos serviços do Microsoft Azure em peering público é sempre iniciada a partir da sua rede para a rede da Microsoft. O tráfego destinado ao Microsoft Azure em peering público tem de realizar um SNAT para endereços IPv4 públicos válidos antes de serem introduzidos na rede da Microsoft. A figura abaixo fornece uma imagem de alto nível da forma como o NAT foi configurado para cumprir o requisito acima.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Conjunto IP do NAT e anúncios de rota
Deve garantir que o tráfego está a entrar no caminho de peering público Azure com um endereço IPv4 público válido. A Microsoft deve conseguir validar a propriedade do conjunto de endereços IPv4 NAT num registo de Internet de encaminhamento regional (RIR) ou num registo de encaminhamento de Internet (IRR). Será efetuada uma verificação com base no número AS com o qual está a ser emparelhado e os endereços IP utilizados para o NAT. Veja a página [Requisitos do encaminhamento do ExpressRoute](expressroute-routing.md) para obter informações sobre os registos do encaminhamento.

Não existem restrições ao comprimento do prefixo do IP do NAT anunciado através deste peering. Monitorize o conjunto NAT e confira que não fica sem sessões NAT.

> [!IMPORTANT]
> O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.
> 
> 

## <a name="nat-requirements-for-microsoft-peering"></a>Requisitos do NAT para peering da Microsoft
O caminho de peering da Microsoft permite-lhe ligar aos serviços em nuvem da Microsoft que não são suportados através do caminho de peering público do Azure. A lista de serviços inclui os serviços do Office 365, como o Exchange Online, SharePoint Online, Skype para Empresas e o CRM Online. A Microsoft espera suportar uma conetividade bidirecional no peering da Microsoft. O tráfego destinado aos serviços em nuvem da Microsoft tem de realizar um SNAT para endereços IPv4 públicos válidos antes de serem introduzidos na rede da Microsoft. O tráfego destinado à sua rede a partir dos serviços em nuvem da Microsoft tem de realizar um SNAT antes de entrarem na sua rede. A figura abaixo fornece uma imagem de alto nível da forma como o NAT deve ser configurado para peering da Microsoft.

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

#### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Tráfego com origem na sua rede destinado à Microsoft
* Deve garantir que o tráfego está a entrar no caminho de peering da Microsoft com um endereço IPv4 público válido. A Microsoft deve conseguir validar o proprietário do conjunto de endereços IPv4 NAT num registo de Internet de encaminhamento regional (RIR) ou num registo de encaminhamento de Internet (IRR). Será efetuada uma verificação com base no número AS com o qual está a ser emparelhado e os endereços IP utilizados para o NAT. Veja a página [Requisitos do encaminhamento do ExpressRoute](expressroute-routing.md) para obter informações sobre os registos do encaminhamento.
* Os endereços IP utilizados para a configuração do peering público Azure e outros circuitos ExpressRoute não devem ser anunciados à Microsoft através da sessão do BGP. Não existe qualquer restrição ao comprimento do prefixo do IP do NAT anunciado através deste peering.
  
  > [!IMPORTANT]
  > O conjunto IP do NAT anunciado para a Microsoft não deve ser anunciado à Internet. Tal irá interromper a conectividade a outros serviços Microsoft.
  > 
  > 

#### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Tráfego com origem na Microsoft destinado à sua rede
* Determinados cenários requerem que a Microsoft inicie a conectividade com pontos finais de serviço alojados na sua rede. Um exemplo típico desse cenário seria conectividade com servidores ADFS alojados na sua rede a partir do Office 365. Nestes casos, deve fornecer prefixos apropriados da sua rede para o peering da Microsoft. 
* Deve realizar o SNAT do tráfego destinado a endereços IP na sua rede da Microsoft. 

## <a name="next-steps"></a>Passos seguintes
* Veja os requisitos para [Encaminhamento](expressroute-routing.md) e [QoS](expressroute-qos.md).
* Para obter informações sobre o fluxo de trabalho, veja [Circuito ExpressRoute aprovisiona fluxos de trabalho e estados de circuitos](expressroute-workflows.md).
* Configurar a ligação do ExpressRoute.
  
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configure o encaminhamento](expressroute-howto-routing-classic.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Nov16_HO2-->


