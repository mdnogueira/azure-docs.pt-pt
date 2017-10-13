---
title: Requisitos do QoS para o ExpressRoute | Microsoft Docs
description: "Esta página fornece os requisitos detalhados para configurar e gerir o QoS para circuitos do ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: db1c1447-0283-4a09-907b-ae481adc40c7
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: cherylmc
ms.openlocfilehash: c097a9ccba91f59b323215d42d37e6d85e0981ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-qos-requirements"></a>Requisitos do QoS do ExpressRoute
O Skype para Empresas tem várias cargas de trabalho que exigem um tratamento do QoS diferenciado. Se planear consumir serviços de voz através do ExpressRoute, deve cumprir os requisitos descritos abaixo.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Os requisitos do QoS aplicam-se apenas ao peering da Microsoft. Os valores DSCP no seu tráfego recebido de rede no peering público do Azure e no peering privado do Azure serão repostos a 0. 
> 
> 

A tabela seguinte fornece uma lista de marcações DSCP utilizadas pelo Skype para Empresas. Para obter mais informações, veja [Gerir QoS para Skype para Empresas](https://technet.microsoft.com/library/gg405409.aspx).

| **Classe de Tráfego** | **Tratamento (Marcação DSCP)** | **Cargas de trabalho do Skype para Empresas** |
| --- | --- | --- |
| **Voz** |EF (46) |Voz do Skype/Lync |
| **Interativo** |AF41 (34) |Video, VBSS |
| AF21 (18) |Partilha de aplicações | |
| **Predefinição** |AF11 (10) |Transferência de ficheiros |
| CS0 (0) |Tudo o resto | |

* Deve classificar as cargas de trabalho e marcar os valores DSCP corretos. Siga as orientações fornecidas [aqui](https://technet.microsoft.com/library/gg405409.aspx) para saber como configurar as marcações DSCP na sua rede.
* Deve configurar e suportar várias filas do QoS na rede. Voz tem de ser uma classe autónoma e receber o tratamento EF especificado no RFC 3246. 
* Pode decidir o mecanismo de colocação em fila, a política de deteção de congestionamento e a alocação de largura de banda por classe de tráfego. No entanto, a marcação DSCP para cargas de trabalho do Skype para Empresas tem de ser preservada. Se estiver a utilizar marcações DSCP não listadas acima, por exemplo, AF31 (26), terá de reescrever este valor DSCP para 0 antes de enviar o pacote à Microsoft. A Microsoft só envia pacotes marcados com o valor DSCP mostrado na tabela acima. 

## <a name="next-steps"></a>Passos seguintes
* Veja os requisitos para [Encaminhamento](expressroute-routing.md) e [NAT](expressroute-nat.md).
* Consulte as ligações seguintes para configurar a ligação do ExpressRoute.
  
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configure o encaminhamento](expressroute-howto-routing-classic.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)

