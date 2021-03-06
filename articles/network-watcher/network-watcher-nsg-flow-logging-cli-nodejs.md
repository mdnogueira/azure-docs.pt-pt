---
title: "Gerir registos de fluxo de grupo de segurança de rede com o observador de rede do Azure - CLI do Azure 1.0 | Microsoft Docs"
description: "Esta página explica como gerir os registos de fluxo de grupo de segurança de rede no observador de rede do Azure com a CLI do Azure 1.0"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 466b146a0c39c1151e52e3ea936ae50705f29c64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli-10"></a>Configurar os registos de fluxo de grupo de segurança de rede com a CLI do Azure 1.0

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Registos de fluxo do grupo de segurança de rede são uma funcionalidade do observador de rede que permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Estes registos de fluxo são escritos no formato json e Mostrar fluxos de saída e entrados numa base por regra, o NIC o fluxo de mensagens em fila aplica-se a informações de 5 cadeias de identificação sobre o fluxo (origem/destino IP, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

Este artigo utiliza a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux. Observador de rede utiliza atualmente a CLI do Azure 1.0 para o suporte da CLI.

## <a name="register-insights-provider"></a>Fornecedor de informações de registo

Ordem de fluxo de registo para trabalhar com êxito, o **insights** fornecedor tem de estar registado. Se não tiver a certeza se a **insights** fornecedor está registado, execute o seguinte script.

```azurecli
azure provider register --namespace Microsoft.Insights --subscription <subscriptionid>
```

## <a name="enable-network-security-group-flow-logs"></a>Registos de fluxo de ativar o grupo de segurança de rede

O comando para ativar registos de fluxo é mostrado no exemplo seguinte:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e true
```

## <a name="disable-network-security-group-flow-logs"></a>Registos de fluxo de desativar o grupo de segurança de rede

Utilize o exemplo seguinte para desativar os registos de fluxo:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e false
```

## <a name="download-a-flow-log"></a>Transferir um registo de fluxo

A localização de armazenamento de um registo de fluxo é definida na criação. Uma ferramenta conveniente para aceder a estes registos de fluxo guardados para uma conta de armazenamento é Explorador de armazenamento do Microsoft Azure, que pode ser transferida aqui: http://storageexplorer.com/

Se for especificada uma conta de armazenamento, ficheiros de captura de pacotes são guardados para uma conta de armazenamento disponíveis na seguinte localização:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

Para obter informações sobre a estrutura do registo visite [descrição geral do fluxo de grupo de segurança de rede registo](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [visualizar os registos de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [visualizar os registos de fluxo NSG com ferramentas open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
