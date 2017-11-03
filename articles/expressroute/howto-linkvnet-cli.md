---
title: 'Ligar uma rede virtual a um circuito de ExpressRoute: CLI: Azure | Microsoft Docs'
description: "Este documento fornece uma descrição geral de como ligar redes virtuais (VNets) para circuitos do ExpressRoute, utilizando o modelo de implementação do Resource Manager e a CLI."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: anzaman,cherylmc
ms.openlocfilehash: 0ea696e796ec3a943bc028f56da417978b728b82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ligar uma rede virtual para um circuito ExpressRoute com o CLI

Este artigo ajuda-o a ligar redes virtuais (VNets) para circuitos ExpressRoute do Azure com a CLI. Para ligar utilizando a CLI do Azure, as redes virtuais devem ser criadas utilizando o modelo de implementação Resource Manager. Estes podem ser na mesma subscrição, ou parte de outra subscrição. Se pretender utilizar um método diferente para ligar a VNet a um circuito ExpressRoute, pode selecionar um artigo na lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Tem a versão mais recente da interface de linha de comandos (CLI). Para obter mais informações, consulte [instalar o Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
* Tem de rever o [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito ExpressRoute](howto-circuit-cli.md) e ter o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o peering privado do Azure configurada para o seu circuito. Consulte o [configurar o encaminhamento](howto-routing-cli.md) artigo para obter instruções de encaminhamento. 
  * Certifique-se de que o peering privado do Azure está configurado. O BGP peering entre a rede e a Microsoft tem de ser cópias de segurança para que pode ativar a conetividade de ponto a ponto.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [configurar um gateway de rede virtual para o ExpressRoute](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Certifique-se de utilizar `--gateway-type ExpressRoute`.

* Pode ligar até 10 redes virtuais para um circuito do ExpressRoute standard. Todas as redes virtuais tem de ser na mesma região geopolítica quando utilizar um circuito do ExpressRoute standard. 

* Se ativar o suplemento ExpressRoute premium, pode ligar uma rede virtual fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais para o circuito do ExpressRoute. Para mais informações sobre o suplemento premium, consulte o [FAQ](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição a um circuito

Pode ligar um gateway de rede virtual para um circuito do ExpressRoute, utilizando o exemplo. Certifique-se de que o gateway de rede virtual é criado e estiver pronto para ligar antes de executar o comando.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito

Pode partilhar um circuito ExpressRoute entre várias subscrições. A figura abaixo mostra uma simples schematic de funciona como partilha para circuitos do ExpressRoute entre várias subscrições.

Cada uma das nuvens inferior na nuvem grande é utilizada para representar subscrições que pertencem aos diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode utilizar as seus próprios subscrição para implementar os seus serviços-, mas pode partilhar um único circuito de ExpressRoute para ligar a sua rede no local. Um único departamento (neste exemplo: IT) pode ter o circuito ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito ExpressRoute.

> [!NOTE]
> Custos de largura de banda e de conectividade para o circuito dedicado serão aplicados ao proprietário do circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - circuito proprietários e utilizadores de circuito

'Proprietário do circuito' é um utilizador autorizado de energia do recurso de circuito ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por 'Circuito utilizadores'. Utilizadores de circuito são proprietários dos gateways de rede virtual que não estão na mesma subscrição que o circuito ExpressRoute. Utilizadores de circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações em qualquer altura. Quando uma autorização é revogada, todas as ligações de ligação são eliminadas da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Para criar uma autorização**

O proprietário do circuito cria uma autorização, que cria uma chave de autorização que pode ser utilizada por um utilizador do circuito para ligar os gateways de rede virtual para o circuito do ExpressRoute. Uma autorização é válida para apenas uma ligação.

O exemplo seguinte mostra como criar uma autorização:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A resposta contém a chave de autorização e o estado:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Para rever autorizações**

O proprietário do circuito pode rever todas as autorizações que são emitidas sobre um determinado circuito, executando o seguinte exemplo:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Para adicionar autorizações**

O proprietário do circuito pode adicionar autorizações utilizando o exemplo seguinte:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Para eliminar autorizações**

O proprietário do circuito pode revogar/eliminar autorizações ao utilizador executando o seguinte exemplo:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

O utilizador do circuito tem o ID de elemento de rede e uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de ligação**

O utilizador do circuito pode executar o exemplo a seguir para resgatar uma autorização de ligação:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para libertar uma autorização de ligação**

Pode disponibilizar uma autorização ao eliminar a ligação que liga o circuito de ExpressRoute para a rede virtual.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).