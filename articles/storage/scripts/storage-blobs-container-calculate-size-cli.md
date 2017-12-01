---
title: Script CLI do Azure de exemplo - calcular o tamanho do contentor do blob | Microsoft Docs
description: Calcule o tamanho de um contentor no Blob storage do Azure por totaling o tamanho dos blobs no contentor.
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: 61a553e47a642aead323a19d0724fdccc94a6282
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contentor de armazenamento de BLOBs

Este script calcula o tamanho de um contentor no Blob storage do Azure por totaling o tamanho dos blobs no contentor.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script CLI fornece um tamanho estimado para o contentor e não deve ser utilizada para cálculos de faturação.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, contentor e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para calcular o tamanho do contentor de armazenamento de Blobs. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](/cli/azure/group#create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [carregamento de blob de armazenamento AZ](/cli/azure/storage/account#create) | Carrega ficheiros locais para um contentor de armazenamento de Blobs do Azure. |
| [lista de BLOBs de armazenamento AZ](/cli/azure/storage/account/keys#list) | Apresenta uma lista de blobs um contentor de armazenamento de Blobs do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script do armazenamento adicional CLI podem ser encontrados no [amostras da CLI do Azure para o Blob storage do Azure](../blobs/storage-samples-blobs-cli.md).
