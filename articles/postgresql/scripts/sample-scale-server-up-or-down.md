---
title: Base de dados do Azure de escala de Script do CLI do Azure para PostgreSQL | Microsoft Docs
description: "Azure CLI Script de exemplo - base de dados do dimensionamento do Azure para o servidor PostgreSQL um nível de desempenho diferente depois de consultar as métricas."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/27/2017
ms.openlocfilehash: a5a24e9aeea193df28bd49d5c428a72b5ec75d1b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorizar e dimensionar um único servidor PostgreSQL utilizando a CLI do Azure
Este script de exemplo do CLI dimensiona uma única base de dados do Azure para o servidor PostgreSQL um nível de desempenho diferente depois de consultar as métricas. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Este script de exemplo, altere as linhas realçadas para personalizar o nome de utilizador de administrador e a palavra-passe. Substitua o ID de subscrição utilizada nos comandos az monitor com o seu próprio identificador de subscrição.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação de script
Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| **Comando** | **Notas** |
|---|---|
| [Criar grupo AZ](/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [servidor de postgres AZ criar](/cli/azure/postgres/server#az_postgres_server_create) | Cria um servidor de PostgreSQL que aloja as bases de dados. |
| [lista de métricas de monitor AZ](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Liste o valor métrico de recursos. |
| [eliminação do grupo de AZ](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview)
- Tente scripts adicionais: [exemplos da CLI do Azure para a base de dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Ler mais informações sobre como aumentar: [escalões de serviço](../concepts-service-tiers.md) e [unidades de armazenamento e computação unidades](../concepts-compute-unit-and-storage.md)
