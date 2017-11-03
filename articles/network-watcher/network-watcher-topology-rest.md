---
title: Visualizar a topologia de observador de rede do Azure - REST API | Microsoft Docs
description: Este artigo descreve como utilizar a REST API para consultar a topologia de rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Visualizar a topologia de observador de rede com a REST API

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

Neste cenário, é possível obter as informações de topologia. ARMclient é utilizada para chamar a API de REST utilizando o PowerShell. ARMClient for encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a resposta de topologia para um grupo de recursos específico.

## <a name="log-in-with-armclient"></a>Iniciar sessão com ARMClient

Inicie sessão no armclient com as suas credenciais do Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Obter a topologia

O exemplo seguinte pede a topologia da API REST.  O exemplo é parametrizado para permitir flexibilidade na criação de um exemplo.  Substituir todos os valores com \< \> envolvente-los.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

A seguinte resposta é a topologia para um resourcegroup de obter um exemplo de uma resposta encurtado que é devolvido quando:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba como visualizar os registos de fluxo NSG com o Power BI, visitando [visualizar NSG flui registos com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

