---
title: Criar e gerir a base de dados do Azure para as regras de firewall de MySQL utilizando a CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerir a base de dados do Azure para as regras de firewall de MySQL utilizando a linha de comandos da CLI do Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 0adcf8fd21049ee75972352b2e7d3c56300e0f87
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Criar e gerir o Azure base de dados MySQL das regras de firewall utilizando a CLI do Azure
Regras de firewall ao nível do servidor permitem aos administradores gerir o acesso a uma base de dados do Azure para o servidor de MySQL de um endereço IP específico ou um intervalo de endereços IP. Utilizar convenientes comandos da CLI do Azure, pode criar, atualizar, eliminar, lista e Mostrar regras de firewall para gerir o seu servidor. Para obter uma descrição geral da base de dados do Azure para MySQL firewalls, consulte [base de dados do Azure para as regras de firewall do servidor MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Instale o Python do Azure SDK para serviços de MySQL e PostgreSQL.
* Instale o componente da CLI do Azure para serviços PostgreSQL e MySQL.
* Crie uma base de dados do Azure para o servidor de MySQL.

## <a name="firewall-rule-commands"></a>Comandos de regra de firewall:
O **az mysql servidor-regra de firewall** comando é utilizado a partir da CLI do Azure para criar, eliminar, listar, mostrar e atualizar as regras de firewall.

Comandos:
- **criar**: criar uma regra de firewall do servidor do Azure MySQL.
- **eliminar**: eliminar uma regra de firewall do servidor do Azure MySQL.
- **lista**: lista as regras de firewall do servidor do Azure MySQL.
- **Mostrar**: Mostrar os detalhes de um servidor do Azure MySQL regra de firewall.
- **Atualizar**: atualizar uma regra de firewall do servidor do Azure MySQL.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Inicie sessão no Azure e lista a base de dados do Azure para servidores MySQL
Ligar de forma segura CLI do Azure com a sua conta do Azure utilizando o **início de sessão az** comando.

1. Na linha de comandos, execute o seguinte comando:
```azurecli
az login
```
Este comando devolve um código para utilizar no próximo passo.

2. Utilizar um browser para abrir a página [https://aka.ms/devicelogin](https://aka.ms/devicelogin)e, em seguida, introduza o código.

3. Na linha de comandos, inicie sessão com as suas credenciais do Azure.

4. Após o início de sessão está autorizado, uma lista de subscrições é impresso na consola do. Copie o ID da subscrição pretendido para configurar a subscrição atual para utilizar. Utilize o [az conta conjunto](/cli/azure/account#az_account_set) comando.
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. Se não souber dos nomes de uma lista de bases de dados do Azure para servidores MySQL para o grupo de recursos e subscrição. Utilize o [lista de servidores mysql az](/cli/azure/mysql/server#az_mysql_server_list) comando.

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Tenha em atenção o atributo de nome na listagem, tem de especificar o servidor de MySQL para trabalhar. Se for necessário, confirme os detalhes para esse servidor e utilizar o atributo de nome para se certificar de que está correto. Utilize o [mostrar de servidor de mysql az](/cli/azure/mysql/server#az_mysql_server_show) comando.

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista de regras de firewall na base de dados do Azure para o servidor de MySQL 
Utilizar o nome do servidor e o nome do grupo de recursos, lista as regras de firewall do servidor existente no servidor. Utilize o [lista de firewall de servidor de mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list) comando.  Tenha em atenção que o atributo de nome de servidor especificado no **– servidor** mudar e não no **– nome** mudar. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
A saída apresenta as regras, se existir, no JSON formatar (por predefinição). Pode utilizar o **– tabela de saída** comutador para enviar os resultados num formato mais legível de tabela.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Criar uma regra de firewall na base de dados do Azure para o servidor de MySQL
Utilizar o nome do servidor do Azure MySQL e o nome do grupo de recursos, crie uma nova regra de firewall no servidor. Utilize o [firewall do servidor az mysql criar](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) comando. Forneça um nome para a regra, bem como o IP de início e de fim IP (para fornecer acesso a um intervalo de endereços IP) para a regra.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Para permitir o acesso de um único endereço IP, de fornecer o mesmo endereço IP que o IP inicial e final de IP, tal como neste exemplo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Depois de concluído com sucesso, a saída do comando lista os detalhes da regra de firewall que criou, no formato JSON (por predefinição). Se existir uma falha, o resultado mostra texto da mensagem de erro em vez disso.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Atualizar uma regra de firewall na base de dados do Azure para o servidor de MySQL 
Utilizar o nome do servidor do Azure MySQL e o nome do grupo de recursos, atualize uma regra de firewall existente no servidor. Utilize o [atualização de firewall do servidor de mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update) comando. Forneça o nome da regra de firewall existente como entrada, bem como o início de atributos IP de IP e de fim para atualizar.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Depois de concluído com sucesso, a saída do comando lista os detalhes da regra de firewall que foi atualizado com, no formato JSON (por predefinição). Se existir uma falha, o resultado mostra texto da mensagem de erro em vez disso.

> [!NOTE]
> Se a regra de firewall não existir, a regra é criada pelo comando de atualização.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Mostrar detalhes da regra de firewall na base de dados do Azure para o servidor de MySQL
Utilizar o nome do servidor do Azure MySQL e o nome do grupo de recursos, mostram a firewall existente detalhes da regra do servidor. Utilize o [mostrar de firewall de servidor de mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show) comando. Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Depois de concluído com sucesso, a saída do comando lista os detalhes da regra de firewall que especificou, no formato JSON (por predefinição). Se existir uma falha, o resultado mostra texto da mensagem de erro em vez disso.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Eliminar uma regra de firewall na base de dados do Azure para o servidor de MySQL
Utilizar o nome do servidor do Azure MySQL e o nome do grupo de recursos, remova uma regra de firewall existente do servidor. Utilize o [firewall do servidor az mysql eliminar](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete) comando. Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Depois de concluído com sucesso, não há nenhuma saída. Após a falha, mostra texto da mensagem de erro.

## <a name="next-steps"></a>Passos seguintes
- Saber mais sobre [base de dados do Azure para as regras de firewall do servidor de MySQL](./concepts-firewall-rules.md).
- [Criar e gerir a base de dados do Azure MySQL das regras de firewall com o portal do Azure](./howto-manage-firewall-using-portal.md).
