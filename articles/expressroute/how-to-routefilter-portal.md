---
title: 'Configurar filtros de rota para peering de ExpressRoute ao Microsoft Azure: Portal | Microsoft Docs'
description: "Este artigo descreve como configurar filtros de rota para Peering da Microsoft através do portal do Azure"
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: 0129a48e43e90001785a5977d4b0d1fd9fa9fd7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configurar filtros de rota para peering da Microsoft: portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Filtros de rota são uma forma para consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos neste artigo ajudam a configurar e gerir filtros de rota para circuitos do ExpressRoute.

Serviços de Dynamics 365 e serviços do Office 365, como o Exchange Online, SharePoint Online e Skype para empresas e serviços do Azure, tais como o armazenamento e a base de dados SQL estão acessíveis através do peering da Microsoft. Quando peering da Microsoft está configurado num circuito do ExpressRoute, relacionadas com estes serviços de todos os prefixos estão anunciados através as que são estabelecidas sessões de BGP. Um valor das Comunidades de BGP está anexado a cada prefixo para identificar o serviço que é fornecido através de prefixo. Para obter uma lista dos valores das Comunidades BGP e os serviços que mapeiam para, consulte [Comunidades BGP](expressroute-routing.md#bgp).

Se necessitar de conectividade a todos os serviços, um grande número de prefixos está anunciado através da BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantida por routers na sua rede. Se planear consumir apenas um subconjunto de serviços fornecidos via peering da Microsoft, pode reduzir o tamanho das suas tabelas de rota de duas formas. Pode:

- Filtre prefixos indesejáveis aplicando filtros de rota no Comunidades BGP. Esta é uma prática padrão de rede e é utilizada frequentemente no diversas redes.

- Definir os filtros de rota e aplicá-las para o circuito do ExpressRoute. Um filtro de rota é um novo recurso, que lhe permite selecionar a lista de serviços que planear consumir através do peering da Microsoft. Routers de ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about"></a>Sobre os filtros de rotas

Quando o peering da Microsoft está configurado no seu circuito do ExpressRoute, os routers de limite de Microsoft estabelecer um par de sessões de BGP com os routers de limite (seu ou o fornecedor de conectividade). Não existem rotas estão anunciadas à sua rede. Para ativar os anúncios de rota à sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que pretende consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de permissão de todos os valores de Comunidades BGP. Depois de um recurso de filtro de rota é definido e ligado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores das Comunidades BGP estão anunciados à sua rede.

Ser capaz de ligar os filtros de rotas com serviços do Office 365 nos mesmos, tem de ter autorização para consumir os serviços do Office 365 através do ExpressRoute. Se não está autorizado a consumir os serviços do Office 365 através do ExpressRoute, a operação ligar filtros de rota falhar. Para mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). A conectividade aos serviços de Dynamics 365 não requer qualquer autorização anterior.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através da Microsoft, peering, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos ExpressRoute que estão configurados em ou após 1 de Agosto de 2017 não terão qualquer prefixos anunciados até um filtro de rota está ligado ao circuito.
> 
> 

### <a name="workflow"></a>Fluxo de trabalho

Para conseguir ligar com êxito aos serviços através do peering da Microsoft, tem de concluir os seguintes passos de configuração:

- Tem de ter um circuito ExpressRoute ativo que tenha aprovisionado de peering da Microsoft. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e tem de ativar o circuito pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute tem de estar num Estado aprovisionado e ativado.
  - [Criar peering da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se gerir diretamente a sessão de BGP. Ou, solicite ao seu fornecedor de conectividade aprovisionar o peering da Microsoft para o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identificar os serviços que com consumir através do peering da Microsoft
    - Identifique a lista de valores das Comunidades BGP associado aos serviços
    - Criar uma regra para permitir a lista de prefixo correspondente os valores das Comunidades do BGP

-  Terá de ligar o filtro de rota para o circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que satisfazem os seguintes critérios:

 - Reveja o [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute tem de estar num Estado aprovisionado e ativado.

 - Tem de ter um peering da Microsoft Active Directory. Siga as instruções apresentadas em [criar e modificar a configuração do peering](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Passo 1: Obter uma lista de prefixos e os valores das Comunidades BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores das Comunidades BGP

Valores das Comunidades BGP associados a serviços acessíveis através do peering da Microsoft está disponível no [requisitos de encaminhamento do ExpressRoute](expressroute-routing.md) página.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Se uma lista de valores que pretende utilizar

Se uma lista dos valores das Comunidades BGP que pretende utilizar no filtro de rota. Por exemplo, o valor das Comunidades de BGP para os serviços de Dynamics 365 é 12076:5040.

## <a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e, a regra tem de ser do tipo 'Permitir'. Esta regra pode ter uma lista dos valores das Comunidades BGP associados à mesma.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota
Pode criar um filtro de rota, selecionando a opção para criar um novo recurso. Clique em **novo** > **redes** > **RouteFilter**, conforme mostrado na imagem seguinte:

![Criar um filtro de rota](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

Tem de colocar o filtro de rota num grupo de recursos. 

![Criar um filtro de rota](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode adicionar e atualizar as regras, selecionando o separador de regra de gerir para o filtro de rota.

![Criar um filtro de rota](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


Pode selecionar os serviços que pretende ligar a partir da lista pendente e guardar a regra quando terminar.

![Criar um filtro de rota](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>Passo 3: Ligar o filtro de rota para um circuito do ExpressRoute

Pode anexar o filtro de rota para um circuito, selecionando o botão "adicionar o circuito" e selecionando o circuito de ExpressRoute na lista pendente.

![Criar um filtro de rota](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

Se o fornecedor de conectividade configura o peering do circuito a partir do painel de circuito ExpressRoute para a atualização do circuito ExpressRoute antes de selecionar o botão "adicionar o circuito".

![Criar um filtro de rota](.\media\how-to-routefilter-portal\RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Pode ver as propriedades de um filtro de rota quando abrir o recurso no portal.

![Criar um filtro de rota](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Pode atualizar a lista de valores das Comunidades BGP ligado a um circuito, selecionando o botão "Gerir regra".


![Criar um filtro de rota](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![Criar um filtro de rota](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


### <a name="detach"></a>Anular a exposição de um filtro de rota de um circuito do ExpressRoute

Para anular a exposição de um circuito do filtro de rota, clique no circuito e clique em "desassocie".

![Criar um filtro de rota](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


### <a name="delete"></a>Para eliminar um filtro de rota

Pode eliminar um filtro de rota, selecionando o botão Eliminar. 

![Criar um filtro de rota](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).