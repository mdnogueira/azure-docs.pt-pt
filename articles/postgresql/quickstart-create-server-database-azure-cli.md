---
title: Criar uma Base de Dados do Azure para PostgreSQL com a CLI do Azure | Microsoft Docs
description: "Guia de introdução para criar e gerir a Base de Dados do Azure para o servidor PostgreSQL com a CLI (interface de linha de comandos) do Azure."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: hero-article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: b2be9e265075c58ed53a0a49c01a08e05db35a06
ms.contentlocale: pt-pt
ms.lasthandoff: 06/20/2017

---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Criar uma Base de Dados do Azure para PostgreSQL com a CLI do Azure
A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de introdução mostra-lhe como criar uma Base de Dados do Azure para o servidor PostgreSQL num [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) com a CLI do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso vai ser cobrado. Selecione um ID de subscrição específica na sua conta com o comando [az account set](/cli/azure/account#set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myresourcegroup` na localização `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar uma Base de Dados do Azure para o servidor PostgreSQL

Crie uma [Base de Dados do Azure para o servidor PostgreSQL](overview.md) com o comando [az postgres server create](/cli/azure/postgres/server#create). Os servidores contêm um grupo de bases de dados geridas como um grupo. 

O exemplo seguinte cria um servidor com o nome `mypgserver-20170401` no seu grupo de recursos `myresourcegroup` com o início de sessão de administrador do servidor `mylogin`. O nome de um servidor mapeia para o nome DNS e, por conseguinte, é necessário para ser globalmente exclusivo no Azure. Substitua `<server_admin_password>` pelo seu próprio valor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

Por predefinição, é criada a base de dados **postgres** no seu servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Crie uma regra de firewall ao nível do servidor do Azure PostgreSQL com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). A regra de firewall ao nível do servidor permite que uma aplicação externa, como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), se ligue ao seu servidor através da firewall do serviço Azure PostgreSQL. 

Pode definir uma regra de firewall que abrange um intervalo IP para conseguir estabelecer ligação a partir da sua rede. O exemplo seguinte utiliza [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) para criar a regra de firewall `AllowAllIps` para um intervalo de endereços IP. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> O servidor PostgreSQL do Azure comunica através da porta 5432. Ao ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Peça ao departamento de TI para abrir a porta 5432, para ligar ao servidor da Base de Dados SQL do Azure.

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

## <a name="connect-to-postgresql-database-using-psql"></a>Ligar à base de dados do PostgreSQL com psql

Se o seu computador cliente tiver o PostgreSQL instalado, pode utilizar uma instância local de [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para ligar a um servidor PostgreSQL do Azure. Vamos utilizar agora o utilitário da linha de comandos psql para ligar ao servidor PostgreSQL do Azure.

1. Execute o comando psql seguinte para ligar a uma Base de Dados do Azure para o servidor PostgreSQL
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mypgserver-20170401.postgres.database.azure.com** com as credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Assim que estiver ligado ao servidor, crie uma base de dados em branco na linha de comandos.
```sql
CREATE DATABASE mypgsqldb;
```

3.  Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** criada recentemente:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Ligar-se à base de dados do PostgreSQL com pgAdmin

Para ligar ao servidor PostgreSQL do Azure com a ferramenta _pgAdmin_ da GUI
1.  Inicie a aplicação _pgAdmin_ no computador cliente. Pode instalar a _pgAdmin_ a partir do site http://www.pgadmin.org/.
2.  Escolha **Adicionar Novo Servidor**, no menu **Ligações Rápidas**.
3.  Na caixa de diálogo **Criar - Servidor**, separador **Geral**, introduza um Nome amigável exclusivo para o servidor. Suponhamos **Azure PostgreSQL Server**.
 ![ferramenta pgAdmin - criar - servidor](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  Na caixa de diálogo **Criar - Servidor**, separador **Ligação**:
    - Introduza o nome de servidor completamente qualificado (por exemplo, **mypgserver-20170401.postgres.database.azure.com**) na caixa **Nome de Anfitrião/Endereço**. 
    - Introduza a porta 5432 na caixa **Porta**. 
    - Introduza o **Início de sessão de administrador do servidor (user@mypgserver)** obtido anteriormente neste guia de introdução e a palavra-passe que introduziu quando criou o servidor nas caixas **Nome de utilizador** e **palavra-passe**, respetivamente.
    - Selecione **Modo SSL** como **Exigir**. Por predefinição, todos os servidores PostgreSQL do Azure são criados com a imposição de SSL ativada. Para desativar a imposição de SSL, veja os detalhes em [Enforcing SSL](./concepts-ssl-connection-security.md) (Impor SSL).

    ![pgAdmin - criar - servidor](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Clique em **Guardar**.
6.  No painel esquerdo do Browser, expanda os **Grupos de Servidores**. Selecione o **Servidor PostgreSQL do Azure**.
7.  Escolha o **Servidor** ao qual se ligou e, em seguida, escolha **Bases de Dados** abaixo do mesmo. 
8.  Clique com o botão direito do rato em **Bases de Dados** para Criar uma Base de Dados.
9.  Escolha um nome de base de dados, **mypgsqldb**, e o seu proprietário como o início de sessão de administrador do servidor **mylogin**.
10. Clique em **Guardar** para criar uma base de dados em branco.
11. No **Browser**, expanda o grupo **Servidores**. Expanda o servidor que criou e veja a base de dados **mypgsqldb** abaixo do mesmo.
 ![pgAdmin – Criar – Base de Dados](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que criou no guia de introdução ao eliminar o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Serão criados outros guias de introdução nesta coleção através deste guia. Se quiser continuar a trabalhar com os guias de introdução subsequentes, não limpe os recursos criados neste guia. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução na CLI do Azure.

```azurecli-interactive
az group delete --name myresourcegroup
```

Se pretende eliminar o único servidor criado recentemente, pode executar o comando [az postgres server delete](/cli/azure/postgres/server#delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com as opções Exportar e Importar](./howto-migrate-using-export-and-import.md)

