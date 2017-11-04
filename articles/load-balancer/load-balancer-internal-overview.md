---
title: "Descrição geral do Balanceador de carga interno do Azure | Microsoft Docs"
description: "Como um balanceador de carga interno funciona no Azure e cenários para configurar pontos finais internos."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 54e390dbdb07cb4c45c801b638099aa0dcc6db1a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="overview-of-azure-internal-load-balancer"></a>Descrição geral do Balanceador de carga interno do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure interno carga balanceador (ILB) apenas direciona o tráfego para recursos que estejam dentro de um serviço em nuvem ou uma VPN que utilizam para aceder à infraestrutura do Azure. No que esta respeita ILB difere de um balanceador de carga para a internet. Infraestrutura do Azure restringe o acesso para os balanceamento de carga endereços IP virtuais (VIP) de um serviço em nuvem ou para uma rede virtual. Endereços VIP e redes virtuais são expostos nunca diretamente para um ponto final de internet. Aplicações de linha de negócio internas executam no Azure e são acedidas a partir do Azure ou a partir dos recursos no local.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Por que razão poderá ter um balanceador de carga interno

Balanceador de carga interno fornece balanceamento da carga entre as máquinas virtuais (VMs) que se encontram dentro de um serviço em nuvem ou de uma rede virtual com um âmbito regional. Para obter informações sobre as redes virtuais com um âmbito regional, consulte [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) no blogue do Azure. As redes virtuais existentes que estão configuradas para um grupo de afinidade não é possível utilizar o ILB.

ILB permite que os seguintes tipos de balanceamento de carga:

* Dentro de um serviço de nuvem: carregar o balanceamento de VMs para um conjunto de VMs que residem no mesmo serviço em nuvem. Consulte este <a href="#figure1">exemplo</a>.
* Dentro de uma rede virtual: balanceamento de carga de VMs na rede virtual para um conjunto de VMs que residem no mesmo serviço em nuvem da rede virtual. Consulte este <a href="#figure2">exemplo</a>.
* Para uma rede virtual em vários locais: carregar o balanceamento de computadores no local para um conjunto de VMs que residem no mesmo serviço em nuvem da rede virtual. Consulte este <a href="#figure3">exemplo</a>.
* Para aplicações de várias camadas: para aplicações de várias camadas de acesso à internet onde as camadas de back-end não são através da Internet de balanceamento de carga. Os escalões de back-end necessitam da camada de acesso à internet de balanceamento de carga de tráfego.
* Para aplicações de linha de negócio: para aplicações de linha de negócio que estão alojadas no Azure sem hardware de Balanceador de carga adicional ou software de balanceamento de carga. Este cenário inclui a servidores no local que estão no conjunto de computadores cujo tráfego está com balanceamento de carga.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Balanceamento de carga para aplicações de várias camadas de acesso à internet

A camada web tem pontos finais de acesso à internet para clientes de internet e faz parte de um conjunto com balanceamento de carga. ILB distribui o tráfego recebido dos clientes de web para a porta TCP 443 (HTTPS) para os servidores web.

Os servidores de base de dados estejam atrás de um ponto final ILB que utilizam os servidores web para o armazenamento. O ponto final ILB é um base de dados com balanceamento de carga ponto final de serviço. O tráfego é com balanceamento de carga nos servidores de base de dados no conjunto de ILB.

A imagem seguinte mostra interna balanceamento de carga para a aplicação de várias camada de acesso à internet no mesmo serviço em nuvem.

<a name="figure1"></a>
![Aplicação de várias camadas de acesso à Internet](./media/load-balancer-internal-overview/IC736321.png)

Outro cenário está disponível para aplicações de várias camadas. O Balanceador de carga for implementado para um serviço em nuvem diferente daquele que consome o serviço para o ILB.

O ponto final ILB podem aceder a serviços em nuvem que utilizam a mesma rede virtual. A imagem seguinte mostra os servidores web front-end que estão num serviço de nuvem diferente da base de dados back-end. Os servidores front-end utilizam o ponto final ILB dentro da mesma rede virtual, como o back-end.

<a name="figure2"></a>
![Servidores front-end num serviço cloud diferente](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Balanceamento de carga para aplicações de linha de negócio de intranet

O tráfego de clientes na rede no local é com balanceamento de carga num conjunto de servidores de linha de negócio que utilizam uma ligação VPN à rede do Azure.

A máquina do cliente pode aceder a um endereço IP do serviço de VPN do Azure através da utilização de uma VPN ponto a site. A aplicação de linha de negócio pode ser alojada por trás o ponto final ILB.

<a name="figure3"></a>
![Aplicação de linha de negócio alojada por trás do ponto final ILB](./media/load-balancer-internal-overview/IC744148.png)

Outro cenário para aplicações de linha de negócio é uma VPN de site a site para a rede virtual em que o ponto final ILB está configurado. Tráfego de rede no local é encaminhado para o ponto final ILB.

<a name="figure4"></a>
![Tráfego encaminhado para o ponto final ILB de rede no local](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Limitações

Configurações de Balanceador de carga internas não suportam a realizar o SNAT. Neste artigo, realizar o SNAT refere-se para cenários que envolvem masquerading de porta tradução de endereços de rede de origem. Uma VM com um conjunto de Balanceador de carga tem de atingir o endereço IP Front-end do Balanceador de carga interno correspondentes. Falhas de ligação ocorrerem quando o fluxo é com balanceamento de carga para a VM que teve origem o fluxo. Estes cenários não são suportados para o ILB. Em vez disso, tem de ser utilizado num Balanceador de carga de estilo de proxy.

## <a name="next-steps"></a>Passos seguintes

* [Suporte do Azure Resource Manager para o Balanceador de carga do Azure](load-balancer-arm.md)
* [Introdução ao configurar um balanceador de carga para a internet](load-balancer-get-started-internet-arm-ps.md)
* [Introdução ao configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configurar o modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
