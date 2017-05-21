---
title: "Guia de introdução: criar uma Base de Dados do Azure para o servidor MySQL - CLI do Azure | Microsoft Docs"
description: "Este guia de introdução descreve como utilizar a CLI do Azure para criar uma Base de Dados do Azure para o servidor MySQL num grupo de recursos do Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 574299dd64120d75a1a36cb2ded0fdd269292570
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure
Este guia de introdução descreve como utilizar a CLI do Azure para criar uma Base de Dados do Azure para o servidor MySQL num grupo de recursos do Azure em cerca de cinco minutos. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts.

Para concluir este guia de introdução, confirme que tem instalada a [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) mais recente. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```
Siga as instruções da linha de comandos para abrir https://aka.ms/devicelog no seu browser e, em seguida, introduza o código gerado na **linha de comandos**.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) com o comando [az group create](https://docs.microsoft.com/cli/azure/group#create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo seguinte cria um grupo de recursos com o nome `mycliresource` na localização `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Crie uma Base de Dados do Azure para o servidor MySQL com o comando **az mysql server create**. Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

O exemplo seguinte cria uma Base de Dados do Azure para o servidor MySQL localizada em `westus` no grupo de recursos `mycliresource` com o nome `mycliserver`. O servidor tem um início de sessão de administrador com o nome `myadmin` e a palavra-passe `Password01!`. É criado com o escalão de desempenho **Básico** e com **50** unidades de computação partilhadas entre todas as bases de dados do servidor. Pode aumentar ou reduzir verticalmente a computação e o armazenamento, dependendo das necessidades da aplicação.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Configurar a regra de firewall
Crie uma regra de firewall ao nível da Base de Dados do Azure para o servidor MySQL com o comando **az mysql server firewall-rule create**. A regra de firewall ao nível do servidor permite que uma aplicação externa, como a ferramenta de linha de comandos **mysql.exe** ou o MySQL Workbench, se ligue ao seu servidor através da firewall do serviço Azure MySQL. 

O exemplo seguinte cria uma regra de firewall para um intervalo de endereços predefinido que, neste exemplo, constitui todo o intervalo de endereços IP possível.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configurar as definições de SSL
Por predefinição, são aplicadas ligações SSL entre o servidor e as aplicações cliente.  Isto garante a segurança dos dados "em movimento" ao encriptar o fluxo de dados através da Internet.  Para facilitar este guia de introdução, vamos desativar as ligações SSL no seu servidor.  A desativação não é recomendada para servidores de produção.  Para obter mais detalhes, veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL).

O exemplo seguinte desativa a aplicação de SSL no seu servidor MySQL.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

O resultado está no formato JSON. Aponte o **fullyQualifiedDomainName** e o **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Ligar ao servidor com a ferramenta de linha de comandos mysql.exe
Para ligar ao servidor com a ferramenta de linha de comandos **mysql.exe**, confirme que tem a instalação do MySQL no seu computador.  Pode transferir o MySQL [aqui](https://dev.mysql.com/downloads/).

Abra a linha de comandos e introduza o seguinte: 

1. Ligar ao servidor com a ferramenta de linha de comandos **mysql**:
```dos
 mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

2. Ver o estado do servidor:
```dos
 mysql> status
```
Se tudo correr bem, a ferramenta de linha de comandos deve produzir o seguinte:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mycliserver.database.windows.net via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Para obter comandos adicionais, veja [MySQL 5.6 Reference Manual - Chapter 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) (Manual de Referência do MySQL 5.6 - Capítulo 4.5.1).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Ligar ao servidor com a ferramenta Workbench GUI do MySQL
1.    Inicie a aplicação MySQL Workbench no computador cliente. Pode transferir e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.    Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

| **Parâmetros** | **Descrição** |
|----------------|-----------------|
|    *Nome da Ligação* | especifique um nome para esta ligação (pode ser qualquer nome) |
| *Método de Ligação* | escolha Standard (TCP/IP) |
| *Nome de Anfitrião* | mycliserver.database.windows.net (o NOME DO SERVIDOR que apontou anteriormente) |
| *Porta* | 3306 |
| *Nome de Utilizador* | myadmin@mycliserver (o INÍCIO DE SESSÃO DE ADMINISTRADOR DO SERVIDOR que apontou anteriormente) |
| *Palavra-passe* | pode armazenar a palavra-passe da conta de administrador no cofre |

![configurar ligação nova](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

3.    Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente.

4.    Agora, pode clicar na ligação que acabou de criar para ligar com êxito ao servidor.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisa destes recursos para outro início rápido/tutorial, pode eliminá-los ao realizar o seguinte procedimento: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Design a MySQL Database with Azure CLI](./tutorial-design-database-using-cli.md) (Conceber uma Base de Dados MySQL com a CLI do Azure).

