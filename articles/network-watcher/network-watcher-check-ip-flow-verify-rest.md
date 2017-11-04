---
title: "Certifique-se de tráfego com o fluxo de IP de observador de rede de Azure verificar - REST | Microsoft Docs"
description: "Este artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3ccef9ef521b86ffc1eb6047174f4f9e5d9e4296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado com o fluxo IP Certifique-se de um componente do observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)


Verificar o fluxo IP é uma funcionalidade do observador de rede que permite-lhe verificar se o tráfego é permitido para ou a partir de uma máquina virtual. A validação pode ser executada para o tráfego de entrada ou de saída. Este cenário é útil para obter um estado atual do se uma máquina virtual pode comunicar com um recurso externo ou o back-end. Verificar o fluxo IP podem ser utilizadas para verificar se as regras do grupo de segurança de rede (NSG) estão configuradas corretamente e resolver problemas relacionados com fluxos que estão a ser bloqueados por regras NSG. Outra razão para utilizar o IP fluxo verificar é certificar-se de que o tráfego que pretende que sejam bloqueadas está a ser bloqueado corretamente por NSG.

## <a name="before-you-begin"></a>Antes de começar

ARMclient é utilizada para chamar a API de REST utilizando o PowerShell. ARMClient for encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

Este cenário utiliza verifique de fluxo de IP para verificar se uma máquina virtual pode comunicar com outro computador através da porta 443. Se o tráfego for recusado, devolve a regra de segurança que está a negar esse tráfego. Para mais informações sobre o fluxo IP verifique, visite [fluxo IP verificar descrição geral](network-watcher-ip-flow-verify-overview.md)

Neste cenário, poderá:

* Obter uma máquina virtual
* Certifique-se de fluxo IP de chamada
* Verificar os resultados

## <a name="log-in-with-armclient"></a>Iniciar sessão com ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Obter uma máquina virtual

Execute o seguinte script para devolver uma máquina virtual. O seguinte código tem valores para as variáveis:

* **subscriptionId** -a subscrição do Id para utilizar.
* **resourceGroupName** -o nome de um grupo de recursos que contém máquinas virtuais.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

As informações que é necessário são o id de tipo `Microsoft.Compute/virtualMachines`. Os resultados deverão ser semelhantes ao exemplo de código seguinte:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Fluxo IP de chamada Verifique

O exemplo seguinte cria um pedido para verificar o tráfego para uma máquina virtual especificada. A resposta devolve se é permitido ao tráfego ou se o tráfego é negado. Se o tráfego é negado também devolve que regra bloqueia o tráfego.

> [!NOTE]
> Fluxo IP verificar requer que o recurso VM está alocado.

O script requer o Id de uma máquina virtual e de uma placa de interface de rede na máquina virtual do recurso. Estes valores são fornecidos pela saída anterior.

> [!Important]
> Para todas as chamadas de REST de observador de rede o nome do grupo de recurso no URI do pedido é aquele que contém a instância de observador de rede, não os recursos que está a efetuar as ações de diagnóstico.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Compreender os resultados

A resposta que regressar indica se o tráfego é permitido ou negado. A resposta aparente ser um dos seguintes exemplos:

**Permitido**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Negado**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Passos seguintes

Se o tráfego está a ser bloqueado e não deve ser, consulte [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para obter mais informações sobre grupos de segurança de rede.












