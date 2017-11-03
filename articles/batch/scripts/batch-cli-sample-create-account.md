---
title: Script CLI do Azure de exemplo - criar uma conta do Batch | Microsoft Docs
description: Script CLI do Azure de exemplo - criar uma conta do Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Criar uma conta do Batch com a CLI do Azure

Este script cria uma conta do Azure Batch e mostra como várias propriedades da conta podem ser consultadas e atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

Instalar a CLI do Azure utilizando as instruções fornecidas a [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se ainda não o tiver feito.

## <a name="batch-account-sample-script"></a>Script de exemplo de conta do batch

Quando cria uma conta do Batch, por predefinição respetivos nós de computação são atribuídos internamente pelo serviço Batch. Nós de computação alocados estarão sujeitas a uma quota de núcleos separado e a conta pode ser autenticada ou através de credenciais de chave partilhada ou um token de Dirctory Active Directory do Azure.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Conta do batch utilizar script de exemplo de subscrição de utilizador

Também pode optar por ter Batch criar os respetivos nós de computação na sua própria subscrição do Azure.
Contas que atribuem de processamento de nós na sua subscrição tem de ser autenticados através de um token do Azure Active Directory e os nós de computação alocados contabilizará-se a sua cota de subscrição. Para criar uma conta neste modo, um deve especificar uma referência do Cofre de chaves ao criar a conta.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar qualquer uma dos scripts de exemplo acima, execute o seguinte comando para remover o grupo de recursos e todos os relacionados recursos (incluindo as contas do Batch, contas de armazenamento do Azure e cofres de chaves do Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma conta do Batch e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [criar conta do batch AZ](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Batch.  |
| [conjunto de conta do batch AZ](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | Atualiza as propriedades da conta do Batch.  |
| [Mostrar de conta do batch AZ](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | Obtém os detalhes da conta de Batch especificado.  |
| [lista de chaves de conta de batch AZ](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | Obtém as chaves de acesso da conta do Batch especificada.  |
| [início de sessão de conta de batch de AZ](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autentica contra a conta de Batch especificada para obter mais interação do CLI.  |
| [criar conta de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. |
| [Criar AZ keyvault](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Cria um cofre de chaves. |
| [política de conjunto do keyvault AZ](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Atualize a política de segurança do Cofre de chaves especificado. |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de Batch CLI adicionais podem ser encontrados no [documentação da CLI do Azure Batch](../batch-cli-samples.md).
