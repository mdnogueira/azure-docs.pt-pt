---
title: "Agrupamento elástico de SQL da base de dados SQL do Azure de exemplo mover CLI | Microsoft Docs"
description: "Script de exemplo do CLI do Azure para mover uma base de dados SQL num agrupamento elástico de SQL"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 07/05/2017
ms.author: janeng
ms.openlocfilehash: 946492e1d4b784a2a70b553826b4ec837799ff6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Utilizar a CLI para mover uma base de dados SQL do Azure num agrupamento elástico de SQL

Neste exemplo de script da CLI do Azure cria dois conjuntos elásticos move de uma base de dados SQL do Azure de um agrupamento elástico de SQL para outro conjunto elástico de SQL e, em seguida, move a base de dados fora do conjunto elástico para um nível de desempenho de base de dados do Azure individual. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

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
| [servidor de sql AZ criar](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Cria um servidor lógico que aloja um agrupamento de base de dados ou elástico. |
| [criar conjuntos de elástico do sql AZ](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) | Cria um conjunto elástico no servidor lógico. |
| [Criar BD do sql AZ](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_create) | Cria uma base de dados de um servidor lógico como um único ou uma base de dados agrupado. |
| [atualização de base de dados do sql AZ](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Atualiza as propriedades de base de dados ou move de uma base de dados para fora do ou entre conjuntos elásticos. |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI de base de dados do SQL adicionais podem ser encontrados no [documentação da SQL Database do Azure](../sql-database-cli-samples.md).


