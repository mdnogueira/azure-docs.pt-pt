---
title: Como configurar o encaminhamento para um circuito ExpressRoute | Microsoft Docs
description: "Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito."
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/05/2016
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 5da660fc36ac0ebef26ed815da6dbba6668643d3
ms.openlocfilehash: 57e8ee6f067a61c1262a8902dd56393e4803c3c6


---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Criar e modificar o encaminhamento de um circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure - Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell – Resource Manager](expressroute-howto-routing-arm.md)
> * [PowerShell – Clássica](expressroute-howto-routing-classic.md)
> 
> 

Este artigo vai orientá-lo nos passos para criar e gerir a configuração de encaminhamento de um circuito ExpressRoute com o PowerShell e o modelo de implementação Azure Resource Manager.  Os passos abaixo também irão mostrar como verificar o estado, atualizar ou eliminar e retirar o aprovisionamento do peerings para um circuito ExpressRoute. 

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração
* Vai precisar da versão mais recente dos módulos do Azure PowerShell, versão 1.0 ou posterior. 
* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute tem de estar num estado aprovisionado e ativado para que seja capaz de executar os cmdlets descritos abaixo.

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviço que oferece serviços geridos de Camada 3 (normalmente, um VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.

> [!IMPORTANT]
> Atualmente, não estamos a anunciar peerings configuradas por fornecedores de serviços através do portal de gestão do serviço. Estamos a trabalhar para ativar essa capacidade brevemente. Consulte o seu fornecedor de serviços antes de configurar os peerings de BGP.
> 
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. 

## <a name="azure-private-peering"></a>Peering privado do Azure
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure
1. Importe o módulo do PowerShell para ExpressRoute.
   
     Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importe todos os módulos AzureRM.* dentro do intervalo da versão semântica conhecida
   
        Import-AzureRM
   
    Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida 
   
        Import-Module AzureRM.Network 
   
    Inicie a sessão na sua conta
   
        Login-AzureRmAccount
   
    Selecione a subscrição com a qual pretende criar o circuito ExpressRoute
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Crie um circuito ExpressRoute.
   
    Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. 
   
    Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo. 
3. Verifique o circuito ExpressRoute para garantir que está aprovisionado.
   
    Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado. Veja o exemplo abaixo.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    A resposta será algo semelhante ao exemplo abaixo:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []
4. Configure o peering privado do Azure para o circuito.
   
    Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
   * Uma sub-rede /30 para a ligação primária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
   * Um hash MD5 se optar por utilizar um. **Isto é opcional**.
     
     Pode executar o cmdlet seguinte para configurar o peering privado o Azure para o seu circuito.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     Pode utilizar o cmdlet abaixo se optar por utilizar um hash MD5.
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     > [!IMPORTANT]
     > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Para ver os detalhes do peering privado do Azure
Pode obter os detalhes de configuração com o cmdlet seguinte

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt    


### <a name="to-update-azure-private-peering-configuration"></a>Para atualizar a configuração do peering privado do Azure
Pode atualizar qualquer parte da configuração com o cmdlet seguinte. No exemplo abaixo, o ID de VLAN do circuito está a ser atualizado de 100 para 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Para eliminar um peering privado do Azure
Pode remover a sua configuração de peering executando o cmdlet seguinte.

> [!WARNING]
> Tem de assegurar que todas as redes virtuais são desassociadas do circuito ExpressRoute antes de executar este cmdlet. 
> 
> 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Peering público do Azure
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure
1. Importe o módulo do PowerShell para ExpressRoute.
   
     Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importe todos os módulos AzureRM.* dentro do intervalo da versão semântica conhecida
   
        Import-AzureRM
   
    Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida 
   
        Import-Module AzureRM.Network 
   
    Inicie a sessão na sua conta
   
        Login-AzureRmAccount
   
    Selecione a subscrição com a qual pretende criar o circuito ExpressRoute
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Crie um circuito ExpressRoute.
   
    Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. 
   
    Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering público do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
3. Verifique o circuito ExpressRoute para garantir que está aprovisionado.
   
    Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado. Veja o exemplo abaixo.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    A resposta será algo semelhante ao exemplo abaixo:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. Configure o peering público do Azure para o circuito.
   
    Assegure que tem as seguintes informações antes de prosseguir.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Um hash MD5 se optar por utilizar um. **Isto é opcional**.
     
     Pode executar o cmdlet seguinte para configurar o peering público do Azure para o seu circuito
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     Pode utilizar o cmdlet abaixo se optar por utilizar um hash MD5
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    > [!IMPORTANT]
    > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.


### <a name="to-view-azure-public-peering-details"></a>Para ver os detalhes do peering público do Azure
Pode obter os detalhes de configuração com o cmdlet seguinte

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Para atualizar a configuração do peering público do Azure
Pode atualizar qualquer parte da configuração com o cmdlet seguinte

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

O ID de VLAN do circuito está a ser atualizado de 200 para 600, no exemplo acima.

### <a name="to-delete-azure-public-peering"></a>Para eliminar um peering público do Azure
Pode remover a sua configuração de peering executando o cmdlet seguinte

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Peering da Microsoft
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering da Microsoft para um circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft
1. Importe o módulo do PowerShell para ExpressRoute.
   
     Instale o programa de instalação mais recente do PowerShell a partir da [Galeria do PowerShell](http://www.powershellgallery.com/) e importe os módulos do Azure Resource Manager para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Terá de executar o PowerShell como Administrador.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importe todos os módulos AzureRM.* dentro do intervalo da versão semântica conhecida
   
        Import-AzureRM
   
    Pode também simplesmente importar um módulo de seleção dentro do intervalo da versão semântica conhecida 
   
        Import-Module AzureRM.Network 
   
    Inicie a sessão na sua conta
   
        Login-AzureRmAccount
   
    Selecione a subscrição com a qual pretende criar o circuito ExpressRoute
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Crie um circuito ExpressRoute.
   
    Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao fornecedor de conectividade que o aprovisione. 
   
    Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
3. Verifique o circuito ExpressRoute para garantir que está aprovisionado.
   
    Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado. Veja o exemplo abaixo.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    A resposta será algo semelhante ao exemplo abaixo:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. Configure o peering da Microsoft para o circuito.
   
    Confirme que tem as seguintes informações antes de continuar.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Pode enviar uma lista separada por vírgulas se pretender enviar um conjunto de prefixos. Estes prefixos têm de ser registados para si num RIR/TIR.
   * Cliente ASN: se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados. **Isto é opcional**.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * Um hash MD5, se optar por utilizar um. **Isto é opcional.**
     
     Pode executar o cmdlet seguinte para configurar o peering da Microsoft para o seu circuito
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-get-microsoft-peering-details"></a>Para obter os detalhes do peering da Microsoft
Pode obter os detalhes de configuração com o cmdlet seguinte.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Para atualizar a configuração do peering da Microsoft
Pode atualizar qualquer parte da configuração com o cmdlet seguinte.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-microsoft-peering"></a>Para eliminar o peering da Microsoft
Pode remover a sua configuração de peering executando o cmdlet seguinte.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Passos seguintes
Passo seguinte, [Ligar uma VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).




<!--HONumber=Nov16_HO3-->


