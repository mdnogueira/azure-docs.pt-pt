---
title: "Localizar o salto seguinte com a rede Azure observador próximo salto - REST | Microsoft Docs"
description: "Este artigo descreve como pode encontrar o que é o tipo de salto seguinte e o endereço ip utilizando o salto seguinte utilizando a API de REST do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Saber que o tipo de próximo salto está a utilizar a capacidade de próximo salto na observador de rede do Azure utilizando a API REST do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

Próximo salto é uma funcionalidade do observador de rede que fornece a capacidade de obter o tipo de salto seguinte e o endereço IP com base numa máquina virtual especificada. Esta funcionalidade é útil para determinar se o tráfego que sai de uma máquina virtual atravessa um gateway, internet ou redes virtuais para aceder ao respetivo destino.

## <a name="before-you-begin"></a>Antes de começar

ARMclient é utilizada para chamar a API de REST utilizando o PowerShell. ARMClient for encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo utiliza o salto seguinte, uma funcionalidade do observador de rede que localiza o tipo de salto seguinte e o endereço IP para um recurso. Para mais informações sobre o próximo salto, visite [descrição geral de salto seguinte](network-watcher-next-hop-overview.md).

Neste cenário, irá:

* Obter o salto seguinte para uma máquina virtual.

## <a name="log-in-with-armclient"></a>Iniciar sessão com ARMClient

Inicie sessão no armclient com as suas credenciais do Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Obter uma máquina virtual

Execute o seguinte script para devolver uma máquina virtual. Estas informações são necessárias para executar o salto seguinte.

O seguinte código tem valores para as seguintes variáveis:

- **subscriptionId** -a subscrição do Id para utilizar.
- **resourceGroupName** -o nome de um grupo de recursos que contém máquinas virtuais.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

O resultado seguinte, o id da máquina virtual é utilizado no exemplo seguinte:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Obter o salto seguinte

Assim que for criado o cabeçalho de autorização, o próximo salto de uma máquina virtual pode ser obtido. Os seguintes valores têm de ser substituídos para o exemplo de código trabalhar.

> [!Important]
> Para chamadas de API de REST do observador de rede, que o nome do grupo de recurso no URI do pedido é o grupo de recursos que contém o observador de rede, não os recursos estiver a efetuar as ações de diagnóstico do.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Próximo salto requer que o recurso VM está alocado para ser executada.

## <a name="results"></a>Resultados

O fragmento seguinte é um exemplo de saída recebido. Os resultados de conter os seguintes valores:

* **nextHopType** -este valor é um dos seguintes valores: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway ou None.
* **o nextHopIpAddress** -o endereço IP do próximo salto.
* **routeTableId** - o valor é um uri para a tabela de rota associada a rota ou, se não definido pelo utilizador rota é definida o valor de *rota de sistema* é devolvido.

Seguem-se os resultados no formato json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Passos seguintes

Depois de ter sido possível saber o salto seguinte para uma máquina virtual, pode ver a segurança dos seus recursos de rede, visitando [descrição geral de vista da segurança](network-watcher-security-group-view-overview.md)














