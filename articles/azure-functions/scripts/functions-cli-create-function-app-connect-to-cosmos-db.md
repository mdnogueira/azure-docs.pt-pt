---
title: "Criar uma função do Azure que se liga a uma base de dados do Azure Cosmos | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar uma função do Azure que se liga a uma base de dados do Cosmos do Azure"
services: functions
documentationcenter: functions
author: rachelappel
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
ms.author: rachelap
ms.custom: mvc
ms.openlocfilehash: ce48f8dd71d5b9e48dcd506df976e07d385f34c5
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar uma função do Azure que se liga a uma base de dados do Cosmos do Azure

Este script de exemplo cria uma aplicação de função do Azure e se liga a uma base de dados do Azure Cosmos DB.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma aplicação de função do Azure e adiciona uma chave de acesso e de ponto final do Cosmos DB para as definições de aplicação.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o comando de seguir para remover o grupo de recursos e todos os recursos relacionados.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [início de sessão AZ](https://docs.microsoft.com/cli/azure/#login) | Inicie sessão no Azure. |
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Criar um grupo de recursos com a localização |
| [criar conta de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/account) | Criar uma conta de armazenamento |
| [Criar AZ functionapp](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Criar uma nova aplicação de função |
| [Criar AZ cosmosdb](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Criar base de dados cosmosdb |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Limpeza |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI de funções do Azure adicionais podem ser encontrados no [documentação de funções do Azure](../functions-cli-samples.md).




