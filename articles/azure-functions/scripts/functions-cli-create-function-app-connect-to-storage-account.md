---
title: "Criar uma função do Azure que se liga a um Storage do Azure | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar uma função do Azure que se liga a um Storage do Azure"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 363a3fd1c80538495658720274840b921baa8675
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="integrate-function-app-into-azure-storage-account"></a>Integrar a aplicação de função na conta do Storage do Azure

Este script de exemplo cria uma aplicação de função e a conta de armazenamento.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se utilizar a CLI localmente, certifique-se de que está a executar a CLI do Azure versão 2.0 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma aplicação de função do Azure e adiciona a cadeia de ligação de armazenamento para uma definição de aplicação.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, execute o seguinte comando para remover o grupo de recursos e todos os recursos relacionados:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [início de sessão AZ](https://docs.microsoft.com/cli/azure/#login) | Inicie sessão no Azure. |
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Criar um grupo de recursos com a localização |
| [criar conta de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/account) | Criar uma conta de armazenamento |
| [Criar AZ functionapp](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Criar uma nova aplicação de função |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Limpeza |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI de funções do Azure adicionais podem ser encontrados no [documentação de funções do Azure](../functions-cli-samples.md).
