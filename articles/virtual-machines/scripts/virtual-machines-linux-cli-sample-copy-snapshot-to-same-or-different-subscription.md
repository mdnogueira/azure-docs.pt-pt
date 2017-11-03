---
title: "Exemplo de Script CLI do Azure - instantâneo de cópia (mover) de um disco gerido à subscrição idêntica ou diferente com a CLI | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - instantâneo de cópia (mover) de um disco gerido à subscrição idêntica ou diferente com a CLI"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7c301a314ee946bb9199650bb7f674b8dce7c141
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Instantâneo de cópia de um disco gerido à subscrição idêntica ou diferente com a CLI

Este script copia um instantâneo de um disco gerido para subscrição idêntica ou diferente. Utilize este script para mover um instantâneo para uma subscrição diferente na mesma região que o instantâneo de principal.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um instantâneo na subscrição de destino com o Id do instantâneo de origem. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Mostrar de instantâneo AZ](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Obtém todas as propriedades de um instantâneo com o nome e as propriedades do grupo de recursos do instantâneo. Propriedade de ID é utilizada para copiar o instantâneo para uma subscrição diferente.  |
| [criar instantâneos AZ](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | Copia um instantâneo através da criação de um instantâneo na subscrição diferentes com o Id e nome do instantâneo principal.  |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Máquina virtual adicional e discos geridos amostras de script da CLI podem ser encontrados no [documentação de VM do Linux do Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
