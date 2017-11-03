---
title: SKUs de gateway de rede virtual do Azure legado | Microsoft Docs
description: SKUs de gateway de rede virtual antigo.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2017
ms.author: cherylmc
ms.openlocfilehash: d5127c7fa512bad49817fa4c8edf3a16ca2f7d60
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhar com o gateway de rede virtual SKUs (SKUs legados)

Este artigo contém informações sobre o gateway de rede virtual (antiga) SKUs de legado. O legado SKUs continuarão a funcionar em ambos os modelos de implementação para gateways VPN que já foram criados. Gateways de VPN clássicos continuam a utilizar os SKUs de legado, para gateways existentes tanto para novos gateways. Ao criar o novo Gestor de recursos VPN gateways, utilize o novo SKUs de gateway. Para obter informações sobre os SKUs de novo, consulte [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Débito agregado estimado por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurações suportadas pelo tipo SKU e a VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Redimensionar um gateway (alterar um SKU de gateway)

Pode redimensionar um SKU de gateway dentro da mesma família SKU. Por exemplo, se tiver um SKU Standard, pode redimensionar a um SKU HighPerformance. Não pode redimensionar os gateways de VPN entre os SKUs antigos e as novas famílias SKU. Por exemplo, não é possível voltar a partir de um SKU Standard para um SKU VpnGw2.

>[!IMPORTANT]
>Quando redimensionar um gateway, terá 20-30 minutos de indisponibilidade para esse gateway enquanto este estiver a ser redimensionada.
>
>

Redimensionar um SKU de gateway para o modelo de implementação clássica, utilize o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Redimensionar um SKU de gateway para o modelo de implementação Resource Manager, utilize o seguinte comando:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>Migrar para o novo SKUs de gateway

Se estiver a trabalhar com o modelo de implementação Resource Manager, pode migrar para o novo SKUS de gateway. Se estiver a trabalhar com o modelo de implementação clássica, não pode migrar para os novo SKUs e em vez disso, tem de continuar a utilizar os SKUs de legado.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a nova SKUs de Gateway, consulte [SKUs de Gateway](vpn-gateway-about-vpngateways.md#gwsku).

Para mais informações sobre as definições de configuração, consulte [definições de configuração sobre o Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).
