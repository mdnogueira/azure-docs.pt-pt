---
title: O suporte do Azure Resource Manager para o Balanceador de carga | Microsoft Docs
description: Utilizar o powershell para o Balanceador de carga com o Azure Resource Manager. Utilizar modelos de Balanceador de carga
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6ba329e55f03cf984ae795c1d3a509e196064e2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Utilizar o suporte do Azure Resource Manager com o Balanceador de carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

O Azure Resource Manager é a arquitetura de gestão preferenciais para serviços no Azure. Balanceador de carga do Azure pode ser gerido utilizando APIs do Azure Resource Manager e as ferramentas.

## <a name="concepts"></a>Conceitos

Com o Resource Manager, o Balanceador de carga do Azure contém os seguintes recursos subordinados:

* Configuração de IP Front-end – um balanceador de carga pode incluir um ou mais front-end endereços IP, caso contrário, conhecidos como um IPs virtuais (VIP). Estes endereços IP servem como entrada para o tráfego.
* Conjunto de endereços de back-end – são endereços IP associados com a máquina virtual rede NIC placa de Interface () para que a carga é distribuída.
* Regras de balanceamento de carga – uma propriedade de regra mapeia um IP de front-end especificado e a combinação de porta para um conjunto de endereços IP back-end e a combinação de porta. Um balanceador de carga individual pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e porta e IP back-end e porta associados a VMs.
* As pesquisas de sondas – permitem-lhe controlar o estado de funcionamento de instâncias VM. Se uma sonda do Estado de funcionamento falhar, a instância VM é retirada rotação automaticamente.
* Regras NAT de entrada – regras NAT que define o tráfego de entrada que fluem através de IP de front-end e distribuídos para o IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.

Modelos podem incluir definições de máquinas virtuais, redes virtuais, conjuntos de disponibilidade, Interfaces de rede (NICs), as contas do Storage, balanceadores de carga, grupos de segurança de rede e IPs públicos. Com modelos, pode criar tudo o que precisa para uma aplicação complexa. O ficheiro de modelo pode ser verificado no sistema de gestão de conteúdo para o controlo de versão e de colaboração.

[Saiba mais sobre os modelos](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Saiba mais sobre os recursos de rede](../virtual-network/resource-groups-networking.md)

Para modelos de início rápido, utilizando o Balanceador de carga do Azure, consulte o [repositório do GitHub](https://github.com/Azure/azure-quickstart-templates) que aloja um conjunto de modelos de Comunidade gerado.

Exemplos de modelos:

* [2 VMs num Load Balancer e regras de balanceamento de carga](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs numa VNET com regras de Balanceador de carga e de Balanceador de carga interno](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs num Balanceador de carga e configurar regras NAT no LB](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configuração de Balanceador de carga do Azure com o PowerShell ou o CLI

Começar a utilizar os cmdlets do Azure Resource Manager, as ferramentas de linha de comandos e REST APIs

* [Cmdlets de rede do Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) pode ser utilizado para criar um balanceador de carga.
* [Como criar um balanceador de carga com o Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Utilizar a CLI do Azure com gestão de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* [REST APIs do Balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Passos seguintes

Também pode [começar a criar um Internet Balanceador de carga com acesso à](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede de Balanceador de carga específicos.

Saiba como gerir [definições de tempo limite TCP para um balanceador de carga de inatividade](load-balancer-tcp-idle-timeout.md). Isto é importante quando a aplicação tem de manter as ligações alive para os servidores protegidos por um balanceador de carga.
