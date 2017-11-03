---
title: "Exemplo de Script do CLI do Azure - cópia (mover) geridos discos para subscrição idêntica ou diferente | Microsoft Docs"
description: "Exemplo de Script do CLI do Azure - cópia (mover) geridos discos para subscrição idêntica ou diferente"
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
ms.openlocfilehash: 8ff34f3d0b11c47f19205b92aebfc96e5cd5a014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copiar discos geridos à subscrição idêntica ou diferente com a CLI

Este script copia um disco gerido à subscrição idêntica ou diferente, mas na mesma região. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um novo disco gerido na subscrição de destino com o Id de disco gerido de origem. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Mostrar de disco AZ](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Obtém todas as propriedades de um disco gerido utilizando o nome e as propriedades do grupo de recursos do disco gerido. Propriedade de ID é utilizada para copiar o disco de gerido para uma subscrição diferente.  |
| [criar disco de AZ](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Cópias de um disco gerido através da criação de um novo geridos disco numa subscrição diferente, utilizando o Id e nome do disco principal gerido.  |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Máquina virtual adicional e discos geridos amostras de script da CLI podem ser encontrados no [documentação de VM do Linux do Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
