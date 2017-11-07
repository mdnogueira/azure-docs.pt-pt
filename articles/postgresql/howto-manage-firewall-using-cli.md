---
title: Criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerir a base de dados do Azure PostgreSQL das regras de firewall com linha de comandos da CLI do Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: ec362cec28160b5c4827f6e47614661319ba4039
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure
Regras de firewall ao nível do servidor permitem que os administradores gerir o acesso a uma base de dados do Azure para o servidor de PostgreSQL de um endereço IP específico ou intervalo de endereços IP. Utilizar convenientes comandos da CLI do Azure, pode criar, atualizar, eliminar, lista e Mostrar regras de firewall para gerir o seu servidor. Para obter uma descrição geral da base de dados do Azure PostgreSQL das regras de firewall, consulte [base de dados do Azure para as regras de firewall do servidor de PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- Um [base de dados do Azure para a base de dados e servidor PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Instalar [Azure CLI 2.0](/cli/azure/install-azure-cli) utilitário da linha de comandos ou utilize a Shell de nuvem do Azure no browser.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurar regras de firewall para a base de dados do Azure para PostgreSQL
O [az postgres servidor-regra de firewall](/cli/azure/postgres/server/firewall-rule) comandos são utilizados para configurar regras de firewall.

## <a name="list-firewall-rules"></a>Lista regras de firewall 
Para listar as regras de firewall do servidor existente, execute o [lista de regra de firewall de servidores de postgres az](/cli/azure/postgres/server/firewall-rule#list) comando.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
A saída apresenta as regras de firewall, se aplicável, por predefinição no JSON formatar. Pode utilizar o comutador `--output table` para um formato mais legível de tabela como resultado.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Criar regra de firewall
Para criar uma nova regra de firewall no servidor, execute o [az postgres servidor-regra de firewall criar](/cli/azure/postgres/server/firewall-rule#create) comando. 

Ao especificar 0.0.0.0 como o `--start-ip-address` e 255.255.255.255 como o `--end-ip-address` intervalo, o exemplo seguinte permite que todos os endereços IP aceder ao servidor **mypgserver 20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Para permitir o acesso a um único endereço IP, forneça o endereço do mesmo a `--start-ip-address` e `--end-ip-address`, tal como neste exemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Depois de concluído com sucesso, a saída do comando lista os detalhes da regra de firewall que criou, por predefinição no formato JSON. Se existir uma falha, o resultado mostra uma mensagem de erro em vez disso.

## <a name="update-firewall-rule"></a>Regra de firewall de atualização 
Uma regra de firewall existente utilizando o servidor de atualização [atualização de regra de firewall do servidor de postgres az](/cli/azure/postgres/server/firewall-rule#update) comando. Forneça o nome da regra de firewall existente como entrada e o início de atributos IP de IP e de fim para atualizar.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Depois de concluído com sucesso, a saída do comando lista os detalhes da regra de firewall atualizado, por predefinição no formato JSON. Se existir uma falha, o resultado mostra uma mensagem de erro em vez disso.
> [!NOTE]
> Se a regra de firewall não existir, é criada pelo comando de atualização.

## <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra de firewall
Também pode mostrar os detalhes de uma regra de firewall ao nível do servidor existente através da execução [mostrar de regra de firewall de servidor de postgres az](/cli/azure/postgres/server/firewall-rule#show) comando.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Depois de concluído com sucesso, a saída do comando lista os detalhes da regra de firewall que especificou, por predefinição no formato JSON. Se existir uma falha, o resultado mostra uma mensagem de erro em vez disso.

## <a name="delete-firewall-rule"></a>Eliminar regra de firewall
Para revogar o acesso de um intervalo IP para o servidor, eliminar uma regra de firewall existente executando o [az postgres servidor-regra de firewall eliminar](/cli/azure/postgres/server/firewall-rule#delete) comando. Forneça o nome da regra de firewall existente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Depois de concluído com sucesso, não há nenhuma saída. Após a falha, o texto da mensagem de erro é devolvido.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode utilizar um browser para [criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure](howto-manage-firewall-using-portal.md).
- Saber mais sobre [base de dados do Azure para as regras de firewall do servidor de PostgreSQL](concepts-firewall-rules.md).
- Para obter ajuda na ligação para uma base de dados do Azure para o servidor de PostgreSQL, consulte [bibliotecas de ligação para base de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
