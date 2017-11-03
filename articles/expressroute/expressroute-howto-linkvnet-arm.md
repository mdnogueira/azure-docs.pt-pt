---
title: 'Ligar uma rede virtual a um circuito de ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: "Este documento fornece uma descrição geral de como ligar redes virtuais (VNets) para circuitos do ExpressRoute, utilizando o modelo de implementação do Resource Manager e o PowerShell."
services: expressroute
documentationcenter: na
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: ganesr
ms.openlocfilehash: 8c2f3036f754a98090ab860f95900416690ebf83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual para um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo ajuda-o a ligar redes virtuais (VNets) para circuitos ExpressRoute do Azure utilizando o modelo de implementação do Resource Manager e o PowerShell. Redes virtuais podem ser na mesma subscrição ou parte de outra subscrição. Este artigo também mostra-lhe como atualizar uma ligação de rede virtual. 

## <a name="before-you-begin"></a>Antes de começar
* Instale a versão mais recente dos módulos do PowerShell do Azure. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).
* Reveja o [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e ter o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o peering privado do Azure configurada para o seu circuito. Consulte o [configurar o encaminhamento](expressroute-howto-routing-arm.md) artigo para obter instruções de encaminhamento. 
  * Certifique-se de que o peering privado do Azure está configurado e o peering de BGP entre a rede e a Microsoft está a funcionar, de modo a que pode ativar a conetividade de ponto a ponto.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para o ExpressRoute utiliza o gatewaytype como 'ExpressRoute', não VPN.

* Pode ligar até 10 redes virtuais para um circuito do ExpressRoute standard. Todas as redes virtuais tem de ser na mesma região geopolítica quando utilizar um circuito do ExpressRoute standard. 

* Pode ligar um redes virtuais fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais para o circuito do ExpressRoute, se tiver ativado o suplemento ExpressRoute premium. Verifique o [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.


## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição a um circuito
Pode ligar um gateway de rede virtual para um circuito ExpressRoute com o cmdlet seguinte. Certifique-se de que o gateway de rede virtual é criado e estiver pronto para ligar antes de executar o cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Pode partilhar um circuito ExpressRoute entre várias subscrições. A figura seguinte mostra um simples schematic de funciona como partilha para circuitos do ExpressRoute entre várias subscrições.

Cada uma das nuvens inferior na nuvem grande é utilizada para representar subscrições que pertencem aos diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode utilizar as seus próprios subscrição para implementar os seus serviços-, mas pode partilhar um único circuito de ExpressRoute para ligar a sua rede no local. Um único departamento (neste exemplo: IT) pode ter o circuito ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito ExpressRoute.

> [!NOTE]
> Custos de largura de banda e de conectividade para o circuito de ExpressRoute serão aplicados ao proprietário da subscrição. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - utilizadores de circuito e proprietários de circuito

'Proprietário do circuito' é um utilizador autorizado de energia do recurso de circuito ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por 'utilizadores circuito'. Utilizadores de circuito são proprietários dos gateways de rede virtual que não estão na mesma subscrição que o circuito ExpressRoute. Utilizadores de circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações em qualquer altura. Revogar um resultados de autorização em todas as ligações de ligação seja eliminados da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Para criar uma autorização**

O proprietário do circuito cria uma autorização. Isto resulta na criação de uma chave de autorização que pode ser utilizada por um utilizador do circuito para ligar os gateways de rede virtual para o circuito do ExpressRoute. Uma autorização é válida para apenas uma ligação.

O seguinte fragmento de cmdlet mostra como criar uma autorização:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


A resposta a esta irá conter a chave de autorização e o estado:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Para rever autorizações**

O proprietário do circuito pode rever todas as autorizações que são emitidas sobre um determinado circuito, executando o seguinte cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para adicionar autorizações**

O proprietário do circuito pode adicionar autorizações utilizando o cmdlet seguinte:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para eliminar autorizações**

O proprietário do circuito pode revogar/eliminar autorizações ao utilizador executando o seguinte cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

O utilizador do circuito tem o ID de elemento de rede e uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

ID de elemento de rede pode ser verificada do seguinte comando:

```powershell
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de ligação**

O utilizador do circuito pode executar o cmdlet seguinte para resgatar uma autorização de ligação:

```powershell
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para libertar uma autorização de ligação**

Pode disponibilizar uma autorização ao eliminar a ligação que liga o circuito de ExpressRoute para a rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma ligação de rede virtual
Pode atualizar determinadas propriedades de uma ligação de rede virtual. 

**Para atualizar o peso de ligação**

A rede virtual pode ser ligada a vários circuitos do ExpressRoute. Poderá receber o mesmo prefixo de mais do que um circuito do ExpressRoute. Para escolher qual a ligação ao enviar o tráfego destinado a este prefixo, pode alterar *RoutingWeight* de uma ligação. Será possível enviar o tráfego a ligação com o maior *RoutingWeight*.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

O intervalo de *RoutingWeight* é 0 para 32000. O valor predefinido é 0.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
