---
title: Visualizar a topologia de observador de rede do Azure - CLI do Azure 1.0 | Microsoft Docs
description: Este artigo descreve como utilizar a CLI do Azure 1.0 para consultar a topologia de rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bf12e1bde56c06e496d29ad27ba3da65cd94629e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-azure-cli-10"></a>Visualizar a topologia de observador de rede com a CLI do Azure 1.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

A funcionalidade de topologia do observador de rede fornece uma representação visual de recursos de rede numa subscrição. No portal, esta visualização é apresentada automaticamente. As informações de vista de topologia no portal podem ser obtidas através do PowerShell.
Esta capacidade torna as informações de topologia mais versátil como os dados podem ser utilizados por outras ferramentas para criar limite a visualização.

Este artigo utiliza a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux. 

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

Neste cenário, utilize o `network watcher topology` cmdlet para obter as informações de topologia. Há também um artigo sobre como [obter topologia de rede com a REST API](network-watcher-topology-rest.md).

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a resposta de topologia para um grupo de recursos específico.

## <a name="retrieve-topology"></a>Obter a topologia

O `network watcher topology` cmdlet obtém a topologia para um grupo de recursos específico. Adicionar o argumento "– json" para ver o oput no formato json

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Resultados

Os resultados devolvidos tem uma propriedade name "recursos", que contém o corpo da resposta json para o `network watcher topology` cmdlet.  A resposta contém os recursos no grupo de segurança de rede e as respetivas associações (ou seja, contém, associados).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as regras de segurança que são aplicadas aos seus recursos de rede, visitando [descrição geral da vista de grupo de segurança](network-watcher-security-group-view-overview.md)
