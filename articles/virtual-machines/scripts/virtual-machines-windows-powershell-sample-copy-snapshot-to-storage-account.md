---
title: "Exemplo de Script do PowerShell do Azure - instantâneo de exportação/copiar como VHD para uma conta de armazenamento numa região diferente | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - instantâneo de exportação/copiar como VHD para uma conta do storage na mesma região diferente"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: be21a891121df1d645b430d87b572cde6c945d61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exportar/cópia gerido instantâneos como VHD para uma conta do storage na região diferente com o PowerShell

Este script exporta um instantâneo de gerido para uma conta de armazenamento numa região diferente. Este primeiro gera o URI de SAS do instantâneo e, em seguida, utiliza-o para a copiar para uma conta de armazenamento numa região diferente. Utilize este script para manter a cópia de segurança dos seus discos geridos numa região diferente para recuperação após desastre.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para gerar o URI de SAS para um instantâneo gerido e copia o instantâneo de uma conta de armazenamento com o URI de SAS. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Conceder AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | Gera o URI de SAS para um instantâneo de que é utilizado para a copiar para uma conta de armazenamento. |
| [Novo AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Cria um contexto de conta de armazenamento com o nome da conta e chave. Pode ser utilizado neste contexto para efetuar operações de leitura/escrita na conta de armazenamento. |
| [Início AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Copia o VHD subjacente de um instantâneo de uma conta de armazenamento |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).