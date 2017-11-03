---
title: "Script CLI do Azure de exemplo - criar um disco gerido a partir de um ficheiro VHD numa conta do storage na mesma subscrição | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar um disco gerido a partir de um ficheiro VHD numa conta do storage na mesma subscrição"
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
ms.openlocfilehash: ede9457f5843d0a8a04503779970a553c5ed4f96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Criar um disco gerido a partir de um ficheiro VHD numa conta do storage na mesma subscrição com a CLI

Este script cria um disco gerido a partir de um ficheiro VHD numa conta do storage na mesma subscrição. Utilize este script para importar um especializadas (não generalizado/processado pelo Sysprep) VHD para o disco de SO gerido para criar uma máquina virtual. Em alternativa, utilize-o para importar um VHD de dados para o disco de dados geridos. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um VHD. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [criar disco de AZ](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Cria um disco gerido com o URI de um VHD numa conta do storage na mesma subscrição |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual ao anexar um disco gerido como disco do SO](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Máquina virtual adicional e discos geridos amostras de script da CLI podem ser encontrados no [documentação de VM do Linux do Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
