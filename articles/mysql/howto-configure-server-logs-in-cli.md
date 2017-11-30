---
title: Os registos do servidor de acesso na base de dados do Azure para MySQL utilizando a CLI do Azure | Microsoft Docs
description: "Este artigo descreve como pode aceder os registos do servidor na base de dados do Azure para MySQL utilizando o utilitário de linha de comandos da CLI do Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 908f28d8bd3d0dcbd03636e69cd47b5c47f3cfde
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurar e os registos do servidor de acesso utilizando a CLI do Azure
Pode transferir a base de dados do Azure para os registos do servidor de MySQL, utilizando a CLI do Azure, o utilitário de linha de comandos do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- [Base de dados do Azure para o servidor de MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- O [Azure CLI 2.0](/cli/azure/install-azure-cli) ou utilizar a Shell de nuvem do Azure no browser.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurar o registo da base de dados do Azure para MySQL
Pode configurar o servidor para aceder ao registo de consulta lenta MySQL.
1. Ativar o registo através da definição **lenta\_consulta\_registo** parâmetro como ON.
2. Ajustar a outros parâmetros como **longo\_consulta\_tempo** e **registo\_lenta\_admin\_instruções**.

Consulte [como configurar parâmetros de servidor](howto-configure-server-parameters-using-cli.md) para saber como definir o valor destes parâmetros através da CLI do Azure.

Por exemplo, o seguinte comando da CLI ativa o registo de consultas lenta, define a hora de consulta longo para 10 segundos e desativa o registo da instrução admin lentos. Por fim, lista as opções de configuração para a revisão.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista de registos da base de dados do Azure para o servidor de MySQL
Para listar os ficheiros de registo disponível para o servidor, execute o [lista de registos do servidor de mysql az](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) comando.

Pode listar os ficheiros de registo para o servidor **myserver4demo.mysql.database.azure.com** no grupo de recursos **myresourcegroup**e direcioná-lo para um ficheiro de texto chamado **registo\_ficheiros \_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Transferir os registos do servidor
O [az mysql-registos do servidor transferir](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) comando permite-lhe transferir ficheiros de registo individuais para o servidor. 

Neste exemplo transfere o ficheiro de registo específicos para o servidor **myserver4demo.mysql.database.azure.com** no grupo de recursos **myresourcegroup** ao seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [registos do servidor na base de dados do Azure para MySQL](concepts-server-logs.md)
