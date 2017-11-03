---
title: "Ligar uma rede virtual a um circuito de ExpressRoute: PowerShell: clássico: Azure | Microsoft Docs"
description: "Este documento fornece uma descrição geral de como ligar redes virtuais (VNets) para circuitos do ExpressRoute, utilizando o modelo de implementação clássica e o PowerShell."
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: ganesr
ms.openlocfilehash: 8df8a4c6ff0897c821e13248e0494b17e1a4992d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Ligar uma rede virtual para um circuito ExpressRoute com o PowerShell (clássica)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo irá ajudá-lo a ligar redes virtuais (VNets) para circuitos ExpressRoute do Azure utilizando o modelo de implementação clássica e o PowerShell. Redes virtuais podem ser na mesma subscrição ou podem fazer parte de outra subscrição.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração
1. Tem a versão mais recente dos módulos do PowerShell do Azure. Pode transferir os módulos do PowerShell mais recentes da secção do PowerShell a [página de transferências do Azure](https://azure.microsoft.com/downloads/). Siga as instruções em [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar o computador para utilizar os módulos do PowerShell do Azure.
2. Tem de rever o [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
3. Deve ter um circuito ExpressRoute ativo.
   * Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao seu fornecedor de conectividade ativar o circuito.
   * Certifique-se de que tem o peering privado do Azure configurada para o seu circuito. Consulte o [configurar encaminhamento](expressroute-howto-routing-classic.md) artigo para obter instruções de encaminhamento.
   * Certifique-se de que o peering privado do Azure está configurado e o peering de BGP entre a rede e a Microsoft está a funcionar, de modo a que pode ativar a conetividade de ponto a ponto.
   * Tem de ter uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [configurar uma rede virtual para o ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Pode ligar até 10 redes virtuais para um circuito ExpressRoute. Todas as redes virtuais tem de ser na mesma região geopolítica. Pode ligar um grande número de redes virtuais para o circuito do ExpressRoute e as redes virtuais de ligação que se encontrem noutras regiões geopolíticas, se tiver ativado o suplemento ExpressRoute premium. Verifique o [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição a um circuito
Pode ligar uma rede virtual para um circuito ExpressRoute com o cmdlet seguinte. Certifique-se de que o gateway de rede virtual é criado e estiver pronto para ligar antes de executar o cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Pode partilhar um circuito ExpressRoute entre várias subscrições. A figura seguinte mostra um simples schematic de funciona como partilha para circuitos do ExpressRoute entre várias subscrições.

Cada uma das nuvens inferior na nuvem grande é utilizada para representar subscrições que pertencem aos diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode utilizar as seus próprios subscrição para implementar os seus serviços - mas departamentos pode partilhar um único circuito de ExpressRoute para ligar a sua rede no local. Um único departamento (neste exemplo: IT) pode ter o circuito ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito ExpressRoute.

> [!NOTE]
> Custos de largura de banda e de conectividade para o circuito dedicado serão aplicados ao proprietário do circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração
O *proprietário do circuito* é o administrador/coadministrador da subscrição na qual o circuito ExpressRoute é criado. O proprietário do circuito pode autorizar administradores/coadministrators de outras subscrições, referidas como *circuito utilizadores*, para utilizar o circuito dedicado que possuem. Os utilizadores de circuito que estão autorizados a utilizar o circuito de ExpressRoute da organização podem ligar a rede virtual na sua subscrição para o circuito de ExpressRoute depois estiverem autorizadas.

O proprietário do circuito tem a capacidade de modificar e revogar autorizações em qualquer altura. Revogar uma autorização resultará em todas as ligações que está a ser eliminadas da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Criar uma autorização**

Os administradores de outras subscrições para utilizar o circuito especificado autoriza o proprietário do circuito. No exemplo seguinte, o administrador do circuito (Contoso TI), permite que o administrador de outra subscrição (Dev-teste) para ligar até duas redes virtuais ao circuito. O administrador de TI da Contoso permite isto, especificando o ID de teste de desenvolvimento Microsoft. O cmdlet não enviar e-mail para o ID da Microsoft especificado. O proprietário do circuito tem de notificar explicitamente o outro proprietário da subscrição que a autorização está concluída.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Rever autorizações**

O proprietário do circuito pode rever todas as autorizações que são emitidas sobre um determinado circuito, executando o seguinte cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Atualizar autorizações**

O proprietário do circuito pode modificar autorizações utilizando o cmdlet seguinte:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Eliminar autorizações**

O proprietário do circuito pode revogar/eliminar autorizações ao utilizador executando o seguinte cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

**Rever autorizações**

O utilizador do circuito pode rever autorizações utilizando o cmdlet seguinte:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Resgatar autorizações de ligações**

O utilizador do circuito pode executar o cmdlet seguinte para resgatar uma autorização de ligação:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Execute este comando na subscrição recentemente ligada para a rede virtual:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).

