---
title: Resolver problemas de rotas - PowerShell | Microsoft Docs
description: "Saiba como resolver problemas de rotas no modelo de implementação Azure Resource Manager com o Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 29823708b2d26a383b76e371499859e57f470c6f
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Resolver problemas de rotas com o Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
> 
> 

Se ocorrerem problemas de conectividade de rede para ou da sua máquina Virtual do Azure (VM), as rotas podem estar a afetar os fluxos de tráfego da VM. Este artigo fornece uma descrição geral das funcionalidades de diagnóstico para rotas para ajudar a resolver problemas mais.

As tabelas de rotas estão associadas a sub-redes e são eficazes em todas as interfaces de rede (NIC) nessa sub-rede. Os seguintes tipos de rotas podem ser aplicados a cada interface de rede:

* **Rotas de sistema:** por predefinição, cada sub-rede criada numa rede Virtual do Azure (VNet) tem as tabelas de rotas de sistema permitem tráfego de VNet local, o tráfego através de gateways de VPN no local e o tráfego de Internet. Também existem rotas de sistema para VNets em modo de peering.
* **Rotas BGP:** propagadas para interfaces de rede através do ExpressRoute ou ligações de VPN de site para site. Saiba mais sobre o encaminhamento de BGP ao ler o [BGP com gateways de VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) e [descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md) artigos.
* **As rotas definidas pelo utilizador (UDR):** se estiver a utilizar dispositivos de rede virtual ou são forçada do túnel de tráfego para uma rede no local através de uma VPN de site a site, pode ter definido pelo utilizador rotas (UDRs) associadas à sua tabela de rota de sub-rede. Se não estiver familiarizado com UDRs, leia o [rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined) artigo.

Com as rotas vários que podem ser aplicadas a uma interface de rede, pode ser difícil determinar que rotas agregadas são eficazes. Para ajudar a resolver problemas de conectividade de rede VM, pode ver todas as rotas eficazes para uma interface de rede no modelo de implementação Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Utilizar rotas efetivas para resolver o fluxo de tráfego da VM
Este artigo utiliza o cenário seguinte como exemplo para ilustrar como resolver problemas com as rotas efetivas de uma interface de rede:

Uma VM (*VM1*) ligado para a VNet (*VNet1*, prefixo: 10.9.0.0/16) não consegue ligar a um VM(VM3) numa VNet em modo de peering recentemente (*VNet3*, prefixo 10.10.0.0/16). Existem não UDRs ou BGP encaminha aplicada à interface de rede VM1 NIC1 ligado à VM, apenas as rotas de sistema são aplicadas.

Este artigo explica como determinar a causa da falha de ligação, utilizar a capacidade de rotas efetivas no modelo de implementação de gestão de recursos do Azure.
Enquanto o exemplo utiliza apenas as rotas de sistema, os mesmos passos podem ser utilizados para determinar as falhas de ligação de entrada e saída através de qualquer tipo de rota.

> [!NOTE]
> Se a VM tem mais do que um NIC anexado, verifique as rotas em vigor para cada um dos NICs para diagnosticar problemas de conectividade de rede de e para uma VM.
> 
> 

### <a name="view-effective-routes-for-a-virtual-machine"></a>Vista rotas eficazes para uma máquina virtual
Para ver as rotas de agregação que são aplicadas a uma VM, conclua os seguintes passos:

### <a name="view-effective-routes-for-a-network-interface"></a>Vista rotas eficazes para uma interface de rede
Para ver as rotas de agregação que são aplicadas a uma interface de rede, execute os seguintes passos:

1. Inicie uma sessão do PowerShell do Azure e iniciar sessão no Azure. Se não estiver familiarizado com o Azure PowerShell, leia o [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. O comando seguinte devolve todas as rotas aplicadas a uma interface de rede com o nome *VM1 NIC1* no grupo de recursos *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Se não souber o nome de uma interface de rede, escreva o seguinte comando para obter os nomes de todas as interfaces de rede no group.* recurso
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   O seguinte resultado semelhante à saída de cada rota de aplicado à sub-rede que a NIC está ligada a:
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Tenha em atenção o seguinte no resultado:
   
   * **Nome**: nome da rota efetiva pode estar vazia, a menos que especificado explicitamente para rotas definidas pelo utilizador. 
   * **Estado**: indica o estado da rota em vigor. Os valores possíveis são "Active" ou "Inválido."
   * **AddressPrefixes**: Especifica o prefixo de endereço da rota Efetivo em notação CIDR. 
   * **nextHopType**: indica o salto seguinte para a rota fornecida. Os valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, ou *nulo*. Um valor de *nulo* para **nextHopType** num UDR pode indicar uma rota inválida. Por exemplo, se **nextHopType** é *VirtualAppliance* e a aplicação virtual de rede VM não se encontra num Estado aprovisionado/em execução. Se **nextHopType** é *o VPNGateway* e não existe nenhum gateway aprovisionado/em execução na VNet determinada, a rota pode tornar-se inválida.
   * **O NextHopIpAddress**: Especifica o endereço IP do próximo salto da rota em vigor.
   
   O comando seguinte devolve as rotas num mais fácil de ver a tabela:
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   O resultado seguinte é algumas do resultado recebido para o cenário descrito anteriormente:
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Não há nenhuma rota listada para o *WestUS VNet3* VNet (prefixo 10.10.0.0/16)** de *WestUS VNet1* (prefixo 10.9.0.0/16) na saída do passo anterior. Como é mostrado na imagem seguinte, o VNet peering ligação com o *WestUS VNet3* VNet está a ser o *desligado* estado.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    A ligação de bidirecional para o peering é interrompido, o qual explica por que motivo VM1 não foi possível ligar ao VM3 no *WestUS VNet3* VNet. Configurar uma ligação para o novo bidirecional VNet peering *WestUS VNet1* e *WestUS VNet3* VNets. O resultado devolvido estabelecido a ligação VNet peering é corretamente segue:
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    Depois de determinar o problema, pode adicionar, remover, ou altere as rotas e tabelas de rotas. Escreva o seguinte comando para ver uma lista de comandos utilizada para o fazer:
   
        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Considerações
Alguns aspetos a lembrar quando rever a lista de rotas devolvido:

* Encaminhamento baseia-se em mais longo do prefixo da correspondência (LPM) entre UDRs, as rotas BGP e sistema. Se existir mais do que uma rota com a mesma correspondência LPM, em seguida, uma rota é selecionada com base na origem pela seguinte ordem:
  
  * Rota definida pelo utilizador
  * Rota BGP
  * Rota de sistema (predefinição)
    
    Com rotas efetivas, pode vê apenas rotas efetivas que estão a correspondência LPM com base em todas as rotas de disponíveis. Por que mostra como as rotas, na verdade, são avaliadas para um determinado NIC, isto torna muito mais fácil solucionar rotas específicas que poderão estar a afetar a conectividade de/para a VM.
* Se tiver UDRs e estiver a enviar o tráfego para uma aplicação virtual de rede (NVA), com *VirtualAppliance* como **nextHopType**, certifique-se de que o reencaminhamento IP está ativado no NVA receber o tráfego de ou pacotes serem largados. 
* Se forçar o túnel estiver ativado, será encaminhado para o local todo o tráfego de saída da Internet. RDP/SSH da Internet para a VM pode não funcionar com esta definição, dependendo da forma como no local processa este tráfego. 
  Túnel forçado pode ser ativado:
  * Se utilizar VPN de site a site, definindo uma rota definida pelo utilizador (UDR) com o nextHopType como Gateway de VPN
  * Se uma rota predefinida é anunciada através do BGP
* Para o tráfego da VNet peering funcione corretamente, uma rota de sistema com **nextHopType** *VNetPeering* tem de existir durante o intervalo de prefixo da VNet em modo de peering. Se não existe uma rota deste tipo e a ligação VNet peering resultados parecem estar OK:
  * Aguarde alguns segundos e repita o se se tratar de uma ligação de peering recentemente estabelecida. Ocasionalmente, demora tempo para propagar rotas para todas as interfaces de rede numa sub-rede.
  * Regras do grupo de segurança de rede (NSG) podem estar a afetar os fluxos de tráfego. Para obter mais informações, consulte o [resolver problemas relacionados com grupos de segurança de rede](virtual-network-nsg-troubleshoot-powershell.md) artigo.

