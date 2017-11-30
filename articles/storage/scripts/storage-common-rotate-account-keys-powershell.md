---
title: Exemplo de Script do PowerShell do Azure - chave de acesso de conta do storage rodar | Microsoft Docs
description: Criar uma conta de armazenamento do Azure, em seguida, obter e rodar uma das chaves de acesso de conta.
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
ms.openlocfilehash: a7aaacf316799540a6a72b699ba8ea8bb389c8a8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Criar uma conta de armazenamento e Rodar chaves de acesso de conta

Este script cria uma conta de armazenamento do Azure, apresenta a chave de acesso primária de uma conta de armazenamento novo e é renovada (roda) a chave.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a conta de armazenamento e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar a conta de armazenamento e obter e rodar uma das respetivas chaves de acesso. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) | Obtém todas as localizações e os fornecedores de recurso suportados para cada localização. |
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos do Azure. |
| [Novo-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Cria uma conta de armazenamento. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Obtém as chaves de acesso para uma conta de armazenamento do Azure. |
| [Novo AzureRmStorageAccountKey](/powershell/module/azurerm.storage/new-azurermstorageaccountkey) | Gera uma chave de acesso para uma conta de armazenamento do Azure de novo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de armazenamento adicional que podem ser encontrados na [exemplos do PowerShell para o Blob storage do Azure](../blobs/storage-samples-blobs-powershell.md).
