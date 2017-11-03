---
title: Sobre os gateways de rede virtual do ExpressRoute | Microsoft Docs
description: Saiba mais sobre os gateways de rede virtual para o ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: cherylmc
ms.openlocfilehash: a6363fa380d0bab05d7500141cc6019d1d3f68b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Acerca dos gateways de rede virtual para o ExpressRoute
Um gateway de rede virtual é utilizado para enviar tráfego de rede entre redes virtuais do Azure e localizações no local. Quando configurar uma ligação ExpressRoute, tem de criar e configurar um gateway de rede virtual e uma ligação de gateway de rede virtual.

Quando criar um gateway de rede virtual, especifica várias definições. Uma das definições necessárias Especifica se o gateway irá ser utilizado para tráfego de ExpressRoute ou VPN de Site para Site. No modelo de implementação Resource Manager, é a definição '-GatewayType'.

Quando o tráfego de rede é enviado uma ligação privada, utilize o tipo de gateway 'ExpressRoute'. Também se refere a este como um gateway ExpressRoute. Quando o tráfego de rede é enviado encriptado através da Internet pública, utilize o tipo de gateway 'Vpn'. Isto é referido como um gateway de VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute. Este artigo incida no gateway de rede virtual do ExpressRoute.

## <a name="gwsku"></a>SKUs de Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se pretender atualizar o gateway para um SKU de gateway mais poderoso, na maioria dos casos pode utilizar o cmdlet do PowerShell de 'Redimensionamento AzureRmVirtualNetworkGateway'. Isto irá funcionar para atualizações para Standard e HighPerformance SKUs. No entanto, para atualizar para o SKU de UltraPerformance, terá de recriar o gateway.

### <a name="aggthroughput"></a>Débito agregado estimado por SKU de gateway
A tabela seguinte mostra os tipos de gateways e o débito agregado estimado. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Débito de aplicação depende de vários fatores, tais como a latência de ponto a ponto e o número de fluxos de tráfego que abre a aplicação. Os números existentes na tabela representam o limite superior que theorectically de pode aplicação alcançar num ambiente ideal. 
> 
>

## <a name="resources"></a>Cmdlets do PowerShell e REST APIs
Para obter recursos técnicos adicionais e requisitos de sintaxe específica quando utilizar REST APIs e cmdlets do PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passos seguintes
Consulte [descrição geral do ExpressRoute](expressroute-introduction.md) para obter mais informações sobre as configurações de ligação disponíveis. 

