---
title: Requisitos do QoS para o ExpressRoute | Microsoft Docs
description: Esta página fornece os requisitos detalhados para configurar e gerir o QoS para circuitos do ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''

ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2016
ms.author: cherylmc

---
# Requisitos do QoS do ExpressRoute
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
| **Interativo** |AF41 (34) |Vídeo |
| AF21 (18) |Partilha de aplicações | |
| CS3 (24) |Sinalização SIP | |
| **Predefinição** |AF11 (10) |Transferência de ficheiros |
| CS0 (0) |Tudo o resto | |

* Deve classificar as cargas de trabalho e marcar os valores DSCP corretos. Siga as orientações fornecidas [aqui](https://technet.microsoft.com/library/gg405409.aspx) para saber como configurar as marcações DSCP na sua rede.
* Deve configurar e suportar várias filas do QoS na rede. Voz tem de ser uma classe autónoma e receber o tratamento EF especificado no RFC 3246. 
* Pode decidir o mecanismo de colocação em fila, a política de deteção de congestionamento e a alocação de largura de banda por classe de tráfego. No entanto, a marcação DSCP para cargas de trabalho do Skype para Empresas tem de ser preservada. Se estiver a utilizar marcações DSCP não listadas acima, por exemplo, AF31 (26), terá de reescrever este valor DSCP para 0 antes de enviar o pacote à Microsoft. A Microsoft só envia pacotes marcados com o valor DSCP mostrado na tabela acima. 

## Passos seguintes
* Veja os requisitos para [Encaminhamento](expressroute-routing.md) e [NAT](expressroute-nat.md).
* Consulte as ligações seguintes para configurar a ligação do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configurar o encaminhamento](expressroute-howto-routing-classic.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!--HONumber=Aug16_HO1-->


