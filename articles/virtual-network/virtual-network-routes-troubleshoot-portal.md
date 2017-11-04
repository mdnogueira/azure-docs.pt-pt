---
title: Resolver problemas de rotas - Portal | Microsoft Docs
description: "Saiba como resolver problemas de rotas no modelo de implementação Azure Resource Manager através do Portal do Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: f76693dfcaf5076372e4c4d5f28678c05eff85ed
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Resolver problemas de rotas através do Portal do Azure
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

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **máquinas virtuais** na lista que é apresentada.
3. Selecione uma VM para resolver problemas da lista que é apresentado e é apresentado o painel uma VM com as opções.
4. Clique em **diagnosticar & resolver problemas** e, em seguida, selecione um problema comum. Neste exemplo, **não consigo ligar à minha VM do Windows** está selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Os passos são apresentados sob o problema, conforme mostrado na imagem seguinte:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Clique em *rotas efetivas* na lista de passos recomendados.
6. O **rotas efetivas** é apresentado o painel, conforme mostrado na imagem seguinte:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Se a VM tem apenas um NIC, está selecionado por predefinição. Se tiver mais do que um NIC, selecione o NIC para o qual pretende ver as rotas efetivas.

   > [!NOTE]
   > Se a VM associada com a NIC não estiver num Estado de execução, rotas efetivas não serão apresentadas. Apenas as primeiros 200 rotas efetivas são apresentadas no portal. Para obter uma lista completa, clique em **transferir**. Pode continuar a filtrar os resultados do ficheiro. csv transferido.
   >
   >

    Tenha em atenção o seguinte no resultado:

   * **Origem**: indica o tipo de rota. Rotas de sistema são apresentadas como *predefinido*, UDRs são apresentadas como *utilizador* e rotas de gateway (estático ou BGP) são apresentadas como *o VPNGateway*.
   * **Estado**: indica o estado da rota em vigor. Os valores possíveis são *Active Directory* ou *inválido*.
   * **AddressPrefixes**: Especifica o prefixo de endereço da rota Efetivo em notação CIDR.
   * **nextHopType**: indica o salto seguinte para a rota fornecida. Os valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, ou *nulo*. Um valor de *nulo* para **nextHopType** num UDR pode indicar uma rota inválida. Por exemplo, se **nextHopType** é *VirtualAppliance* e a aplicação virtual de rede VM não se encontra num Estado aprovisionado/em execução. Se **nextHopType** é *o VPNGateway* e não existe nenhum gateway aprovisionado/em execução na VNet determinada, a rota pode tornar-se inválida.
7. Não há nenhuma rota listada para o *WestUS VNET3* VNet (prefixo 10.10.0.0/16) a partir de *WestUS VNet1* (prefixo 10.9.0.0/16) na imagem no passo anterior. Na imagem seguinte, a ligação peering está a ser o *desligado* Estado:

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    A ligação de bidirecional para o peering é interrompido, o qual explica por que motivo VM1 não foi possível ligar ao VM3 no *WestUS VNet3* VNet.
8. A imagem seguinte mostra as rotas depois de estabelecer a ligação de peering bidirecional:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Para cenários de resolução de problemas mais para avaliação forçada de túnel e route, leia o [considerações](virtual-network-routes-troubleshoot-portal.md#considerations) secção deste artigo.

### <a name="view-effective-routes-for-a-network-interface"></a>Vista rotas eficazes para uma interface de rede
Se o fluxo de tráfego de rede é afetado para uma determinada interface de rede (NIC), pode ver uma lista completa das rotas efetivas de um NIC diretamente. Para ver as rotas de agregação que são aplicadas a um NIC, conclua os seguintes passos:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **interfaces de rede**
3. Pesquise a lista para o nome de um NIC ou selecione-o da lista que é apresentada. Neste exemplo, **VM1 NIC1** está selecionada.
4. Selecione **rotas efetivas** no **interface de rede** painel, conforme mostrado na imagem seguinte:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    O **âmbito** por predefinição, a interface de rede selecionado.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Ver as rotas eficazes para uma tabela de rota
Quando modificar as rotas definidas pelo utilizador (UDRs) na tabela de rotas, poderá pretender rever o impacto das rotas seja adicionado de uma VM específica. Uma tabela de rota pode ser associada a qualquer número de sub-redes. Agora, pode ver todas as rotas eficazes para todos os NICs que uma tabela de rota indicado é aplicada, sem ter de mudar o contexto no painel de tabela de rota indicado.

Neste exemplo, um UDR (*UDRoute*) é especificada na tabela de rotas (*UDRouteTable*). Esta rota envia todo o tráfego de Internet de *Subnet1* no *WestUS VNet1* VNet, através de uma aplicação virtual de rede (NVA) na *Subnet2* da mesma VNet. A rota é mostrada na imagem seguinte:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Para ver as rotas agregadas de uma tabela de rota, conclua os seguintes passos:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **tabelas de rotas**
3. Pesquise a lista para a tabela de rota que pretende ver agregadas rotas para e selecione-o. Neste exemplo, **UDRouteTable** está selecionada. Um painel para a tabela de rota selecionada for apresentada, conforme mostrado na imagem seguinte:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Selecione **rotas efetivas** no **tabela de rotas** painel. O **âmbito** está definido para a tabela de rota que selecionou.
5. Uma tabela de rota pode ser aplicada a várias sub-redes. Selecione o **sub-rede** pretende rever da lista. Neste exemplo, **Subnet1** está selecionada.
6. Selecione um **Interface de rede**. São listados todos os NICs de ligados à sub-rede selecionada. Neste exemplo, **VM1 NIC1** está selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Se a NIC não estiver associada uma VM em execução, não rotas efetivas são apresentadas.
   >
   >

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
  * Regras do grupo de segurança de rede (NSG) podem estar a afetar os fluxos de tráfego. Para obter mais informações, consulte o [resolver problemas relacionados com grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md) artigo.
