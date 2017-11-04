---
title: Exemplos do PowerShell do Azure | Microsoft Docs
description: Exemplos do PowerShell do Azure
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-powershell-samples-for-networking"></a>Exemplos do PowerShell do Azure para funcionamento em rede

A tabela seguinte inclui ligações para scripts criados com o Azure PowerShell.

| | |
|-|-|
|**Conectividade entre os recursos do Azure**||
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP, enquanto o tráfego para a sub-rede de back-end está limitado a SQL Server, a porta 1433. |
| [Elemento duas redes virtuais](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria e ligar duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com uma VM que tenha capacidade para encaminhar o tráfego entre as duas sub-redes e as sub-redes de front-end e back-end. |
| [Filtrar o tráfego de rede VM de entrada e saído](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. Tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP e HTTPS... Não é permitido o tráfego de saída à Internet da sub-rede de back-end. |
|**Direção do tráfego e balanceamento de carga**||
| [Tráfego de balanceamento de carga para as VMs para elevada disponibilidade](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria várias máquinas virtuais da elevada disponibilidade e a configuração de balanceamento de carga. |
| [Balanceamento de carga de vários sites em VMs](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria duas VMs com várias configurações de IP, associadas a um Azure do conjunto de disponibilidade, acessível através de um balanceador de carga do Azure. |
| [Direto tráfego em várias regiões para disponibilidade elevada de aplicação](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Cria duas planos de serviço de aplicações, duas aplicações web, um perfil do Gestor de tráfego e dois pontos finais Gestor de tráfego. |
| | |
