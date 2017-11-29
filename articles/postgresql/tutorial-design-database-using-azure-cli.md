---
title: Conceber a sua primeira base de dados do Azure para PostgreSQL utilizando a CLI do Azure | Microsoft Docs
description: Este tutorial mostra como conceber a sua primeira base de dados do Azure para PostgreSQL utilizando a CLI do Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/27/2017
ms.openlocfilehash: 97299ae904115d08c5d03be38be263203552b84b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Conceber a sua primeira base de dados do Azure para PostgreSQL utilizando a CLI do Azure 
Neste tutorial, utilize a CLI do Azure (interface de linha de comandos) e outros utilitários para saber como:
> [!div class="checklist"]
> * Criar uma Base de Dados do Azure para o servidor PostgreSQL
> * Configurar a firewall do servidor
> * Utilize [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) utilitário para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Pode utilizar a Shell de nuvem do Azure no browser, ou [instale o Azure CLI 2.0]( /cli/azure/install-azure-cli) no seu computador para executar os comandos neste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado. Selecione um ID de subscrição específica na sua conta com o comando [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL
Crie uma [Base de Dados do Azure para o servidor PostgreSQL](overview.md) com o comando [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Os servidores contêm um grupo de bases de dados geridas como um grupo. 

O exemplo seguinte cria um servidor designado `mypgserver-20170401` no seu grupo de recursos `myresourcegroup` com início de sessão de administrador de servidor `mylogin`. O nome de um servidor mapeia para o nome DNS e, por conseguinte, é necessário para ser globalmente exclusivo no Azure. Substitua `<server_admin_password>` pelo seu próprio valor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

Por predefinição, é criada a base de dados **postgres** no seu servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Crie uma regra de firewall ao nível do servidor do Azure PostgreSQL com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). A regra de firewall ao nível do servidor permite que uma aplicação externa, como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), se ligue ao seu servidor através da firewall do serviço Azure PostgreSQL. 

Pode definir uma regra de firewall que abrange um intervalo IP para conseguir estabelecer ligação a partir da sua rede. O exemplo seguinte utiliza [az postgres servidor-regra de firewall criar](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) para criar uma regra de firewall `AllowAllIps` que permite a ligação de qualquer endereço IP. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

Para restringir o acesso ao seu servidor do Azure PostgreSQL apenas a rede, pode definir a regra de firewall para abranger apenas o intervalo de endereços IP de rede empresarial.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> O servidor PostgreSQL do Azure comunica através da porta 5432. Ao ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Peça ao departamento de TI para abrir a porta 5432, para ligar ao servidor da Base de Dados SQL do Azure.
>

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

O resultado está no formato JSON. Aponte o **administratorLogin** e o **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Ligar à base de dados do Azure para a base de dados PostgreSQL utilizando psql
Se o computador cliente tiver PostgreSQL instalado, pode utilizar uma instância local do [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), ou a consola de nuvem do Azure para ligar a um servidor do Azure PostgreSQL. Agora, vamos utilizar o utilitário da linha de comandos psql para ligar à Base de Dados do Azure para o servidor PostgreSQL.

1. Execute o seguinte comando para ligar a uma base de dados do Azure para a base de dados PostgreSQL psql:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mypgserver-20170401.postgres.database.azure.com** com as credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  Assim que estiver ligado ao servidor, crie uma base de dados em branco na linha de:
```sql
CREATE DATABASE mypgsqldb;
```

3.  Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** criada recentemente:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à base de dados do Azure para PostgreSQL, iremos pode passam como concluir algumas tarefas básicas.

Em primeiro lugar, iremos criar uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que controla as informações de inventário:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Agora, pode ver a tabela criada recentemente na lista de tabelas, escrevendo:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Carregar dados para a tabela
Agora que temos uma tabela, iremos pode inserir alguns dados para a mesma. A janela de linha de comandos aberta, execute a seguinte consulta para inserir alguns linhas de dados:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Adicionou agora duas linhas de dados de exemplo para a tabela que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela de inventário: 
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados na tabela de inventário:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Pode ver valores atualizados ao obter os dados:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo
Imagine que tenha eliminado acidentalmente uma tabela. Este é algo que facilmente não consegue recuperar. Base de dados do Azure para PostgreSQL permite-lhe voltar atrás para qualquer ponto no tempo (até 7 dias em Basic) e 35 dias num padrão e restaurar este ponto no tempo para um novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. 

O seguinte comando restaura o servidor de exemplo para um ponto antes da tabela foi adicionada:
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

O `az postgres server restore` comando tem os seguintes parâmetros:
| Definição | Valor sugerido | Descrição  |
| --- | --- | --- |
| -grupo de recursos |  myResourceGroup |  O grupo de recursos no qual o servidor de origem existe.  |
| – nome | restaurar o mypgserver | O nome do novo servidor que é criado pelo comando restore. |
| restauro ponto no tempo | 2017-04-13T13:59:00Z | Selecione um ponto no tempo para restaurar. Esta data e hora tem de ser dentro do período de retenção de cópias de segurança do servidor de origem. Utilize o formato de data e hora ISO8601. Por exemplo, pode utilizar o seus próprios fuso horário local, tais como `2017-04-13T05:59:00-08:00`, ou utilize o formato UTC Zulu `2017-04-13T13:59:00Z`. |
| -servidor de origem | mypgserver 20170401 | O nome ou ID do servidor de origem para restaurar a partir de. |

Restaurar um servidor para um ponto no tempo cria um novo servidor, copiado como o servidor original a partir do ponto no tempo que especificar. A localização e o preço de camada de valores para o servidor restaurado são os mesmos que o servidor de origem.

O comando é síncrono e irá devolver depois do servidor é restaurado. Depois do restauro concluir, localize o novo servidor que foi criado. Certifique-se de que os dados foram restaurados conforme esperado.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a utilizar a CLI do Azure (interface de linha de comandos) e outros utilitários para:
> [!div class="checklist"]
> * Criar uma Base de Dados do Azure para o servidor PostgreSQL
> * Configurar a firewall do servidor
> * Utilize [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) utilitário para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Seguidamente, saiba como utilizar o portal do Azure para realizar tarefas semelhantes, reveja este tutorial: [conceber a sua primeira base de dados do Azure para PostgreSQL no portal do Azure](tutorial-design-database-using-azure-portal.md)
