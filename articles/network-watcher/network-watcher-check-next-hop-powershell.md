---
title: "Localizar o salto seguinte com o Azure rede observador do próximo salto - PowerShell | Microsoft Docs"
description: "Este artigo descreve como pode encontrar o que é o tipo de salto seguinte e o endereço ip utilizando o salto seguinte através do PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Saber que o tipo de próximo salto está a utilizar a capacidade de próximo salto na observador de rede do Azure com o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

Próximo salto é uma funcionalidade do observador de rede que fornece a capacidade de obter o tipo de salto seguinte e o endereço IP com base numa máquina virtual especificada. Esta funcionalidade é útil para determinar se o tráfego que sai de uma máquina virtual atravessa um gateway, internet ou redes virtuais para aceder ao respetivo destino.

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, irá utilizar o portal do Azure para localizar o tipo de salto seguinte e o endereço IP.

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo utiliza o salto seguinte, uma funcionalidade do observador de rede que localiza o tipo de salto seguinte e o endereço IP para um recurso. Para mais informações sobre o próximo salto, visite [descrição geral de salto seguinte](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

O primeiro passo é obter a instância de observador de rede. O `$networkWatcher` variável ser passada para o salto seguinte verificar cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Obter uma máquina virtual

Próximo salto devolve o salto seguinte e o endereço IP do próximo salto de uma máquina virtual. Um Id de uma máquina virtual é necessário para o cmdlet. Se já souber o ID da máquina virtual a utilizar, pode ignorar este passo.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Próximo salto requer que o recurso VM está alocado para ser executada.

## <a name="get-the-network-interfaces"></a>Obter interfaces de rede

O endereço IP de uma NIC na máquina virtual é necessário, neste exemplo, obtemos os NICs numa máquina virtual. Se já conhece o endereço IP que pretende testar na máquina virtual, pode ignorar este passo.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Obter o salto seguinte

Agora que chamamos a `Get-AzureRmNetworkWatcherNextHop` cmdlet. O cmdlet podemos transmitir o observador de rede, a máquina virtual Id, endereço IP de origem e endereço IP de destino. Neste exemplo, endereço IP de destino é uma VM na outra rede virtual. Não há um gateway de rede virtual entre as duas redes virtuais.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Reveja os resultados

Quando terminar, os resultados são fornecidos. O endereço IP do próximo salto é devolvido, bem como o tipo de recurso é. Neste cenário, é o endereço IP público do gateway de rede virtual.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
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

















