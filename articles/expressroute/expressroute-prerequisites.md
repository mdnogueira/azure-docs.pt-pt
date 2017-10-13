---
title: "Pré-requisitos para adoção do Azure ExpressRoute | Microsoft Docs"
description: "Esta página fornece uma lista de requisitos a serem satisfeitos para que possa ordenar um circuito do ExpressRoute do Azure."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2017
ms.author: cherylmc
ms.openlocfilehash: 8629235511e0dda149ceef6a9c834c3042f64f90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para ligar a serviços cloud da Microsoft com o ExpressRoute, terá de verificar se foram cumpridos os seguintes requisitos listados nas secções abaixo.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos incluídos nas subscrições do Azure. Uma subscrição do Azure é um requisito, mesmo se a conectividade estiver limitada a serviços cloud não pertencentes ao Microsoft Azure, como serviços do Office 365 e do Dynamics 365.
* Uma subscrição ativa do Office 365 (se utilizar os serviços do Office 365). Para obter mais informações, consulte a secção [Requisitos específicos do Office 365](#office-365-specific-requirements) deste artigo.

## <a name="connectivity-provider"></a>Fornecedor de conectividade

* Pode trabalhar com um [Parceiro de conectividade do ExpressRoute](expressroute-locations.md#partners) para se ligar à nuvem da Microsoft. Pode configurar uma ligação entre a sua rede no local e a Microsoft de [três modos](expressroute-introduction.md).
* Se o seu fornecedor não for um parceiro de conectividade do ExpressRoute, ainda pode ligar-se à nuvem da Microsoft através de um [fornecedor do Exchange na nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos da rede
* **Conectividade redundante**: não é necessário haver redundância na conectividade física entre o utilizador e o fornecedor. A Microsoft exige a configuração de sessões de BGP redundantes entre routers da Microsoft e routers de peering, mesmo se tiver apenas [uma ligação física a um Exchange de nuvem](expressroute-faqs.md#onep2plink).
* **Encaminhamento**: dependendo do modo como se liga ao Microsoft Cloud, o utilizador ou o fornecedor precisa de configurar e gerir as sessões de BGP para [domínios de encaminhamento](expressroute-circuit-peerings.md). Alguns fornecedores de conectividade Ethernet ou fornecedores do Exchange na nuvem poderão oferecer gestão de BGP como um serviço de valor acrescentado.
* **NAT**: a Microsoft só aceita endereços IP públicos através do peering da Microsoft. Se estiver a utilizar endereços IP privados na rede no local, o utilizador ou o fornecedor precisará de converter os endereços IP privados em endereços IP públicos [com o NAT](expressroute-nat.md).
* **QoS**: o Skype para Empresas tem vários serviços (por exemplo, voz, vídeo, texto) que exigem um tratamento QoS diferenciado. O utilizador e o fornecedor devem cumprir os [Requisitos de QoS](expressroute-qos.md).
* **Segurança de Rede**: tenha em conta a [segurança de rede](../best-practices-network-security.md) ao ligar à Microsoft Cloud através do ExpressRoute.

## <a name="office-365"></a>Office 365
Se planear ativar o Office 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações acerca dos requisitos do Office 365.

* [Descrição geral do ExpressRoute para o Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Encaminhamento com o ExpressRoute para o Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planeamento de rede e otimização de desempenho para o Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Calculadoras e ferramentas de largura de banda de rede](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integração do Office 365 com ambientes no local](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Vídeos de formação de avançada do ExpressRoute no Office 365](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Se planear ativar o Dynamics 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações acerca do Dynamics 365

* [Documento técnico do Dynamics 365 e ExpressRoute](http://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [URL](https://support.microsoft.com/kb/2655102) e [intervalos de endereços de IP do Dynamics 365](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Localizar um fornecedor de conectividade do ExpressRoute. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Veja os requisitos de [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configure o encaminhamento](expressroute-howto-routing-classic.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)
