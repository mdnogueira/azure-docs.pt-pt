---
title: "Analisar a segurança de rede com a vista de grupo de segurança de observador de rede do Azure - Azure CLI 2.0 | Microsoft Docs"
description: "Este artigo descreve como utilizar o Azure CLI 2.0 para analisar um segurança de máquinas virtuais com a vista do grupo de segurança."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c2d86777625059253864c1c9dc885aa28ac4b016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-20"></a>Analisar a segurança da Máquina Virtual com a vista de grupo de segurança a utilizar o Azure CLI 2.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

Vista do grupo de segurança devolve regras de segurança de rede configurados e eficaz que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar os grupos de segurança de rede e regras que são configuradas numa VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, vamos mostrar-lhe como obter as regras de segurança configuradas e eficaz para uma máquina virtual utilizando a CLI do Azure


Este artigo utiliza o nosso CLI de próxima geração para o modelo de implementação de gestão de recursos, Azure CLI 2.0, que está disponível para o Windows, Mac e Linux.

Para efetuar os passos neste artigo, terá de [instalar a Interface de linha de comandos do Azure para Mac, Linux e Windows (CLI do Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém as regras de segurança configurado e eficaz para uma máquina virtual especificada.

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessário para executar o `vm list` cmdlet. O comando seguinte apresenta uma lista de máquinas virtuais num grupo de recursos:

```azurecli
az vm list -resource-group resourceGroupName
```

Assim que sabe que a máquina virtual, pode utilizar o `vm show` cmdlet para obter o Id do recurso:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Obter vista de grupo de segurança

O próximo passo é obter o resultado de vista do grupo de segurança.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visualizar os resultados

O exemplo seguinte é uma resposta reduzida de resultados devolvidos. O resultado mostra todas as regras de segurança eficaz e aplicados na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, e **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Visite [auditoria de segurança de rede grupos (NSG) com o observador de rede](network-watcher-nsg-auditing-powershell.md) para saber como automatizar a validação dos grupos de segurança de rede.

Saiba mais sobre as regras de segurança que são aplicadas aos seus recursos de rede, visitando [descrição geral da vista de grupo de segurança](network-watcher-security-group-view-overview.md)
