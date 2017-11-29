---
title: Configurar e os registos do servidor de acesso PostgreSQL utilizando a CLI do Azure | Microsoft Docs
description: Este artigo descreve como configurar e aceder os registos do servidor na base de dados do Azure para PostgreSQL utilizando a linha de comandos da CLI do Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d18ec44ecede44829b488ac9864bbfae2c62883a
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurar e os registos do servidor de acesso utilizando a CLI do Azure
Pode transferir os registos de erros de servidor PostgreSQL utilizando a Interface de linha de comandos (CLI do Azure). No entanto, o acesso aos registos de transações não é suportado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- Um [base de dados do Azure para o servidor de PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instalar [Azure CLI 2.0](/cli/azure/install-azure-cli) utilitário da linha de comandos ou utilize a Shell de nuvem do Azure no browser.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configurar o registo da base de dados do Azure para PostgreSQL
Pode configurar o servidor para aceder a registos de consulta e registos de erros. Os registos de erros podem conter informações vacuum automática, a ligação e a pontos de verificação.
1. Ativar o registo
2. Registo de atualização\_declaração e de registo\_min\_duração\_instrução para ativar o registo de consulta
3. Período de retenção de atualização

Para obter mais informações, consulte [personalizar parâmetros de configuração do servidor](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Registos de lista da base de dados do Azure para o servidor de PostgreSQL
Para listar os ficheiros de registo disponível para o servidor, execute o [lista de registos do servidor de postgres az](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) comando.

Pode listar os ficheiros de registo para o servidor **mypgserver 20170401.postgres.database.azure.com** no grupo de recursos **myresourcegroup**e direcioná-lo para um ficheiro de texto chamado **registo\_ficheiros\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Transferir os registos do servidor, localmente
O [az postgres-registos do servidor transferir](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) comando permite-lhe transferir ficheiros de registo individuais para o servidor. 

Neste exemplo transfere o ficheiro de registo específicos para o servidor **mypgserver 20170401.postgres.database.azure.com** no grupo de recursos **myresourcegroup** ao seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre os registos do servidor, consulte [registos do servidor na base de dados do Azure para PostgreSQL](concepts-server-logs.md)
- Para obter mais informações sobre os parâmetros de servidor, consulte [personalizar parâmetros de configuração de servidor utilizando a CLI do Azure](howto-configure-server-parameters-using-cli.md)
