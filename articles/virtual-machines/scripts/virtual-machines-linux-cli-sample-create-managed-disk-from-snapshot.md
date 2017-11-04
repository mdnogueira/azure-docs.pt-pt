---
title: "Script CLI do Azure de exemplo - criar um disco gerido a partir de um instantâneo | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar um disco gerido a partir de um instantâneo"
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
ms.openlocfilehash: ffdad7faa34fec09623a415664b5a260868e9dbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Criar um disco gerido a partir de um instantâneo com o CLI

Este script cria um disco gerido a partir de um instantâneo. Utilizá-la para restaurar uma máquina virtual de instantâneos de discos de dados e SO. Crie o SO e discos geridos dos respetivos instantâneos de dados e, em seguida, criar uma nova máquina virtual ligando discos geridos. Também pode restaurar os discos de dados de uma VM existente ao anexar a criação de instantâneos de discos de dados.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um instantâneo. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Mostrar de instantâneo AZ](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Obtém todas as propriedades de um instantâneo com o nome e as propriedades do grupo de recursos do instantâneo. Propriedade de ID é utilizada para criar o disco gerido.  |
| [criar disco de AZ](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Cria um geridos utilizando o disco de instantâneos Id de um instantâneo gerido |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual ao anexar um disco gerido como disco do SO](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Máquina virtual adicional e discos geridos amostras de script da CLI podem ser encontrados no [documentação de VM do Linux do Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
