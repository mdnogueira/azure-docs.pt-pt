---
title: Conceber a sua primeira base de dados do Azure para a base de dados MySQL - CLI do Azure | Microsoft Docs
description: Este tutorial explica como criar e gerir a base de dados do Azure para o servidor de MySQL e base de dados utilizando o Azure CLI 2.0 na linha de comandos.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/03/2017
ms.custom: mvc
ms.openlocfilehash: dcd59442c0b3aa5d6ed1a9ef287949d1d17fa80f
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Conceber a sua primeira base de dados do Azure para a base de dados MySQL

Base de dados do Azure para MySQL é um serviço de base de dados relacional em nuvem da Microsoft com base no motor de base de dados MySQL Comunidade edição. Neste tutorial, utilize a CLI do Azure (interface de linha de comandos) e outros utilitários para saber como:

> [!div class="checklist"]
> * Criar uma base de dados do Azure para MySQL
> * Configurar a firewall do servidor
> * Utilize [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Pode utilizar a Shell de nuvem do Azure no browser, ou [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli) no seu computador para executar os blocos de código neste tutorial.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer que está a executar a CLI do Azure versão 2.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado. Selecione um ID de subscrição específica na sua conta com o comando [az account set](/cli/azure/account#set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Criar um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) com [criar grupo az](https://docs.microsoft.com/cli/azure/group#az_group_create) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo seguinte cria um grupo de recursos com o nome `mycliresource` na localização `westus`.

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Crie uma base de dados do Azure para o servidor de MySQL com o servidor de mysql az criar comando. Cada servidor pode gerir múltiplas bases de dados. Geralmente, é utilizada uma base de dados em separado para cada projeto ou para cada utilizador.

O exemplo seguinte cria uma Base de Dados do Azure para o servidor MySQL localizada em `westus` no grupo de recursos `mycliresource` com o nome `mycliserver`. O servidor tem um início de sessão de administrador com o nome `myadmin` e a palavra-passe `Password01!`. É criado com o escalão de desempenho **Básico** e com **50** unidades de computação partilhadas entre todas as bases de dados do servidor. Pode aumentar ou reduzir verticalmente a computação e o armazenamento, dependendo das necessidades da aplicação.

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver
--location westus --user myadmin --password Password01!
--performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Configurar a regra de firewall
Crie uma base de dados do Azure para comandos de criação de regra de firewall ao nível do servidor de MySQL com a az mysql servidor-regra de firewall. Uma regra de firewall ao nível do servidor permite que uma aplicação externa, tais como **mysql** ferramenta da linha de comandos ou MySQL Workbench para ligar ao seu servidor através da firewall do serviço de MySQL do Azure. 

O exemplo seguinte cria uma regra de firewall para um intervalo de endereços predefinidas. Este exemplo mostra todo possíveis intervalo de endereços IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Para ligar ao seu servidor, terá de fornecer credenciais de acesso e informações de anfitrião.
```azurecli-interactive
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

## <a name="connect-to-the-server-using-mysql"></a>Ligar ao servidor utilizando o mysql
Utilize o [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) estabelecer uma ligação à base de dados do Azure para o servidor de MySQL. Neste exemplo, o comando é:
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia
Assim que o se estiver ligado ao servidor, crie uma base de dados em branco.
```sql
mysql> CREATE DATABASE mysampledb;
```

Na linha de comandos, execute o seguinte comando para mudar a ligação a este criado recentemente a base de dados:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à base de dados do Azure para a base de dados MySQL, conclua algumas tarefas básicas:

Em primeiro lugar, crie uma tabela e carregá-lo com alguns dados. Vamos criar uma tabela que armazena informações de inventário.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas
Agora que tem uma tabela, inserir alguns dados na mesma. A janela de linha de comandos aberta, execute a seguinte consulta para inserir alguns linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tem agora duas linhas de dados de exemplo para a tabela que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela da base de dados.
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados nas tabelas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha obtém atualizada em conformidade ao obter dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo
Imagine que tenha eliminado acidentalmente nesta tabela. Este é algo que facilmente não consegue recuperar. Base de dados do Azure para MySQL permite-lhe voltar atrás para qualquer ponto no tempo no último até 35 dias e restaurar este ponto no tempo para um novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. Os seguintes passos restaurar o servidor de exemplo para um ponto antes da tabela foi adicionada.

Para o restauro, terá as seguintes informações:

- Ponto de restauro: selecione um ponto no tempo que ocorre antes do servidor foi alterado. Tem de ser maior que ou igual ao valor de cópia de segurança mais antigo a origem da base de dados.
- Servidor de destino: forneça um novo nome de servidor que pretende restaurar para
- Servidor de origem: forneça o nome do servidor que pretende restaurar a partir de
- Localização: Não é possível selecionar a região, por predefinição é igual ao servidor de origem

```azurecli-interactive
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

Para restaurar o servidor e [restaurar para um ponto no tempo](./howto-restore-server-portal.md) antes da tabela foi eliminada. Restaurar um servidor para um outro ponto no tempo cria um novo servidor duplicado como o servidor original a partir do ponto no tempo que especificar, desde que esteja dentro do período de retenção para sua [camada de serviço](./concepts-service-tiers.md).

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, que aprendeu a:
> [!div class="checklist"]
> * Criar uma base de dados do Azure para MySQL
> * Configurar a firewall do servidor
> * Utilize [ferramenta da linha de comandos mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar uma base de dados
> * Carregar dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

> [!div class="nextstepaction"]
> [Base de dados do Azure para MySQL - amostras da CLI do Azure](./sample-scripts-azure-cli.md)
