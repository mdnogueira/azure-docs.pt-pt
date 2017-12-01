---
title: Exemplo de Script CLI do Azure - chaves de acesso de conta de armazenamento rodar | Microsoft Docs
description: Criar uma conta de armazenamento do Azure, em seguida, obter e Rodar chaves de acesso de conta.
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
ms.openlocfilehash: c8c99dd8663eab5b5c1c47d34f110f9dceaace6b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Criar uma conta de armazenamento e Rodar chaves de acesso de conta

Este script cria uma conta de armazenamento do Azure, apresenta as chaves de acesso da conta de armazenamento novo e é renovada (roda) as chaves.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a conta de armazenamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar a conta de armazenamento e obter e rodar as respetivas chaves de acesso. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](/cli/azure/group#create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [criar conta de armazenamento AZ](/cli/azure/storage/account#create) | Cria uma conta de armazenamento do Azure no grupo de recursos especificado. |
| [lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys#list) | Mostra as chaves de acesso da conta de armazenamento para a conta especificada. |
| [chaves de conta de armazenamento AZ renovar](/cli/azure/storage/account/keys#renew) | A chave de acesso da conta de armazenamento primário ou secundário gera de novo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](/cli/azure/overview).

Exemplos de script do armazenamento adicional CLI podem ser encontrados no [amostras da CLI do Azure para o Blob storage do Azure](../blobs/storage-samples-blobs-cli.md).
