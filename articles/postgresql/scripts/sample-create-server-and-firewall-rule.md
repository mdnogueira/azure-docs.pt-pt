---
title: CLI do Azure Script - criar uma base de dados do Azure para PostgreSQL | Microsoft Docs
description: "Exemplo de Script do CLI do Azure - cria uma base de dados do Azure para o servidor de PostgreSQL e configura uma regra de firewall ao nível do servidor."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 11/27/2017
ms.openlocfilehash: f92739181a2011be7ce609b65bf7c862ac705129
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Criar uma base de dados do Azure para o servidor de PostgreSQL e configurar uma regra de firewall utilizando a CLI do Azure
Este script de exemplo do CLI cria uma base de dados do Azure para o servidor de PostgreSQL e configura uma regra de firewall ao nível do servidor. Assim que o script tiver sido executado com êxito, o servidor de PostgreSQL pode ser acedido de todos os serviços do Azure e o endereço IP configurado.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Este script de exemplo, edite as linhas realçadas para personalizar o nome de utilizador de administrador e a palavra-passe.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação de script
Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| **Comando** | **Notas** |
|---|---|
| [Criar grupo AZ](/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [servidor de postgres AZ criar](/cli/azure/postgres/server#az_postgres_server_create) | Cria um servidor de PostgreSQL que aloja as bases de dados. |
| [firewall do servidor AZ postgres criar](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) | Cria uma regra de firewall para permitir o acesso ao servidor e bases de dados-lo do intervalo de endereços IP introduzido. |
| [eliminação do grupo de AZ](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview)
- Tente scripts adicionais: [exemplos da CLI do Azure para a base de dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
