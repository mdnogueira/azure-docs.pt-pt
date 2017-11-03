---
title: Visualizar a topologia de observador de rede do Azure - PowerShell | Microsoft Docs
description: Este artigo descreve como utilizar o PowerShell para consultar a topologia de rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>Visualizar a topologia de observador de rede com o PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

A funcionalidade de topologia do observador de rede fornece uma representação visual de recursos de rede numa subscrição. No portal, esta visualização é apresentada automaticamente. As informações de vista de topologia no portal podem ser obtidas através do PowerShell.
Esta capacidade torna as informações de topologia mais versátil como os dados podem ser utilizados por outras ferramentas para criar limite a visualização.

O interconnection é modelada em duas relações.

- **Contenção** -exemplo: VNet contém uma sub-rede contém um NIC
- **Associados** -exemplo: NIC está associado uma VM

A lista seguinte é propriedades que são devolvidas quando a consulta da API de REST de topologia.

* **nome** -o nome do recurso
* **ID** -o uri do recurso.
* **localização** -a localização onde o recurso existe.
* **associações** -uma lista de associações para o objeto referenciado.
    * **nome** -o nome do recurso referenciado.
    * **resourceId** -o resourceId é o uri do recurso referenciado na associação.
    * **associationType** -a relação entre o objeto subordinado e o elemento principal faz referência a este valor. Os valores válidos são **contém** ou **associados**.

## <a name="before-you-begin"></a>Antes de começar

Neste cenário, utilize o `Get-AzureRmNetworkWatcherTopology` cmdlet para obter as informações de topologia. Há também um artigo sobre como [obter topologia de rede com a REST API](network-watcher-topology-rest.md).

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a resposta de topologia para um grupo de recursos específico.

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

O primeiro passo é obter a instância de observador de rede. O `$networkWatcher` variável ser passada para o `Get-AzureRmNetworkWatcherTopology` cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Obter a topologia

O `Get-AzureRmNetworkWatcherTopology` cmdlet obtém a topologia para um grupo de recursos específico.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Resultados

Os resultados devolvidos tem uma propriedade name "recursos", que contém o corpo da resposta json para o `Get-AzureRmNetworkWatcherTopology` cmdlet.  A resposta contém os recursos no grupo de segurança de rede e as respetivas associações (ou seja, contém, associados).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os registos de fluxo NSG com o Power BI, visitando [visualizar NSG flui registos com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


