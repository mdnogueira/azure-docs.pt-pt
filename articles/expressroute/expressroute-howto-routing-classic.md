---
title: "Como configurar o encaminhamento circuito (peering) para o ExpressRoute: Azure: clássica | Microsoft Docs"
description: "Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 37713db70f3ae837edafc997b78b16b121d0a885
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Criar e modificar o peering de um circuito de ExpressRoute (clássica)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - privada peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering público](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Este artigo explica os passos para criar e gerir a configuração de encaminhamento para um circuito ExpressRoute com o PowerShell e o modelo de implementação clássica. Os passos abaixo também irão mostrar como verificar o estado, atualizar ou eliminar e retirar o aprovisionamento do peerings para um circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração
* Terá da versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, consulte [introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview).  
* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) e tem de ativar o circuito pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute tem de estar num estado aprovisionado e ativado para que seja capaz de executar os cmdlets descritos abaixo.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que fornecem serviços de conectividade de Camada 2. Se estiver a utilizar um fornecedor de serviço que oferece serviços geridos de Camada 3 (normalmente, um VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.
> 
> 

Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Inicie sessão na sua conta do Azure e selecionar uma subscrição
1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

        Login-AzureRmAccount

2. Verifique as subscrições da conta.

        Get-AzureRmSubscription

3. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Em seguida, utilize o cmdlet seguinte para adicionar a sua subscrição do Azure para o PowerShell para o modelo de implementação clássica.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Peering privado do Azure
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure
1. **Importe o módulo do PowerShell para ExpressRoute.**
   
    Tem de importar os módulos do Azure e ExpressRoute para a sessão do PowerShell para começar a utilizar os cmdlets ExpressRoute. Execute os seguintes comandos para importar os módulos do Azure e ExpressRoute para a sessão do PowerShell.  
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Crie um circuito ExpressRoute.**
   
    Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo. 
3. **Verifique o circuito de ExpressRoute para garantir que está aprovisionado.**
   
    Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado. Veja o exemplo abaixo.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Certifique-se de que o circuito é apresentado como aprovisionado e ativado. Se não, trabalhar com o seu fornecedor de conectividade para obter o seu circuito para o estado e estado necessário.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configure o peering privado do Azure para o circuito.**
   
    Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
   * Uma sub-rede /30 para a ligação primária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Assegure que não está a utilizar 65515.
   * Um hash MD5 se optar por utilizar um. **Isto é opcional**.
     
    Pode executar o cmdlet seguinte para configurar o peering privado o Azure para o seu circuito.
     
        Novo AzureBGPPeering - AccessType privada - ServiceKey "***" - PrimaryPeerSubnet "10.0.0.0/30" - SecondaryPeerSubnet "10.0.0.4/30" - PeerAsn 1234 - VlanId 100
     
    Pode utilizar o cmdlet abaixo se optar por utilizar um hash MD5.
     
        Novo AzureBGPPeering - AccessType privada - ServiceKey "***" - PrimaryPeerSubnet "10.0.0.0/30" - SecondaryPeerSubnet "10.0.0.4/30" - PeerAsn 1234 - VlanId 100 - SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Para ver os detalhes do peering privado do Azure
Pode obter os detalhes de configuração com o cmdlet seguinte

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Para atualizar a configuração do peering privado do Azure
Pode atualizar qualquer parte da configuração com o cmdlet seguinte. No exemplo abaixo, o ID de VLAN do circuito está a ser atualizado de 100 para 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Para eliminar um peering privado do Azure
Pode remover a sua configuração de peering executando o cmdlet seguinte.

> [!WARNING]
> Tem de assegurar que todas as redes virtuais são desassociadas do circuito ExpressRoute antes de executar este cmdlet. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Peering público do Azure
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure
1. **Importe o módulo do PowerShell para ExpressRoute.**
   
    Tem de importar os módulos do Azure e ExpressRoute para a sessão do PowerShell para começar a utilizar os cmdlets ExpressRoute. Execute os seguintes comandos para importar os módulos do Azure e ExpressRoute para a sessão do PowerShell. 
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Crie um circuito do ExpressRoute**
   
    Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering público do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
3. **Verifique o circuito de ExpressRoute para garantir que está aprovisionado**
   
    Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado. Veja o exemplo abaixo.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Certifique-se de que o circuito é apresentado como aprovisionado e ativado. Se não, trabalhar com o seu fornecedor de conectividade para obter o seu circuito para o estado e estado necessário.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configure o peering público do Azure para o circuito**
   
    Assegure que tem as seguintes informações antes de prosseguir.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Um hash MD5 se optar por utilizar um. **Isto é opcional**.
     
    Pode executar o cmdlet seguinte para configurar o peering público do Azure para o seu circuito
     
        Novo AzureBGPPeering - AccessType público - ServiceKey "***" - PrimaryPeerSubnet "131.107.0.0/30" - SecondaryPeerSubnet "131.107.0.4/30" - PeerAsn 1234 - VlanId 200
     
    Pode utilizar o cmdlet abaixo se optar por utilizar um hash MD5
     
        Novo AzureBGPPeering - AccessType público - ServiceKey "***" - PrimaryPeerSubnet "131.107.0.0/30" - SecondaryPeerSubnet "131.107.0.4/30" - PeerAsn 1234 - VlanId 200 - SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Assegure que especifica o seu número AS como ASN de peering e não cliente ASN.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Para ver os detalhes do peering público do Azure
Pode obter os detalhes de configuração com o cmdlet seguinte

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Para atualizar a configuração do peering público do Azure
Pode atualizar qualquer parte da configuração com o cmdlet seguinte

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

O ID de VLAN do circuito está a ser atualizado de 200 para 600, no exemplo acima.

### <a name="to-delete-azure-public-peering"></a>Para eliminar um peering público do Azure
Pode remover a sua configuração de peering executando o cmdlet seguinte

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Peering da Microsoft
Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering da Microsoft para um circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft
1. **Importe o módulo do PowerShell para ExpressRoute.**
   
    Tem de importar os módulos do Azure e ExpressRoute para a sessão do PowerShell para começar a utilizar os cmdlets ExpressRoute. Execute os seguintes comandos para importar os módulos do Azure e ExpressRoute para a sessão do PowerShell.  
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Crie um circuito do ExpressRoute**
   
    Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
3. **Verifique o circuito de ExpressRoute para garantir que está aprovisionado**
   
    Primeiro tem de verificar se o circuito ExpressRoute está num Estado aprovisionado e ativado.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Certifique-se de que o circuito é apresentado como aprovisionado e ativado. Se não, trabalhar com o seu fornecedor de conectividade para obter o seu circuito para o estado e estado necessário.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configurar o peering da Microsoft para o circuito**
   
    Confirme que tem as seguintes informações antes de continuar.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Pode enviar uma lista separada por vírgulas se pretender enviar um conjunto de prefixos. Estes prefixos têm de ser registados para si num RIR/TIR.
   * Cliente ASN: se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados. **Isto é opcional**.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * Um hash MD5, se optar por utilizar um. **Isto é opcional.**
     
    Pode executar o cmdlet seguinte para configurar pering da Microsoft para o seu circuito
     
        Novo AzureBGPPeering - AccessType Microsoft - ServiceKey "***" - PrimaryPeerSubnet "131.107.0.0/30" - SecondaryPeerSubnet "131.107.0.4/30" - VlanId 300 - PeerAsn 1234 - CustomerAsn 2245 - AdvertisedPublicPrefixes "123.0.0.0/30" - RoutingRegistryName "ARIN" - SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Para ver os detalhes do peering da Microsoft
Pode obter os detalhes de configuração com o cmdlet seguinte.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Para atualizar a configuração do peering da Microsoft
Pode atualizar qualquer parte da configuração com o cmdlet seguinte.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Para eliminar o peering da Microsoft
Pode remover a sua configuração de peering executando o cmdlet seguinte.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Passos seguintes
Em seguida, [ligar uma VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Para obter mais informações sobre fluxos de trabalho, consulte [fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).

