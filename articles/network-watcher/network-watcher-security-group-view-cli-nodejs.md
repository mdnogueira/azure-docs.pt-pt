---
title: "Analisar a segurança de rede com a vista de grupo de segurança de observador de rede do Azure - CLI do Azure 1.0 | Microsoft Docs"
description: "Este artigo descreve como utilizar a CLI do Azure 1.0 para analisar um segurança de máquinas virtuais com a vista do grupo de segurança."
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
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>Analisar a segurança da Máquina Virtual com a vista de grupo de segurança utilizando a CLI do Azure 1.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

Vista do grupo de segurança devolve regras de segurança de rede configurados e eficaz que são aplicadas a uma máquina virtual. Esta capacidade é útil para auditar e diagnosticar os grupos de segurança de rede e regras que são configuradas numa VM para garantir que o tráfego está a ser corretamente permitido ou negado. Neste artigo, vamos mostrar-lhe como obter as regras de segurança configuradas e eficaz para uma máquina virtual utilizando a CLI do Azure

Este artigo utiliza a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux. Observador de rede utiliza atualmente a CLI do Azure 1.0 para o suporte da CLI.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém as regras de segurança configurado e eficaz para uma máquina virtual especificada.

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessário para executar o `vm list` cmdlet. O comando seguinte apresenta uma lista de machinese virtual num grupo de recursos:

```azurecli
azure vm list -g resourceGroupName
```

Assim que sabe que a máquina virtual, pode utilizar o `vm show` cmdlet para obter o Id do recurso:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Obter vista de grupo de segurança

O próximo passo é obter o resultado de vista do grupo de segurança. A adição de "– json" Sinalizador irá formatar os resultados no json.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Visualizar os resultados

O exemplo seguinte é uma resposta reduzida de resultados devolvidos. O resultado mostra todas as regras de segurança eficaz e aplicados na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, e **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Visite [auditoria de segurança de rede grupos (NSG) com o observador de rede](network-watcher-nsg-auditing-powershell.md) para saber como automatizar a validação dos grupos de segurança de rede.

Saiba mais sobre as regras de segurança que são aplicadas aos seus recursos de rede, visitando [descrição geral da vista de grupo de segurança](network-watcher-security-group-view-overview.md)
