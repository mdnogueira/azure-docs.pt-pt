---
title: Script do PowerShell do Azure de exemplo - calcular o tamanho do contentor do blob | Microsoft Docs
description: Calcule o tamanho de um contentor no Blob storage do Azure por totaling o tamanho de cada um dos respetivos blobs.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contentor de armazenamento de BLOBs

Este script calcula o tamanho de um contentor no Blob storage do Azure por totaling o tamanho dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do PowerShell fornece um tamanho estimado para o contentor e não deve ser utilizada para cálculos de faturação. Para um script que calcula o tamanho do contentor para fins de faturação, consulte [calcular o tamanho de um contentor de Blob storage para fins de faturação](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, contentor e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para calcular o tamanho do contentor de armazenamento de Blobs. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtém uma conta de armazenamento especificada, ou todas as contas de armazenamento de um grupo de recursos ou subscrição. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Apresenta uma lista de blobs num contentor. ||

## <a name="next-steps"></a>Passos seguintes

Para um script que calcula o tamanho do contentor para fins de faturação, consulte [calcular o tamanho de um contentor de Blob storage para fins de faturação](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de armazenamento adicional que podem ser encontrados na [exemplos do PowerShell para o Storage do Azure](../blobs/storage-samples-blobs-powershell.md).
