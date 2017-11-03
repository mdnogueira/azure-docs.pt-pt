---
title: 'Configurar filtros de rota para peering de ExpressRoute ao Microsoft Azure: PowerShell | Microsoft Docs'
description: "Este artigo descreve como configurar filtros de rota para Peering da Microsoft através do PowerShell"
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
ms.openlocfilehash: 76077be4f443f8e0dd6341d1a87539277f23e1c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Configurar filtros de rota para peering da Microsoft: PowerShell
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
  - [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e tem de ativar o circuito pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute tem de estar num Estado aprovisionado e ativado.
  - [Criar peering da Microsoft](expressroute-circuit-peerings.md) se gerir diretamente a sessão de BGP. Ou, solicite ao seu fornecedor de conectividade aprovisionar o peering da Microsoft para o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identificar os serviços que com consumir através do peering da Microsoft
    - Identifique a lista de valores das Comunidades BGP associado aos serviços
    - Criar uma regra para permitir a lista de prefixo correspondente os valores das Comunidades do BGP

-  Terá de ligar o filtro de rota para o circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que satisfazem os seguintes critérios:

 - Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, consulte [instalar e configurar o Azure PowerShelll](/powershell/azure/install-azurerm-ps).

  > [!NOTE]
  > Transfira a versão mais recente a partir da galeria do PowerShell, em vez de utilizarem o instalador. O instalador não suporta atualmente os cmdlets necessários.
  > 

 - Reveja o [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute tem de estar num Estado aprovisionado e ativado.

 - Tem de ter um peering da Microsoft Active Directory. Siga as instruções apresentadas em [criar e modificar a configuração do peering](expressroute-circuit-peerings.md)

### <a name="log-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Antes de iniciar esta configuração, deve iniciar sessão na conta do Azure. O cmdlet pede-lhe as credenciais de início de sessão da conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.

Abra a consola do PowerShell com privilégios elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Login-AzureRmAccount
```

Se tiver múltiplas subscrições do Azure, verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

Especifique a subscrição que pretende utilizar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Passo 1: Obter uma lista de prefixos e os valores das Comunidades BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores das Comunidades BGP

Utilize o cmdlet seguinte para obter a lista de valores das Comunidades BGP associados a serviços acessíveis através do peering da Microsoft e a lista de prefixos associadas:

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Se uma lista de valores que pretende utilizar

Se uma lista dos valores das Comunidades BGP que pretende utilizar no filtro de rota. Por exemplo, o valor das Comunidades de BGP para os serviços de Dynamics 365 é 12076:5040.

## <a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e, a regra tem de ser do tipo 'Permitir'. Esta regra pode ter uma lista dos valores das Comunidades BGP associados à mesma.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota

Em primeiro lugar, crie o filtro de rota. O comando 'New-AzureRmRouteFilter' apenas cria um recurso de filtro de rota. Depois de criar o recurso, deve, em seguida, criar uma regra e anexe-o para o objeto de filtro de rota. Execute o seguinte comando para criar um recurso de filtro de rota:

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode especificar um conjunto de Comunidades BGP como uma lista separada por vírgulas, conforme mostrado no exemplo. Execute o seguinte comando para criar uma nova regra:
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Adicionar a regra de filtro de rota

Execute o seguinte comando para adicionar a regra de filtro para o filtro de rota:
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Passo 3: Ligar o filtro de rota para um circuito do ExpressRoute

Execute o seguinte comando para anexar o filtro de rota para o circuito do ExpressRoute, partindo do princípio que tem apenas peering da Microsoft:

```powershell
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, utilize os seguintes passos:

1. Execute o seguinte comando para obter o recurso de filtro de rota:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Obter a rota de regras de filtro para o recurso de filtro de rota, executando o seguinte comando:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está ligado a um circuito, as atualizações à lista de comunidades de BGP automaticamente propagam alterações de anúncio de prefixo adequado através de estabelecer sessões de BGP. Pode atualizar a lista de Comunidades BGP do seu filtro de rota com o seguinte comando:

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Anular a exposição de um filtro de rota de um circuito do ExpressRoute

Depois de um filtro de rota é desligado do circuito ExpressRoute, nenhum prefixos estão anunciados através da sessão de BGP. Pode desanexar um filtro de rota de um circuito ExpressRoute com o seguinte comando:
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Para eliminar um filtro de rota

Só é possível eliminar um filtro de rota, se não está ligado a qualquer circuito. Certifique-se de que o filtro de rota não está ligado a qualquer circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota com o seguinte comando:

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).