---
title: "Modelos de conectividade do ExpressRoute: ligar ao Microsoft Azure através de fornecedores de serviços de rede, trocas e fornecedores de Ethernet | Microsoft Docs"
description: "Este artigo descreve os diferentes modos de conectividade entre a rede do cliente e os serviços do Microsoft Azure, do Office 365 e do Dynamics 365. Os clientes podem utilizar fornecedores de MPLS, trocas da cloud e fornecedores de Ethernet."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: cherylmc
ms.openlocfilehash: 00f97da2189491103c461b49ac82feb92d8f4b9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-connectivity-models"></a>Modelos de conectividade do ExpressRoute
Pode criar uma ligação entre a rede no local e a cloud da Microsoft de três formas diferentes, [Localização Conjunta do CloudExchange](#CloudExchange), [Ligação Ethernet Ponto a Ponto](#Ethernet) e [Ligação (IPVPN) Qualquer a Qualquer](#IPVPN). Os fornecedores de conectividade podem oferecer um ou mais modelos de conetividade. Pode trabalhar com o seu fornecedor de conectividade para escolher o modelo que funciona melhor para si.
<br><br>

![Diagrama dos modelos de conectividade do ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Localizado conjuntamente numa troca de cloud
Se estiver localizado conjuntamente num local com uma troca de nuvem, pode organizar ligações cruzadas virtuais para a nuvem da Microsoft através da troca de Ethernet do fornecedor de localização conjunta. Os fornecedores de localização conjunta podem oferecer ligações cruzadas de Camada 2 ou ligações cruzadas geridas de Camada 3 entre a sua infraestrutura na localização conjunto e a nuvem da Microsoft.

## <a name="Ethernet"></a>Ligações de Ethernet ponto a ponto
Pode ligar os seus centros de dados/escritórios no local à nuvem da Microsoft através de ligações de Ethernet ponto a ponto. Os fornecedores de Ethernet ponto a ponto podem oferecer ligações de Camada 2 ou ligações geridas de Camada 3 entre o seu site e a nuvem da Microsoft.

## <a name="IPVPN"></a>Redes qualquer a qualquer (VPN de IP)
Pode integrar a sua WAN com a nuvem da Microsoft. Os fornecedores de VPN de IP (normalmente VPN de MPLS) oferecem uma conectividade qualquer a qualquer entre as suas sucursais e os centros de dados. A nuvem da Microsoft pode ser interligada à sua WAN para torná-la mais semelhante a qualquer outra sucursal. Os fornecedores de WAN oferecem normalmente uma conectividade gerida de Camada 3. As funcionalidades e capacidades do ExpressRoute são idênticas em todos os modelos de conetividade acima. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre ligações ExpressRoute e domínios de encaminhamento. Veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Saiba mais sobre funcionalidades do ExpressRoute. Consulte a [Descrição Geral Técnica do ExpressRoute](expressroute-introduction.md)
* Encontre um fornecedor de serviços. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Veja os requisitos para [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configure o encaminhamento](expressroute-howto-routing-portal-resource-manager.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)