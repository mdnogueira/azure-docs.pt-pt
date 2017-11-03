---
title: "Certifique-se de tráfego com o Azure rede observador IP fluxo verifique - CLI do Azure | Microsoft Docs"
description: "Este artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado utilizando a CLI do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f1355cd861722848211277250155c434da1e774d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado de uma VM com o IP fluxo verificar ou para um componente do observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)


Fluxo de IP verificar é uma funcionalidade do observador de rede que permite-lhe verificar se o tráfego é permitido para ou a partir de uma máquina virtual. Este cenário é útil para obter um estado atual do se uma máquina virtual pode comunicar com um recurso externo ou o back-end. Verificar o fluxo IP podem ser utilizadas para verificar se as regras do grupo de segurança de rede (NSG) estão configuradas corretamente e resolver problemas relacionados com fluxos que estão a ser bloqueados por regras NSG. Outra razão para utilizar o IP fluxo verificar é certificar-se de que o tráfego que pretende que sejam bloqueadas está a ser bloqueado corretamente por NSG.

Este artigo utiliza a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede ou ter uma instância existente do observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

Este cenário utiliza fluxo Certifique-se de IP para verificar se uma máquina virtual pode comunicar com um endereço IP do Bing conhecido. Se o tráfego for recusado, devolve a regra de segurança que está a negar esse tráfego. Para mais informações sobre o fluxo Certifique-se de IP, visite [IP fluxo Certifique-se de descrição geral](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>Obter uma VM

Fluxo IP Certifique-se o tráfego de testes para ou de um endereço IP numa máquina virtual para ou a partir de um destino remoto. Um Id de uma máquina virtual é necessário para o cmdlet. Se já souber o ID da máquina virtual a utilizar, pode ignorar este passo.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Obter os NICS

O endereço IP de uma NIC na máquina virtual é necessário, neste exemplo, obtemos os NICs numa máquina virtual. Se já conhece o endereço IP que pretende testar na máquina virtual, pode ignorar este passo.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Certifique-se de execução de fluxo de IP

Agora que temos as informações necessárias para executar o cmdlet, iremos executar o `network watcher ip-flow-verify` cmdlet para testar o tráfego. Neste exemplo, estamos a utilizar o primeiro endereço IP no NIC primeiro.

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> Fluxo de IP verificar requer que o recurso VM está alocado para executar.

## <a name="review-results"></a>Reveja os resultados

Após a execução `network watcher ip-flow-verify` os resultados são devolvidos, o exemplo seguinte é os resultados devolvidos a partir do passo anterior.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Passos seguintes

Se o tráfego está a ser bloqueado e não deve ser, consulte [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para identificar as regras de segurança e de grupo de segurança de rede que estão definidas.

Saiba como as definições de NSG de auditoria, visitando [auditoria de segurança de rede grupos (NSG) com o observador de rede](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
