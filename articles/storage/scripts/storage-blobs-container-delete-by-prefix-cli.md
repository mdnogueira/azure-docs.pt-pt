---
title: Exemplo de Script CLI do Azure - eliminar contentores pelo prefixo | Microsoft Docs
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
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: d14195abf1c17d11e259ed9edb5112626b063112
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Eliminar contentores com base no prefixo de nome de contentor

Este script primeiro cria alguns contentores de exemplo no Blob storage do Azure, em seguida, elimina alguns dos contentores com base no prefixo no nome do contentor.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, restantes contentores, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para eliminar contentores com base no prefixo de nome de contentor. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](/cli/azure/group#create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [criar conta de armazenamento AZ](/cli/azure/storage/account#create) | Cria uma conta de armazenamento do Azure no grupo de recursos especificado. |
| [Criar contentor de armazenamento AZ](/cli/azure/storage/container#create) | Cria um contentor de Blob storage do Azure. |
| [lista de contentor de armazenamento AZ](/cli/azure/storage/container#list) | Lista os contentores na conta de armazenamento do Azure. |
| [eliminação de contentor de armazenamento AZ](/cli/azure/storage/container#delete) | Elimina os contentores de uma conta de armazenamento do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script do armazenamento adicional CLI podem ser encontrados no [amostras da CLI do Azure para armazenamento do Azure](../blobs/storage-samples-blobs-cli.md).
