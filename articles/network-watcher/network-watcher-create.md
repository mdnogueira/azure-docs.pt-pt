---
title: "Criar uma instância de observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece os passos para criar uma instância do observador de rede utilizando o portal e a API REST do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 687e5b65e89ae2a79d8e9aa5c4345c91b4943d3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância de observador de rede do Azure

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. Monitorização ao nível do cenário permite-lhe diagnosticar problemas de uma vista de nível de rede ponto a ponto. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure.

> [!NOTE]
> Como atualmente o observador de rede suporta apenas CLI 1.0, as instruções para criar uma nova instância de observador de rede é fornecido para a CLI 1.0.

## <a name="create-a-network-watcher-in-the-portal"></a>Criar um observador de rede no portal

Navegue para **mais serviços** > **redes** > **observador de rede**. Pode selecionar todas as subscrições que pretende ativar o observador de rede para. Esta ação cria um observador de rede em cada região que está disponível.

![criar um observador de rede][1]

Quando ativar o observador de rede através do Portal, o nome da instância do observador de rede irá definir automaticamente como NetworkWatcher_region_name onde region_name corresponde à região do Azure onde a instância foi ativada.  Por exemplo, um observador de rede ativada na região Central EUA oeste será designado NetworkWatcher_westcentralus

Além disso, a instância de observador de rede será automaticamente adicionada para um grupo de recursos denominado NetworkWatcherRG.  Este grupo de recursos será criado se já existir.

Se pretender personalizar o nome de uma instância de observador de rede e o grupo de recursos está a ser colocado, pode utilizar o Powershell, a REST API ou ARMClient métodos descritos abaixo.  Cada opção, o grupo de recursos tem de existir antes de colocar o observador de rede para a mesma.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um observador de rede com o PowerShell

Para criar uma instância do observador de rede, execute o seguinte exemplo:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um observador de rede com a API REST

ARMclient é utilizada para chamar a API de REST utilizando o PowerShell. ARMClient for encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Iniciar sessão com ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Criar o observador de rede

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma instância do observador de rede, saiba mais sobre as funcionalidades disponíveis:

* [Topologia](network-watcher-topology-overview.md)
* [Captura de pacotes](network-watcher-packet-capture-overview.md)
* [Verificação do fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Salto seguinte](network-watcher-next-hop-overview.md)
* [Vista do grupo de segurança](network-watcher-security-group-view-overview.md)
* [Registo de fluxo do NSG](network-watcher-nsg-flow-logging-overview.md)
* [Resolução de problemas de Gateway de rede virtual](network-watcher-troubleshoot-overview.md)

Quando tiver sido criada uma instância de observador de rede, a captura de pacote pode ser configurada ao seguir o artigo: [criar uma captura de pacotes accionadas alerta](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-create/figure1.png











