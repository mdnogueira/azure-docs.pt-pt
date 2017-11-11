---
title: "Mover um peering público no Azure ExpressRoute para peering da Microsoft | Microsoft Docs"
description: "Este artigo mostra-lhe os passos para mover o peering público para a Microsoft peering no ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/09/2017
ms.author: cherylmc
ms.openlocfilehash: 4fd0f1cbba36957ae5ecc1b7436d1f1734a3ef79
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um peering público para peering da Microsoft

ExpressRoute suporta a utilização de peering da Microsoft com filtros de rota para serviços do Azure PaaS, como o armazenamento do Azure e SQL Database do Azure. Agora tem apenas um domínio de encaminhamento para aceder ao Microsoft PaaS e serviços SaaS. Pode utilizar filtros de rota para seletivamente anunciar os prefixos de serviço PaaS de regiões do Azure que pretende consumir.

Este artigo ajuda-o a mover uma configuração do peering público para peering da Microsoft com sem períodos de indisponibilidade. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [ExpressRoute circuitos e domínios de encaminhamento](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Tem de ter o suplemento ExpressRoute premium para utilizar o peering da Microsoft. Para mais informações sobre o suplemento premium, consulte o [FAQ do ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before"></a>Antes de começar

* Para ligar ao peering da Microsoft, tem de configurar e gerir o NAT. O fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se estiver a planear aceder ao Azure PaaS e serviços SaaS do Azure em peering da Microsoft, é importante para o tamanho do conjunto de IP do NAT corretamente. Para obter mais informações sobre NAT para o ExpressRoute, consulte o [requisitos do NAT para peering da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Se estiver a utilizar o peering público e ter atualmente regras de rede de IP para endereços IP públicos que são utilizados para aceder ao [Storage do Azure](../storage/common/storage-network-security.md) ou [SQL Database do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), tem de certificar-se de que o conjunto de IP do NAT configurado com a Microsoft, peering está incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou a conta de SQL do Azure.

* Para mover para peering da Microsoft com sem períodos de indisponibilidade, utilize os passos neste artigo na ordem em que são apresentados.

## <a name="create"></a>1. Criar peering da Microsoft

Se o peering da Microsoft não tiver sido criada, utilize qualquer um dos seguintes artigos para criar peering da Microsoft. Se as ofertas de fornecedor de conectividade geridas layer 3 serviços, pode pedir ao fornecedor de conectividade para ativar o peering da Microsoft para o seu circuito.

  * [Criar peering da Microsoft através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Criar peering da Microsoft com o Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Criar peering da Microsoft com a CLI do Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Validar Microsoft peering está ativado

Certifique-se de que o peering da Microsoft está ativado e os prefixos públicos anunciados estão no estado configurado.

  * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurar e ligar um filtro de rota para o circuito

Por predefinição, novo Microsoft peerings não anunciar os prefixos até um filtro de rota está ligado ao circuito. Quando cria uma regra de filtro de rota, pode especificar a lista de comunidades de serviço para regiões do Azure que pretende consumir para os serviços do Azure PaaS, conforme mostrado na captura de ecrã seguinte:

![Intercalar o peering público](.\media\how-to-move-peering\public.png)

Configure filtros de rotas com qualquer um dos seguintes artigos:

  * [Configurar filtros de rota para peering da Microsoft através do portal do Azure](how-to-routefilter-portal.md)
  * [Configurar filtros de rota para peering da Microsoft com o Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurar filtros de rota para peering da Microsoft com a CLI do Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Eliminar o peering público

Depois de verificar que o peering da Microsoft está configurado e prefixos que pretende consumir corretamente estão anunciados no peering da Microsoft, em seguida, pode eliminar o peering público. Para eliminar o peering público, utilize qualquer um dos seguintes artigos:

  * [Eliminar o peering público do Azure através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Eliminar o peering público do Azure com o Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Eliminar o peering público do Azure com a CLI](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).