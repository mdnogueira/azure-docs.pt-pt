---
title: 'Configurar filtros de rota para peering de ExpressRoute ao Microsoft Azure: CLI | Microsoft Docs'
description: Este artigo descreve como configurar filtros de rota para Peering da Microsoft com a CLI do Azure
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Configurar filtros de rota para peering da Microsoft: CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Filtros de rota são uma forma para consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos neste artigo ajudam a configurar e gerir filtros de rota para circuitos do ExpressRoute.

Serviços de Dynamics 365 e serviços do Office 365, como o Exchange Online, SharePoint Online e Skype para empresas, estão acessíveis através do peering da Microsoft. Quando peering da Microsoft está configurado num circuito do ExpressRoute, relacionadas com estes serviços de todos os prefixos estão anunciados através as que são estabelecidas sessões de BGP. Um valor das Comunidades de BGP está anexado a cada prefixo para identificar o serviço que é fornecido através de prefixo. Para obter uma lista dos valores das Comunidades BGP e os serviços que mapeiam para, consulte [Comunidades BGP](expressroute-routing.md#bgp).

Se necessitar de conectividade a todos os serviços, um grande número de prefixos está anunciado através da BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantida por routers na sua rede. Se planear consumir apenas um subconjunto de serviços fornecidos via peering da Microsoft, pode reduzir o tamanho das suas tabelas de rota de duas formas. Pode:

* Filtre prefixos indesejáveis aplicando filtros de rota no Comunidades BGP. Esta é uma prática padrão de rede e é utilizada frequentemente no diversas redes.

* Definir os filtros de rota e aplicá-las para o circuito do ExpressRoute. Um filtro de rota é um novo recurso, que lhe permite selecionar a lista de serviços que planear consumir através do peering da Microsoft. Routers de ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

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

* Tem de ter um circuito ExpressRoute ativo que tenha aprovisionado de peering da Microsoft. Pode utilizar as seguintes instruções para realizar estas tarefas:
  * [Criar um circuito ExpressRoute](howto-circuit-cli.md) e tem de ativar o circuito pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute tem de estar num Estado aprovisionado e ativado.
  * [Criar peering da Microsoft](howto-routing-cli.md) se gerir diretamente a sessão de BGP. Ou, solicite ao seu fornecedor de conectividade aprovisionar o peering da Microsoft para o seu circuito.

* Tem de criar e configurar um filtro de rota.
  * Identificar os serviços que com consumir através do peering da Microsoft
  * Identifique a lista de valores das Comunidades BGP associado aos serviços
  * Criar uma regra para permitir a lista de prefixo correspondente os valores das Comunidades do BGP

* Terá de ligar o filtro de rota para o circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli).

* Reveja o [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](howto-circuit-cli.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute tem de estar num Estado aprovisionado e ativado.

* Tem de ter um peering da Microsoft Active Directory. Siga as instruções apresentadas em [criar e modificar a configuração do peering](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecionar a sua subscrição

Para iniciar a configuração, inicie sessão na sua conta do Azure. Utilize os seguintes exemplos para ajudar na ligação:

```azurecli
az login
```

Verifique as subscrições da conta.

```azurecli
az account list
```

Selecione a subscrição para o qual pretende criar um circuito ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Passo 1: Obter uma lista de prefixos e os valores das Comunidades BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores das Comunidades BGP

Utilize o cmdlet seguinte para obter a lista de valores das Comunidades BGP associados a serviços acessíveis através do peering da Microsoft e a lista de prefixos associadas:

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Se uma lista de valores que pretende utilizar

Se uma lista dos valores das Comunidades BGP que pretende utilizar no filtro de rota. Por exemplo, o valor das Comunidades de BGP para os serviços de Dynamics 365 é 12076:5040.

## <a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e, a regra tem de ser do tipo 'Permitir'. Esta regra pode ter uma lista dos valores das Comunidades BGP associados à mesma.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota

Em primeiro lugar, crie o filtro de rota. O comando 'az criar do filtro de rota de rede' apenas cria um recurso de filtro de rota. Depois de criar o recurso, deve, em seguida, criar uma regra e anexe-o para o objeto de filtro de rota. Execute o seguinte comando para criar um recurso de filtro de rota:

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Execute o seguinte comando para criar uma nova regra:
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Passo 3: Ligar o filtro de rota para um circuito do ExpressRoute

Execute o seguinte comando para anexar o filtro de rota para o circuito do ExpressRoute:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, utilize o seguinte comando:

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está ligado a um circuito, as atualizações à lista de comunidades de BGP automaticamente propagam alterações de anúncio de prefixo adequado através de estabelecer sessões de BGP. Pode atualizar a lista de Comunidades BGP do seu filtro de rota com o seguinte comando:

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Anular a exposição de um filtro de rota de um circuito do ExpressRoute

Depois de um filtro de rota é desligado do circuito ExpressRoute, nenhum prefixos estão anunciados através da sessão de BGP. Pode desanexar um filtro de rota de um circuito ExpressRoute com o seguinte comando:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Para eliminar um filtro de rota

Só é possível eliminar um filtro de rota, se não está ligado a qualquer circuito. Certifique-se de que o filtro de rota não está ligado a qualquer circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota com o seguinte comando:

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
