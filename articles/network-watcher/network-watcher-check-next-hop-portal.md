---
title: "Localizar o salto seguinte com o Azure rede observador do próximo salto - portal do Azure | Microsoft Docs"
description: "Este artigo descreve como pode encontrar o que é o tipo de salto seguinte e o endereço ip utilizando o salto seguinte no portal do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Saber que o tipo de próximo salto está a utilizar a capacidade de próximo salto na observador de rede do Azure através do portal

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

Próximo salto é uma funcionalidade do observador de rede que fornece a capacidade de obter o tipo de salto seguinte e o endereço IP com base numa máquina virtual especificada. Esta funcionalidade é útil para determinar se o tráfego que sai de uma máquina virtual atravessa um gateway, internet ou redes virtuais para aceder ao respetivo destino.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo utiliza o salto seguinte para determinar o tipo de salto seguinte e o endereço IP para um recurso. Para mais informações sobre o próximo salto, visite [descrição geral de salto seguinte](network-watcher-next-hop-overview.md).

Neste cenário, irá:

* Obter o salto seguinte a partir de uma máquina virtual.

## <a name="get-next-hop"></a>Obter o salto seguinte

### <a name="step-1"></a>Passo 1

Navegue para o recurso do observador de rede no portal do Azure.

### <a name="step-2"></a>Passo 2

Clique em **próximo salto** no painel de navegação, selecione a máquina virtual e a interface de rede, preencha o IP de origem e de destino e clique em de **próximo salto** botão.

> [!NOTE]
> Próximo salto requer que o recurso VM está alocado para ser executada.

![Descrição geral de salto seguinte][1]

### <a name="step-3"></a>Passo 3

Assim que a tarefa estiver concluída, os resultados são fornecidos. O endereço IP e o tipo de dispositivo, o próximo salto é, é apresentada. A tabela seguinte mostra os valores devolvidos disponíveis no portal.

**Tipo de próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nenhuma

Se uma rota personalizada foi utilizada para encaminhar este tráfego, a rota definida pelo utilizador (UDR) é apresentada, bem como com os resultados.

![obter resultados de salto seguintes][2]

## <a name="next-steps"></a>Passos seguintes

Saiba como rever as definições de grupo de segurança de rede através de programação, visitando [NSG auditoria com observador de rede](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














