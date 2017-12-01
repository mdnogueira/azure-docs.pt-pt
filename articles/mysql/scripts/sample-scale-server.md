---
title: Exemplos da CLI do Azure para dimensionar uma base de dados do Azure para o servidor de MySQL | Microsoft Docs
description: "Este script de exemplo do CLI dimensiona de base de dados do Azure para o servidor de MySQL para um nível de desempenho diferente depois de consultar as métricas."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/02/2017
ms.openlocfilehash: 517208b76757655990f82f50677267320eb1393c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorizar e dimensionar uma base de dados do Azure para o servidor de MySQL utilizando a CLI do Azure
Este script de exemplo do CLI dimensiona uma única base de dados do Azure para o servidor de MySQL para um nível de desempenho diferente depois de consultar as métricas.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Este script de exemplo, altere as linhas realçadas para personalizar o nome de utilizador de administrador e a palavra-passe. Substitua o ID de subscrição utilizado nos comandos az monitor com o seu id de subscrição.[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=15-16 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode remover o grupo de recursos e todos os recursos associados, executando o seguinte comando:[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação de script
Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| **Comando** | **Notas** |
|---|---|
| [Criar grupo AZ](/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar servidor do AZ mysql](/cli/azure/mysql/server#az_mysql_server_create) | Cria um servidor de MySQL que aloja as bases de dados. |
| [lista de métricas de monitor AZ](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Liste o valor métrico de recursos. |
| [eliminação do grupo de AZ](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview).
- Tente scripts adicionais: [amostras da CLI do Azure para a base de dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre como aumentar, consulte [escalões de serviço](../concepts-service-tiers.md) e [unidades de armazenamento e computação unidades](../concepts-compute-unit-and-storage.md).
