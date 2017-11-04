---
title: "Automatizar a auditoria de NSG com vista do grupo de segurança de observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece instruções sobre como configurar a auditoria de um grupo de segurança de rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 57f2200e541eeb629f72d60ffa0acb2d8233c018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizar a auditoria de NSG com vista do grupo de segurança de observador de rede do Azure

Os clientes, muitas vezes, são confrontados com o desafio de verificar a postura de segurança da respetiva infraestrutura. Este desafio é não diferente para as respetivas VMs no Azure. É importante ter um perfil de segurança semelhantes com base nas regras do grupo de segurança de rede (NSG) aplicadas. Utilizar a vista de grupo de segurança, agora pode obter a lista de regras aplicadas a uma VM dentro de um NSG. Pode definir um perfil de segurança NSG dourado e iniciar a vista do grupo de segurança uma cadência semanal e comparar o resultado para o perfil dourado e criar um relatório. Desta forma pode identificar com facilidade todas as VMs que não está em conformidade com o perfil de segurança previstas.

Se não estiver familiarizado com grupos de segurança de rede, visite [descrição geral de segurança de rede](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, comparar uma linha de base de boa conhecida com os resultados de vista de grupo de segurança devolvido para uma máquina virtual.

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede. O cenário também parte do princípio de que existe um grupo de recursos com uma máquina virtual válida para ser utilizado.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a vista de grupo de segurança para uma máquina virtual.

Neste cenário, irá:

- Obter um conjunto de regras de boa conhecida
- Obter uma máquina virtual com a Rest API
- Obter vista de grupo de segurança para a máquina virtual
- Avaliar a resposta

## <a name="retrieve-rule-set"></a>Obter conjunto de regras

O primeiro passo neste exemplo é para trabalhar com uma linha de base existente. O exemplo seguinte é algumas json extraído de um grupo de segurança de rede existente utilizando o `Get-AzureRmNetworkSecurityGroup` cmdlet que é utilizada como linha de base para este exemplo.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Converter o conjunto de regras de objetos do PowerShell

Neste passo, vamos estiver a ler um ficheiro json que foi criado anteriormente com as regras que deverão estar no grupo de segurança de rede para este exemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

O próximo passo é obter a instância de observador de rede. O `$networkWatcher` variável ser passada para o `AzureRmNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessário para executar o `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet contra. O exemplo seguinte obtém um objeto VM.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Obter vista de grupo de segurança

O próximo passo é obter o resultado de vista do grupo de segurança. Este resultado é comparado com o json de "linha de base" que foi apresentado anteriormente.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisar os resultados

A resposta está agrupada por interfaces de rede. Os diferentes tipos de regras devolvidos são eficazes e predefinidos regras de segurança. O resultado é reduzido ainda mais por como é aplicada, numa sub-rede ou um NIC virtual.

O seguinte script do PowerShell compara os resultados da vista de grupo de segurança para uma saída existente de um NSG. O exemplo seguinte é um exemplo simples da forma como os resultados podem ser comparados ao `Compare-Object` cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

O exemplo seguinte é o resultado. Pode ver duas das regras que foram na primeira regra definidas não estavam presentes na comparação.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Passos seguintes

Se as definições foram alteradas, consulte o artigo [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para identificar as regras de segurança e de grupo de segurança de rede que estão em questão.













