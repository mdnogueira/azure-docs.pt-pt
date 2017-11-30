---
title: Exemplo de Script do PowerShell do Azure - eliminar contentores pelo prefixo | Microsoft Docs
description: Elimine contentores de BLOBs de armazenamento do Azure com base no prefixo de nome de contentor.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 629189b9dbe2327763d364abc95f49539a312c53
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Eliminar contentores com base no prefixo de nome de contentor

Este script Elimina contentores no armazenamento de Blobs do Azure com base no prefixo no nome do contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, restantes contentores, e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para eliminar contentores com base no prefixo de nome de contentor. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtém uma conta de armazenamento especificada, ou todas as contas de armazenamento de um grupo de recursos ou subscrição. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Lista os contentores de armazenamento associados uma conta de armazenamento. |
| [Remover AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Remove o contentor de armazenamento especificada. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de armazenamento adicional que podem ser encontrados na [exemplos do PowerShell para o Blob storage do Azure](../blobs/storage-samples-blobs-powershell.md).
