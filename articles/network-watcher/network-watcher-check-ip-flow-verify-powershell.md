---
title: "Certifique-se de tráfego com o IP de observador de rede de Azure fluxo verifique - PowerShell | Microsoft Docs"
description: "Este artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado com o PowerShell"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 5257a70aa2dbc25bfe4eca5e2e0db87ca5e6b6fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado de uma VM com o fluxo IP ou para verificar um componente do observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)


Verificar o fluxo IP é uma funcionalidade do observador de rede que permite-lhe verificar se o tráfego é permitido para ou a partir de uma máquina virtual. Este cenário é útil para obter um estado atual do se uma máquina virtual pode comunicar com um recurso externo ou o back-end. Verificar o fluxo IP podem ser utilizadas para verificar se as regras do grupo de segurança de rede (NSG) estão configuradas corretamente e resolver problemas relacionados com fluxos que estão a ser bloqueados por regras NSG. Outra razão para utilizar o IP fluxo verificar é certificar-se de que o tráfego que pretende que sejam bloqueadas está a ser bloqueado corretamente por NSG.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede ou ter uma instância existente do observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

Certifique-se de fluxo IP este cenário utiliza para verificar se uma máquina virtual pode comunicar com um endereço IP do Bing conhecido. Se o tráfego for recusado, devolve a regra de segurança que está a negar esse tráfego. Para obter mais informações sobre o fluxo IP verificar, visite [fluxo IP verificar descrição geral](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

O primeiro passo é obter a instância de observador de rede. O `$networkWatcher` variável ser passada para o IP de fluxo verificar cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obter uma VM

Fluxo IP Certifique-se o tráfego de testes para ou de um endereço IP numa máquina virtual para ou a partir de um destino remoto. Um Id de uma máquina virtual é necessário para o cmdlet. Se já souber o ID da máquina virtual a utilizar, pode ignorar este passo.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Obter os NICS

O endereço IP de uma NIC na máquina virtual é necessário, neste exemplo, obtemos os NICs numa máquina virtual. Se já conhece o endereço IP que pretende testar na máquina virtual, pode ignorar este passo.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Certifique-se de execução de fluxo de IP

Agora que temos as informações necessárias para executar o cmdlet, iremos executar o `Test-AzureRmNetworkWatcherIPFlow` cmdlet para testar o tráfego. Neste exemplo, estamos a utilizar o primeiro endereço IP no NIC primeiro.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> Fluxo IP verificar requer que o recurso VM está alocado para ser executada.

## <a name="review-results"></a>Reveja os resultados

Após a execução `Test-AzureRmNetworkWatcherIPFlow` os resultados são devolvidos, o exemplo seguinte é os resultados devolvidos a partir do passo anterior.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Passos seguintes

Se o tráfego está a ser bloqueado e não deve ser, consulte [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para identificar as regras de segurança e de grupo de segurança de rede que estão definidas.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













