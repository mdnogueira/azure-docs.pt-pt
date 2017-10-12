---
title: Rotas definidas pelo utilizador e Reencaminhamento IP no Azure | Microsoft Docs
description: "Saiba como configurar rotas definidas pelo utilizador (UDR) e Reencaminhamento IP para reencaminhar tráfego para aplicações virtuais de rede no Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c39076c4-11b7-4b46-a904-817503c4b486
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6274e0101f6fb0864c8d1efaef7fcde78b8760c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="user-defined-routes-and-ip-forwarding"></a>Rotas definidas pelo utilizador e reencaminhamento de IP

Quando adiciona máquinas virtuais (VM) a uma rede virtual (VNet) no Azure, repara que as VM conseguem comunicar entre si através da rede, automaticamente. Não é necessário especificar um gateway, apesar das VM estarem em sub-redes diferentes. O mesmo se verifica para a comunicação entre as VM e a Internet pública, e até mesmo na sua rede no local quando está presente uma ligação híbrida a partir do Azure para o seu centro de dados.

Este fluxo de comunicação é possível porque o Azure utiliza uma série de rotas de sistema para definir como flui o tráfego IP. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

* A partir da mesma sub-rede.
* A partir de uma sub-rede para outra numa VNet.
* A partir das VM para a Internet.
* A partir de uma VNet para outra VNet através de um gateway VPN.
* A partir de uma VNet para outra VNet através de VNet Peering (Encadeamento de Serviços).
* A partir de um VNet para a sua rede no local através de um gateway VPN.

A figura abaixo mostra uma configuração simples com uma VNet, duas sub-redes e algumas VMs, em conjunto com as rotas de sistema que permitem o fluxo do tráfego IP.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure1.png)

Embora a utilização das rotas de sistema facilita o tráfego automaticamente para a implementação, há casos em que pretende controlar o encaminhamento de pacotes através de uma aplicação virtual. Pode fazê-lo ao criar rotas definidas pelo utilizador que especificam o salto seguinte para os pacotes que fluem para uma sub-rede específica para aceder à sua aplicação virtual disso e ao ativar o reencaminhamento IP para a VM que funciona como aplicação virtual.

A figura abaixo mostra um exemplo de rotas definidas pelo utilizador e reencaminhamento IP para forçar a passagem de pacotes enviados para uma sub-rede de outra através de um dispositivo virtual numa sub-rede terceira.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure2.png)

> [!IMPORTANT]
> As rotas definidas pelo utilizador são aplicadas ao tráfego que sai de uma sub-rede a partir de qualquer recurso (por exemplo, as interfaces de rede ligadas a VMs) na sub-rede. Não pode criar rotas para especificar a forma como o tráfego entra numa sub-rede a partir da Internet, por exemplo. A aplicação para onde está a reencaminhar o tráfego não pode estar na mesma sub-rede de onde origina o tráfego. Crie sempre uma sub-rede separada para os dispositivos. 
> 
> 

## <a name="route-resource"></a>Recurso da rota
Os pacotes são reencaminhados através de uma rede TCP/IP com base numa tabela de rota definida em cada nó na rede física. Uma tabela de rota é uma coleção de rotas individuais utilizada para decidir onde pretende reencaminhar os pacotes com base no endereço IP de destino. Uma rota consiste no seguinte:

| Propriedade | Descrição | Restrições | Considerações |
| --- | --- | --- | --- |
| Prefixo do endereço |O CIDR de destino aos quais se aplica a rota, por exemplo, 10.1.0.0/16. |Tem de ser um intervalo CIDR válido que represente endereços na Internet pública, rede virtual do Azure ou centro de dados no local. |Confirme se o **Prefixo do endereço** não contém o endereço para o **Endereço do próximo salto**. Caso contrário, os pacotes entrarão num ciclo que vai da origem para o salto seguinte sem nunca chegarem ao destino. |
| Tipo de salto seguinte |O tipo de salto Azure para o qual o pacote deve ser enviado. |Tem de ser um dos seguintes valores: <br/> **Rede Virtual**. Representa a rede virtual local. Por exemplo, se tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16, na mesma rede virtual, a rota de cada sub-rede na tabela de rota terá um valor de salto seguinte igual a *Rede Virtual*. <br/> **Gateway de Rede Virtual**. Representa um VPN Gateway S2S do Azure. <br/> **Internet**. Representa o gateway de Internet predefinido fornecido pela infraestrutura do Azure. <br/> **Aplicação Virtual**. Representa uma aplicação virtual que adicionou à sua rede virtual do Azure. <br/> **Nenhuma**. Representa um buraco negro. Os pacotes reencaminhados para um buraco negro não são reencaminhados de modo algum. |Considere a utilização de um **Dispositivo Virtual** para direcionar o tráfego para um endereço IP interno VM ou para um Balanceador de Carga do Azure.  Este tipo permite a especificação de um endereço IP, conforme descrito abaixo. Considere a utilização de um tipo **Nenhuma** para impedir o fluxo dos pacotes para um determinado destino. |
| Endereço do próximo salto |O endereço do próximo salto contém o endereço IP para onde devem ser reencaminhados os pacotes. Os valores de salto seguintes só são permitidos em rotas onde está o tipo de salto seguinte é *Aplicação Virtual*. |Tem de ser um endereço IP alcançável dentro da Rede Virtual onde é aplicada a Rota Definida pelo Utilizador, sem passar por um **Gateway de Rede Virtual**. O endereço IP tem de estar na mesma Rede Virtual em que é aplicado ou uma Rede Virtual em modo de peering. |Se o endereço IP representa uma VM, certifique-se de que ativa [reencaminhamento IP](#IP-forwarding) no Azure para a VM. Se o endereço IP representa o endereço IP interno de um Balanceador de Carga do Azure, certifique-se de que tem uma regra de balanceamento de carga correspondente para cada porta para a qual pretende fazer o balanceamento de carga.|

No Azure PowerShell alguns dos valores "NextHopType" têm nomes diferentes:

* A Rede virtual é VnetLocal
* O Gateway de Rede Virtual é VirtualNetworkGateway
* A Aplicação Virtual é VirtualAppliance
* A Internet é Internet
* Nenhuma é Nenhuma

### <a name="system-routes"></a>Rotas de sistema
Cada sub-rede criada numa rede virtual é associada automaticamente a uma tabela de rota que contém as seguintes regras de rota de sistema:

* **Regra de Vnet Local**: esta regra é criada automaticamente para cada sub-rede numa rede virtual. Especifica que existe uma ligação direta entre as VM na VNet e não existe qualquer passo seguinte intermédio.
* **Regra no local**: esta regra aplica-se a todo o tráfego destinado ao intervalo de endereços no local e utiliza o gateway VPN como destino de salto seguinte.
* **Regra de Internet**: esta regra processa todo o tráfego destinado à Internet pública (prefixo de endereço 0.0.0.0/0) e utiliza o gateway para a Internet da infraestrutura como salto seguinte para todo o tráfego destinado à Internet.

### <a name="user-defined-routes"></a>Rotas definidas pelo utilizador
Para a maior parte dos ambientes, só necessita das rotas de sistema já definidas pelo Azure. No entanto, poderá ter de criar uma tabela de rota e adicionar um ou mais rotas em casos específicos, tais como:

* Forçar o túnel à Internet através da rede no local.
* Utilização de aparelhos virtuais no seu ambiente do Azure.

Nos cenários acima, terá de criar uma tabela de rota e adicionar-lhe rotas definidas pelo utilizador. Pode ter várias tabelas de rota e a mesma tabela de rota pode ser associada a uma ou mais sub-redes. Além disso, cada sub-rede apenas pode ser associada a uma tabela de rota única. Todas as VM e serviços em nuvem numa sub-rede utilizam a tabela de rota associada a essa sub-rede.

As sub-redes dependem de rotas de sistema até uma tabela de rota ser associada à sub-rede. Quando existe uma associação, o encaminhamento é efetuado com base na Maior Correspondência de Prefixo (LPM) entre ambas as rotas de sistema e as rotas definidas pelo utilizador. Se houver mais de uma rota com a mesma correspondência LPM, uma rota é selecionada com base na respetiva origem pela seguinte ordem:

1. Rota definida pelo utilizador
2. Rota BGP (quando é utilizado o ExpressRoute)
3. Rota de sistema

Para saber como criar rotas definidas pelo utilizador, veja [Como Criar Rotas e Permitir o Reencaminhamento IP no Azure](virtual-network-create-udr-arm-template.md).

> [!IMPORTANT]
> As rotas definidas pelo utilizador só são aplicadas às VM do Azure e aos serviços em nuvem. Por exemplo, se pretender adicionar uma aplicação virtual de firewall entre a rede no local e o Azure, terá de criar uma rota definida pelo utilizador para as suas tabelas de rota do Azure que reencaminham todo o tráfego destinado ao espaço de endereço no local para a aplicação virtual. Também pode adicionar uma rota definida pelo utilizador (UDR) para o GatewaySubnet para reencaminhar todo o tráfego no local para o Azure através do dispositivo virtual. Trata-se de uma adição recente.
> 
> 

### <a name="bgp-routes"></a>Rotas BGP
Se tiver uma ligação ExpressRoute entre a rede no local e o Azure, pode ativar a BGP para propagar rotas da sua rede no local para o Azure. Estes rotas BGP são utilizadas da mesma forma que as rotas de sistema e as rotas definidas pelo utilizador em cada sub-rede do Azure. Para obter mais informações, consulte [Introdução ao ExpressRoute](../expressroute/expressroute-introduction.md).

> [!IMPORTANT]
> Pode configurar o seu ambiente do Azure para utilizar imposição de túnel através da rede no local ao criar uma rota definida pelo utilizador para a sub-rede 0.0.0.0/0 que utiliza o gateway VPN como salto seguinte. No entanto, isto só funciona se utilizar um gateway VPN, não o ExpressRoute. Para o ExpressRoute, a imposição de protocolo é configurada através da BGP.
> 
> 

## <a name="ip-forwarding"></a>Reencaminhamento IP
Como descrito acima, uma das principais razões para criar uma rota definida de utilizador é reencaminhar tráfego para uma aplicação virtual. Uma aplicação virtual não é mais do que uma VM que executa uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT.

Este VM de aplicação virtual deve ser capaz de receber tráfego de entrada que não esteja endereçada a si mesma. Para permitir que uma VM receba tráfego endereçado a outros destinos, tem de ativar o Reencaminhamento IP para a VM. Isto é uma definição do Azure, não se trata de uma definição no sistema operativo convidado.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar rotas no modelo de implementação do Gestor de recursos](virtual-network-create-udr-arm-template.md) e associá-las a sub-redes. 
* Saiba como [criar rotas no modelo de implementação clássico](virtual-network-create-udr-classic-ps.md) e associá-las a sub-redes.

