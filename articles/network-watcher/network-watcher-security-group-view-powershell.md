---
title: "Analisar a segurança de rede com a vista de grupo de segurança de observador de rede do Azure - PowerShell | Microsoft Docs"
description: "Este artigo descreve como utilizar o PowerShell para analisar um segurança de máquinas virtuais com a vista do grupo de segurança."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3983055cd580c263d39b908c61a16ed14353c9a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analisar a segurança da Máquina Virtual com a vista de grupo de segurança com o PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

Vista do grupo de segurança devolve regras de segurança de rede configurados e eficaz que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar os grupos de segurança de rede e regras que são configuradas numa VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, vamos mostrar-lhe como obter as regras de segurança configuradas e eficaz para uma máquina virtual utilizando o PowerShell

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, execute o `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet para obter as informações de regra de segurança.

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém as regras de segurança configurado e eficaz para uma máquina virtual especificada.

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

O primeiro passo é obter a instância de observador de rede. Esta variável é transmitida para o `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessário para executar o `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet contra. O exemplo seguinte obtém um objeto VM.

```powershell
$VM = Get-AzurermVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Obter vista de grupo de segurança

O próximo passo é obter o resultado de vista do grupo de segurança.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Visualizar os resultados

O exemplo seguinte é uma resposta reduzida de resultados devolvidos. O resultado mostra todas as regras de segurança eficaz e aplicados na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, e **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Passos seguintes

Visite [auditoria de segurança de rede grupos (NSG) com o observador de rede](network-watcher-nsg-auditing-powershell.md) para saber como automatizar a validação dos grupos de segurança de rede.


