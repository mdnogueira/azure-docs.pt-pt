---
title: "Localizar o salto seguinte com o Azure rede observador do próximo salto - CLI do Azure 1.0 | Microsoft Docs"
description: "Este artigo descreve como pode encontrar o que é o tipo de salto seguinte e o endereço ip utilizando o salto seguinte ao utilizar a CLI do Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Saber que o tipo de próximo salto está a utilizar a capacidade de próximo salto na observador de rede do Azure utilizando a CLI do Azure 1.0

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

Próximo salto é uma funcionalidade do observador de rede que fornece a capacidade de obter o tipo de salto seguinte e o endereço IP com base numa máquina virtual especificada. Esta funcionalidade é útil para determinar se o tráfego que sai de uma máquina virtual atravessa um gateway, internet ou redes virtuais para aceder ao respetivo destino.

Este artigo utiliza a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux.

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, irá utilizar a CLI do Azure para localizar o tipo de salto seguinte e o endereço IP.

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo utiliza o salto seguinte, uma funcionalidade do observador de rede que localiza o tipo de salto seguinte e o endereço IP para um recurso. Para mais informações sobre o próximo salto, visite [descrição geral de salto seguinte](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Obter o salto seguinte

Para obter o salto seguinte chamamos a `azure netowrk watcher next-hop` cmdlet. O cmdlet podemos transmitir o grupo de recursos do observador de rede, NetworkWatcher, a máquina virtual Id, endereço IP de origem e endereço IP de destino. Neste exemplo, endereço IP de destino é uma VM na outra rede virtual. Não há um gateway de rede virtual entre as duas redes virtuais. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Se a VM possui vários NICs e reencaminhamento IP está ativado em nenhum dos NICs, em seguida, o parâmetro NIC (-i nic id) tem de ser especificado. Caso contrário, é opcional.

## <a name="review-results"></a>Reveja os resultados

Quando terminar, os resultados são fornecidos. O endereço IP do próximo salto é devolvido, bem como o tipo de recurso é.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

A lista seguinte mostra os valores de NextHopType atualmente disponíveis:

**Tipo de próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nenhuma

## <a name="next-steps"></a>Passos seguintes

Saiba como rever as definições de grupo de segurança de rede através de programação, visitando [NSG auditoria com observador de rede](network-watcher-nsg-auditing-powershell.md)
