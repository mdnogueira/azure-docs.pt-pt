---
title: "Exemplo de Script CLI do Azure - instantâneo de exportação/copiar como VHD para uma conta de armazenamento numa região diferente | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - instantâneo de exportação/copiar como VHD para uma conta de armazenamento na subscrição idêntica ou diferente"
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
ms.openlocfilehash: dfb78106bc72aacee85f8412032165fdfcfc1ab3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>Exportar/cópia gerido instantâneos como VHD para uma conta do storage na região diferente com a CLI

Este script exporta um instantâneo de gerido para uma conta de armazenamento numa região diferente. Este primeiro gera o URI de SAS do instantâneo e, em seguida, utiliza-o para a copiar para uma conta de armazenamento numa região diferente. Utilize este script para manter a cópia de segurança dos seus discos geridos numa região diferente para recuperação após desastre. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para gerar o URI de SAS para um instantâneo gerido e copia o instantâneo de uma conta de armazenamento com o URI de SAS. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [AZ instantâneo conceder acesso de leitura](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Gera SAS só de leitura, que é utilizada para copiar o ficheiro VHD subjacente para uma conta de armazenamento ou transfira-o para no local  |
| [iniciar a cópia de BLOBs de armazenamento AZ](https://docs.microsoft.com/en-us/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Copia um blob assíncrona a partir de uma conta de armazenamento para outro |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Máquina virtual adicional e discos geridos amostras de script da CLI podem ser encontrados no [documentação de VM do Linux do Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
