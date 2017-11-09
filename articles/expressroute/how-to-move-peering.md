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
ms.date: 11/07/2017
ms.author: cherylmc
ms.openlocfilehash: 311e1de3200cd684bbec1329ebd5217b4fb3a2e2
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um peering público para peering da Microsoft

O ExpressRoute suporta agora a serviços do Azure PaaS, como o armazenamento do Azure e SQL Database do Azure, utilizando o peering da Microsoft com filtros de rota. Agora tem apenas um domínio de encaminhamento para aceder ao Microsoft PaaS e serviços SaaS. Pode tirar partido de filtros de rota para seletivamente anunciar os prefixos de serviço PaaS de regiões do Azure que pretende consumir.

Este artigo ajuda-o a mover uma configuração do peering público para peering da Microsoft com sem períodos de indisponibilidade. Para obter mais informações sobre domínios de encaminhamento e peerings, consulte [ExpressRoute circuitos e domínios de encaminhamento](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Tem de ter o suplemento ExpressRoute premium para utilizar o peering da Microsoft. Para mais informações sobre o suplemento premium, consulte o [FAQ do ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before-you-begin"></a>Antes de começar

* Para ligar ao peering da Microsoft, tem de configurar e gerir o NAT. O fornecedor de conectividade pode configurar e gerir o NAT como um serviço gerido. Se estiver a planear aceder ao Azure PaaS e serviços SaaS do Azure em peering da Microsoft, é importante para o tamanho do conjunto de IP do NAT corretamente. Para obter mais informações sobre NAT para o ExpressRoute, consulte o [requisitos do NAT para peering da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Se tiver atualmente uma lista de controlo de acesso (ACL) de rede para os pontos finais de serviço de Azure PaaS, ao utilizar o peering público do Azure que tem de garantir que o IP do NAT conjunto configurado com o peering da Microsoft está incluído na lista de controlo de acesso configurada para o serviço ponto final.

Para mover para peering da Microsoft com sem períodos de indisponibilidade, tem de utilizar os passos neste artigo na ordem em que são apresentados.

## <a name="1-create-microsoft-peering"></a>1. Criar peering da Microsoft

Se o peering da Microsoft não tiver sido criada, utilize qualquer um dos seguintes artigos para criar peering da Microsoft. Se as ofertas de fornecedor de conectividade geridas layer 3 serviços, pode pedir ao fornecedor de conectividade para ativar o peering da Microsoft para o seu circuito.

  * [Criar peering da Microsoft através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Criar peering da Microsoft com o Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Criar peering da Microsoft com a CLI do Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a>2. Validar Microsoft peering está ativado

Certifique-se de que o peering da Microsoft está ativado e os prefixos públicos anunciados estão no estado configurado.

  * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a>3. Configurar e ligar um filtro de rota para o circuito

Por predefinição, novo Microsoft peerings não anunciar os prefixos até um filtro de rota está ligado ao circuito. Quando cria uma regra de filtro de rota, pode especificar a lista de comunidades de serviço para regiões do Azure que pretende consumir para os serviços do Azure PaaS, conforme mostrado na captura de ecrã seguinte:

![Intercalar o peering público](.\media\how-to-move-peering\public.png)

Utilize qualquer um dos seguintes artigos para configurar filtros de rota.

  * [Configurar filtros de rota para peering da Microsoft através do portal do Azure](how-to-routefilter-portal.md)
  * [Configurar filtros de rota para peering da Microsoft com o Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurar filtros de rota para peering da Microsoft com a CLI do Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a>4. Eliminar o peering público

Depois de verificar que o peering da Microsoft está configurado e prefixos que pretende consumir corretamente estão anunciados no peering da Microsoft, em seguida, pode eliminar o peering público. Para eliminar o peering público, utilize qualquer um dos seguintes artigos:

  * [Eliminar o peering público do Azure através do portal do Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Eliminar o peering público do Azure com o Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Eliminar o peering público do Azure com a CLI](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).