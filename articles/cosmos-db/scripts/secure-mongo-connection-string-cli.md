---
title: "Cadeia de ligação de base de dados do Cosmos do CLI Get de Script do Azure do Azure para aplicações do MongoDB | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - cadeia de ligação de base de dados do Get Azure Cosmos para aplicações do MongoDB"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.openlocfilehash: 12e51922db4e9a2ff9b176186a4180ae2238be7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli"></a>Obter uma cadeia de ligação de base de dados do Azure Cosmos para as aplicações de MongoDB a utilizar a CLI do Azure

Este exemplo obtém uma cadeia de ligação de base de dados do Azure Cosmos para aplicações de MongoDB a utilizar a CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "Get Azure Cosmos DB connection string for MongoDB apps")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [atualização de cosmosdb AZ](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_update) | Atualiza uma conta de base de dados do Azure Cosmos. |
| [AZ cosmosdb lista--cadeias de ligação](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Obtém a cadeia de ligação para a conta.|
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script da CLI do Azure Cosmos DB adicionais podem ser encontrados no [documentação da CLI do Azure Cosmos DB](../cli-samples.md).
