---
title: "A verificar se conectividade: O ExpressRoute do Azure guia de resolução de problemas | Microsoft Docs"
description: "Esta página fornece instruções sobre a resolução de problemas e validar a conetividade ponto a ponto de um circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 960ea1540d644b6f41b95ab7df61cf91adcbb4ad
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="verifying-expressroute-connectivity"></a>Verificar a conectividade do ExpressRoute
O ExpressRoute, que expande uma rede no local para a nuvem da Microsoft através de uma ligação privada que é facilitada por um fornecedor de conectividade, envolve as zonas de rede distintos três seguintes:

-   Rede de cliente
-   Rede de fornecedor
-   Centro de dados do Microsoft

O objetivo deste documento é ajudar o utilizador identifique onde (ou mesmo) existe um problema de conectividade e, em que zona, deste modo, para procurar o ressarcimento de ajuda da equipa adequada para resolver o problema. Se necessitar de suporte da Microsoft para resolver um problema, abra um pedido de suporte com [Microsoft Support][Support].

> [!IMPORTANT]
> Este documento destina-se para ajudar a diagnosticar e corrigir problemas simples. Não se destina a ser uma substituição para o suporte da Microsoft. Abra um pedido de suporte com [Microsoft Support] [ Support] se não for possível resolver o problema utilizando a orientação fornecida.
>
>

## <a name="overview"></a>Descrição geral
O diagrama seguinte mostra a conectividade lógica de uma rede de cliente à rede da Microsoft com o ExpressRoute.
[![1]][1]

No diagrama anterior, os números indicam pontos chave de rede. Os pontos de rede são, muitas vezes, referenciados através deste artigo pelo respetivo número associado.

Consoante o modelo de conectividade do ExpressRoute (localização conjunta do Exchange na nuvem, ligação de Ethernet ponto a ponto ou qualquer a qualquer (VPN de IP)), os pontos de rede 3 e 4 poderá comutadores (dispositivos de camada 2). Os pontos de chave de rede ilustrados são os seguintes:

1.  Dispositivo de computação do cliente (por exemplo, um servidor ou computador)
2.  CEs: Routers de limite de cliente 
3.  PEs (direcionadas para o CE): fornecedor edge routers/comutadores que estão a enfrentar os routers de limite de cliente. Referido como PE CEs neste documento.
4.  PEs (com acesso do MSEE): fornecedor edge routers/comutadores que estão a enfrentar MSEEs. Referido como PE MSEEs neste documento.
5.  MSEEs: Routers de Microsoft Enterprise Edge (MSEE) ExpressRoute
6.  Gateway de rede virtual (VNet)
7.  Dispositivo no Azure VNet de computação

Se forem utilizados modelos de conetividade de localização conjunta de Exchange de nuvem ou de ligação de Ethernet ponto a ponto, o router de limite de cliente (2) seria estabelecer BGP peering com MSEEs (5). Pontos de rede 3 e 4 seriam ainda existem, mas ser um pouco transparente como dispositivos de camada 2.

Se for utilizado o modelo de conectividade qualquer a qualquer (VPN de IP), o PEs (com acesso do MSEE) (4) seria estabelecer BGP peering com MSEEs (5). Rotas seriam, em seguida, propagar novamente para a rede de cliente através da rede de fornecedor de serviço de VPN de IP.

>[!NOTE]
>Para elevada disponibilidade do ExpressRoute, Microsoft requer um par redundante de sessões de BGP entre os MSEEs (5) e PE-MSEEs (4). Um par redundante de caminhos de rede também é encouraged entre a rede de cliente e PE CEs. No entanto, no modelo de ligação de qualquer a qualquer (VPN de IP), um único dispositivo CE (2) pode ser ligado a um ou mais PEs (3).
>
>

Para validar um circuito do ExpressRoute, os seguintes passos são abrangidos (com o ponto de rede indicado pelo número associado):
1. [Validar o aprovisionamento e o estado (5)](#validate-circuit-provisioning-and-state)
2. [Validar o ExpressRoute, pelo menos, um peering é configurado (5)](#validate-peering-configuration)
3. [Validar ARP entre a Microsoft e o serviço do fornecedor (ligação entre 4 e 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Validar o BGP e rotas do MSEE (BGP entre 4 a 5 e 5 para 6 se estiver ligada uma VNet)](#validate-bgp-and-routes-on-the-msee)
5. [Verifique as estatísticas de tráfego (tráfego de passar pela 5)](#check-the-traffic-statistics)

Mais validações e verificações serão adicionadas novamente a verificação de futura, mensalmente!

##<a name="validate-circuit-provisioning-and-state"></a>Validar e estado de aprovisionamento
Independentemente do modelo de conectividade, um circuito ExpressRoute tem de ser criado e, por conseguinte, uma chave de serviço gerados para o aprovisionamento do circuito. Aprovisionamento de um circuito ExpressRoute estabelece uma ligações de camada 2 redundantes entre PE-MSEEs (4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, aprovisionar e certifique-se de um circuito do ExpressRoute, consulte o artigo [criar e modificar um circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito ExpressRoute. Esta chave é necessária para a maioria dos comandos do powershell mencionados neste documento. Além disso, se necessitar de assistência da Microsoft ou de um parceiro para resolver um problema de ExpressRoute, forneça a chave do serviço para identificar prontamente o circuito do ExpressRoute.
>
>

###<a name="verification-via-the-azure-portal"></a>Verificação através do portal do Azure
No portal do Azure, o estado de um circuito ExpressRoute pode ser verificado selecionando ![2][2] no menu da barra de lado esquerdo e, em seguida, selecionar o circuito ExpressRoute. Selecionar um ExpressRoute circuito listado em "Todos os recursos" abre o painel de circuito do ExpressRoute. No ![3][3] secção do painel, ExpressRoute essentials estão listado como mostrado na captura de ecrã seguinte:

![4][4]    

No ExpressRoute Essentials, *circuito estado* indica o estado do circuito no lado de Microsoft. *Estado do fornecedor* indica se o circuito ter sido *aprovisionado/não aprovisionado* no lado do fornecedor de serviços. 

Para um circuito de ExpressRoute estar operacional, o *circuito estado* tem de ser *ativado* e *Estado fornecedor* tem de ser *aprovisionado*.

>[!NOTE]
>Se o *circuito estado* não é ativada, contacte [Microsoft Support][Support]. Se o *Estado fornecedor* não é aprovisionado, contacte o fornecedor de serviço.
>
>

###<a name="verification-via-powershell"></a>Verificação através do PowerShell
Para listar todos os dos circuitos ExpressRoute num grupo de recursos, utilize o seguinte comando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Pode obter o nome do grupo de recursos através do Azure. Consulte a subsecção anterior deste documento e tenha em atenção que o nome do grupo de recursos está listado na captura de ecrã de exemplo.
>
>

Para selecionar um determinado circuito de ExpressRoute num grupo de recursos, utilize o seguinte comando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Uma resposta de amostra é:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Para confirmar se um circuito ExpressRoute está operacional, preste especial atenção aos seguintes campos:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Se o *CircuitProvisioningState* não é ativada, contacte [Microsoft Support][Support]. Se o *ServiceProviderProvisioningState* não é aprovisionado, contacte o fornecedor de serviço.
>
>

###<a name="verification-via-powershell-classic"></a>Verificação através do PowerShell (clássica)
Para listar todos os circuitos ExpressRoute numa subscrição, utilize o seguinte comando:

    Get-AzureDedicatedCircuit

Para selecionar um determinado circuito do ExpressRoute, utilize o seguinte comando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Uma resposta de amostra é:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Para confirmar se um circuito ExpressRoute está operacional, preste especial atenção aos seguintes campos: ServiceProviderProvisioningState: Estado aprovisionado: ativado

>[!NOTE]
>Se o *estado* não é ativada, contacte [Microsoft Support][Support]. Se o *ServiceProviderProvisioningState* não é aprovisionado, contacte o fornecedor de serviço.
>
>

##<a name="validate-peering-configuration"></a>Validar a configuração do Peering
Depois do fornecedor de serviços foi concluído o aprovisionamento do circuito do ExpressRoute, uma configuração de encaminhamento é possível criar o circuito de ExpressRoute entre MSEE-PRs (4) e MSEEs (5). Cada circuito de ExpressRoute pode ter um, dois ou três contextos de encaminhamento ativados: Azure privado peering (o tráfego para redes virtuais privadas no Azure), Azure público peering (para endereços IP públicos no Azure) e o Microsoft peering (tráfego ao Office 365 e Dynamics 365). Para obter mais informações sobre como criar e modificar a configuração de encaminhamento, consulte o artigo [criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].

###<a name="verification-via-the-azure-portal"></a>Verificação através do portal do Azure

>[!NOTE]
>Se camada 3 é fornecida pelo fornecedor de serviço e os peerings em branco no portal, atualize a configuração do circuito com o botão de atualização no protal. Esta operação será aplicada a configuração de encaminhamento correta no seu circuito. 
>
>

No portal do Azure, o estado de um circuito ExpressRoute pode ser verificado selecionando ![2][2] no menu da barra de lado esquerdo e, em seguida, selecionar o circuito ExpressRoute. Selecionar ExpressRoute circuito listado em "Todos os recursos" seria abrir o painel de circuito do ExpressRoute. No ![3][3] secção do painel, ExpressRoute essentials deverá estar listado conforme mostrado na captura de ecrã seguinte:

![5][5]

No exemplo anterior, como o Azure apontou contexto de encaminhamento de peering privado está ativado, enquanto público do Azure e contextos de encaminhamento peering Microsoft não estão ativados. Um contexto de peering ativado com êxito também teria as sub-redes de primária e secundária-to-Point (necessário para BGP) listadas. O/30 sub-redes são utilizadas para o endereço IP da interface do MSEEs e PE MSEEs. 

>[!NOTE]
>Se um peering não estiver ativado, verifique se as sub-redes primários e secundários atribuídas corresponde à configuração no PE MSEEs. Se não, para alterar a configuração nos MSEE routers, consulte [criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering]
>
>

###<a name="verification-via-powershell"></a>Verificação através do PowerShell
Para obter os detalhes de configuração de peering de privado do Azure, utilize os seguintes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt

Uma resposta de exemplo, para um configurados com êxito peering privado, é:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Um contexto de peering ativado com êxito teria os prefixos de endereço primária e secundária listados. O/30 sub-redes são utilizadas para o endereço IP da interface do MSEEs e PE MSEEs.

Para obter os detalhes de configuração de peering de público do Azure, utilize os seguintes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

Para obter os detalhes de configuração de peering do Microsoft, utilize os seguintes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

Se não estiver configurado um peering, poderia ser uma mensagem de erro. Uma resposta de exemplo, quando o declarados peering (Azure público peering neste exemplo) não está configurado no circuito:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Se um peering não estiver ativado, verifique se as sub-redes primários e secundários atribuídas correspondem a configuração do PE-MSEE ligado. Verifique também se o correto *VlanId*, *AzureASN*, e *PeerASN* são utilizadas em MSEEs e se estes valores mapeia para as utilizadas do PE-MSEE ligado. Se for escolhido o hash MD5, a chave partilhada deve ser igual no MSEE e PE MSEE par. Para alterar a configuração nos routers MSEE, consulte [criar e modificar o encaminhamento para um circuito ExpressRoute] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verificação através do PowerShell (clássica)
Para obter os detalhes de configuração de peering de privado do Azure, utilize o seguinte comando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

É uma resposta de exemplo, para um peering privado configurados com êxito:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

A ativada com êxito, contexto peering teria as sub-redes de elemento de rede principal e secundário listadas. O/30 sub-redes são utilizadas para o endereço IP da interface do MSEEs e PE MSEEs.

Para obter os detalhes de configuração de peering de público do Azure, utilize os seguintes comandos:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Para obter os detalhes de configuração de peering do Microsoft, utilize os seguintes comandos:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Se tiverem sido definidas peerings de camada 3 pelo fornecedor de serviço, definir os peerings de ExpressRoute através do portal ou PowerShell substitui as definições do fornecedor de serviço. A repor as definições de peering do fornecedor do lado requer o suporte de fornecedor de serviços. Só deve modificar os peerings de ExpressRoute se determinados-se de que o fornecedor de serviço está a fornecer os serviços de camada 2 apenas!
>
>

<p/>
>[!NOTE]
>Se um peering não estiver ativado, verifique se as sub-redes de elemento de rede principal e secundário atribuídas correspondem a configuração do PE-MSEE ligado. Verifique também se o correto *VlanId*, *AzureAsn*, e *PeerAsn* são utilizadas em MSEEs e se estes valores mapeia para as utilizadas do PE-MSEE ligado. Para alterar a configuração nos routers MSEE, consulte [criar e modificar o encaminhamento para um circuito ExpressRoute] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validar ARP entre a Microsoft e o fornecedor de serviços
Esta secção utiliza comandos do PowerShell (clássica). Se o que estiver a utilizar comandos do PowerShell do Azure Resource Manager, certifique-se de que tem acesso de administrador/coadministrador na subscrição. Para resolução de problemas com o Azure Resource Manager comandos, consulte o [ARP ao obter as tabelas num modelo de implementação Resource Manager] [ ARP] documento.

>[!NOTE]
>Para obter ARP, o portal do Azure e os comandos do PowerShell do Azure Resource Manager podem ser utilizados. Se forem encontrados erros com os comandos do PowerShell do Azure Resource Manager, os comandos do PowerShell clássicos deverão funcionar como Classic PowerShell comandos também funcionam com circuitos ExpressRoute do Azure Resource Manager.
>
>

Para obter a tabela de ARP do router MSEE primário para o peering privado, utilize o seguinte comando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Um exemplo de resposta para o comando, no cenário com êxito:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Da mesma forma, pode verificar a tabela de ARP do MSEE no *primário*/*secundário* caminho, para *privada*/*pública*/*Microsoft* peerings.

O exemplo seguinte mostra que a resposta do comando para um peering não existe.

    ARP Info:
       
>[!NOTE]
>Se a tabela de ARP não tem endereços IP das interfaces mapeados para os endereços MAC, reveja as seguintes informações:
>1. Se o primeiro endereço IP de/30 sub-rede atribuída para a ligação entre o MSEE ELI e MSEE é utilizada na interface do MSEE PR. Azure utiliza sempre o segundo endereço IP para MSEEs.
>2. Verifique se o cliente (C-Tag) e as etiquetas VLAN de serviço (S-Tag) coincidem com ambos num par de MSEE ELI e MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar o BGP e rotas do MSEE
Esta secção utiliza comandos do PowerShell (clássica). Se o que estiver a utilizar comandos do PowerShell do Azure Resource Manager, certifique-se de que tem acesso de administrador/coadministrador na subscrição.

>[!NOTE]
>Para obter informações de BGP, o portal do Azure e os comandos do PowerShell do Azure Resource Manager podem ser utilizados. Se forem encontrados erros com os comandos do PowerShell do Azure Resource Manager, os comandos do PowerShell clássicos deverão funcionar como PowerShell clássico comandos também funcionam com circuitos ExpressRoute do Azure Resource Manager.
>
>

Para obter a tabela de encaminhamento (vizinho BGP) resumo para um contexto de encaminhamento específico, utilize o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Um exemplo de resposta é:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Como é mostrado no exemplo anterior, o comando é útil para determinar para quanto o contexto de encaminhamento foi estabelecida. Também indica o número de prefixos de rotas anunciadas pelo peering router.

>[!NOTE]
>Se o estado está a ser ativo ou inativo, verifique se as sub-redes de elemento de rede principal e secundário atribuídas correspondem a configuração do PE-MSEE ligado. Verifique também se o correto *VlanId*, *AzureAsn*, e *PeerAsn* são utilizadas em MSEEs e se estes valores mapeia para as utilizadas do PE-MSEE ligado. Se for escolhido o hash MD5, a chave partilhada deve ser igual no MSEE e PE MSEE par. Para alterar a configuração nos routers MSEE, consulte [criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Se determinados destinos não estão acessíveis através de um peering específica, consulte a tabela de rotas de MSEEs que pertencem ao contexto de peering específico. Se um prefixo correspondente (pode ser NATed IP) está presente na tabela de encaminhamento, em seguida, verifique se existem firewalls / / ACLs do NSG no caminho e se permitem o tráfego.
>
>

Para obter a tabela de encaminhamento completa do MSEE no *primário* caminho específico *privada* encaminhamento contexto, utilize o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Um resultado de êxito de exemplo para o comando é:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Da mesma forma, pode verificar a tabela de encaminhamento do MSEE no *primário*/*secundário* caminho, para *privada*/*pública*/*Microsoft* um contexto de peering.

O exemplo seguinte mostra que a resposta do comando para um peering não existe:

    Route Table Info:

##<a name="check-the-traffic-statistics"></a>Verifique as estatísticas de tráfego
Para obter as estatísticas de tráfego de caminho principal e secundário combinado – bytes e terminar – de um contexto de peering, utilizam o seguinte comando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Um resultado de exemplo do comando é:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Um resultado de exemplo do comando para um peering inexistente é:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações ou ajuda, consulte as ligações seguintes:

- [Suporte da Microsoft][Support]
- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento para um circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "conectividade do Expressroute lógica"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ícone do todos os recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone de descrição geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Captura de ecrã de exemplo de ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Captura de ecrã de exemplo de ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[OldPortal]: https://manage.windowsazure.com
[ARP]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






