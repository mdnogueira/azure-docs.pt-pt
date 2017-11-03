---
title: "Azure CLI Script-criar uma política de ativação pós-falha para elevada disponibilidade | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criar uma política de ativação pós-falha para elevada disponibilidade"
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
ms.openlocfilehash: 96083d66cc1a2ef179f9313c1b3ed04162c1c048
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-failover-policy-for-high-availability-using-the-azure-cli"></a>Criar uma política de ativação pós-falha para elevada disponibilidade utilizando a CLI do Azure

Este script de exemplo do CLI cria uma conta de base de dados do Azure Cosmos e, em seguida, configura-a para elevada disponibilidade.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh?highlight=23-27 "Create an Azure Cosmos DB failover policy")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](/cli/azure/group#create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ cosmosdb](/cli/azure/sql/server#create) | Cria uma conta de base de dados do Azure Cosmos. |
| [atualização de cosmosdb AZ](/cli/azure/cosmosdb#update) | As atualizações de conta de base de dados do Azure Cosmos. |
| [eliminação do grupo de AZ](/cli/azure/resource#delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script da CLI do Azure Cosmos DB adicionais podem ser encontrados no [documentação da CLI do Azure Cosmos DB](../cli-samples.md).
