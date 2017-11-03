---
title: "Resolver problemas de Gateway de rede Virtual do Azure e ligações - CLI do Azure 1.0 | Microsoft Docs"
description: "Esta página explica como utilizar o observador de rede de Azure CLI do Azure 1.0 de resolução de problemas"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 2acbc47970acf0eb2aa1aea8535d7157bc73cbb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-10"></a>Resolver problemas de Gateway de rede Virtual e ligações através do Azure rede observador Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Observador de rede oferece muitas funcionalidades no que respeita à compreender os seus recursos de rede no Azure. Uma dessas funcionalidades é recursos de resolução de problemas. Resolução de problemas de recursos pode ser chamada através do portal, o PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um Gateway de rede Virtual ou uma ligação e devolve conclusões.

Este artigo utiliza a plataforma do Azure CLI 1.0, que está disponível para o Windows, Mac e Linux. 

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de visita de tipos de gateway suportados, [tipos de Gateway suportado](/network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

Resolução de problemas de recursos fornece a capacidade de resolução de problemas que surgir com Gateways de rede Virtual e ligações. Quando é efetuado um pedido para a resolução de problemas de recursos, os registos estão a ser consultado e inspecioná-los. Quando a inspeção estiver concluída, os resultados são devolvidos. Recursos pedidos de resolução de problemas são execução longos pedidos, que pode demorar vários minutos a devolver um resultado. Os registos de resolução de problemas são armazenados num contentor numa conta de armazenamento que é especificado.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Obter uma ligação de Gateway de rede Virtual

Neste exemplo, a resolução de problemas de recursos está a ser foi executada numa ligação. Também pode transmiti-lo um Gateway de rede Virtual. O cmdlet seguinte apresenta uma lista de ligações vpn num grupo de recursos.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

Também pode executar o comando para ver as ligações numa subscrição.

```azurecli
azure network vpn-connection list -s subscription
```

Assim que tiver o nome da ligação, pode executar este comando para obter o Id do recurso:

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Resolução de problemas de recursos devolve dados sobre o estado de funcionamento do recurso, também economizam registos para uma conta de armazenamento para ser revisto. Neste passo, vamos criar uma conta de armazenamento, se existir uma conta de armazenamento existente poderá utilizá-lo.

1. Criar a conta de armazenamento

    ```azurecli
    azure storage account create -n storageAccountName -l location -g resourceGroupName
    ```

1. Obter as chaves de conta de armazenamento

    ```azurecli
    azure storage account keys list storageAccountName -g resourcegroupName
    ```

1. Criar o contentor

    ```azurecli
    azure storage container create --account-name storageAccountName -g resourcegroupName --account-key {storageAccountKey} --container logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar a resolução de problemas de recursos do observador de rede

Resolver problemas relacionados com os recursos com o `network watcher troubleshoot` cmdlet. Estamos a transmitir o cmdlet o grupo de recursos, o nome do observador de rede, o Id da ligação, o Id da conta de armazenamento, e o caminho para o blob para armazenar a resolução de problemas resulta numa.

```azurecli
azure network watcher troubleshoot -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

Depois de executar o cmdlet, o observador de rede revê o recurso para verificar o estado de funcionamento. Este devolve os resultados e a shell e armazena registos dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Compreender os resultados

O texto de ação fornece orientações gerais sobre como resolver o problema. Se uma ação pode ser levada para o problema, é fornecida uma ligação com orientações adicionais. No caso em que não existe nenhuma orientação adicional, a resposta fornece o url para abrir um incidente de suporte.  Para obter mais informações sobre as propriedades de resposta e que está incluído, visite [descrição geral de resolução de problemas de observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre como transferir ficheiros entre contas de armazenamento do azure, consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser utilizada é Explorador de armazenamento. Obter mais informações sobre o Explorador de armazenamento podem ser encontradas aqui na seguinte hiperligação: [Explorador de armazenamento](http://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se foram alteradas as definições que conectividade VPN de parar, consulte o artigo [gerir grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para identificar as regras de segurança e de grupo de segurança de rede que poderão estar em questão.
