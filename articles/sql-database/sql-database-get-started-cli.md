---
title: "CLI do Azure: criar uma única base de dados SQL | Microsoft Docs"
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados com a CLI do Azure."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 04/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 24a99c20dc015b15de980e8323f2d88a39d318dd
ms.lasthandoff: 04/12/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Criar uma única base de dados SQL do Azure com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha a utilização da CLI do Azure para implementar uma base de dados SQL do Azure num [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) num [servidor lógico de Base de Dados SQL do Azure](sql-database-features.md).

Para concluir este início rápido, confirme que tem instalado a [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) mais recente. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico de Base de Dados SQL do Azure](sql-database-features.md) com o comando [az sql server create](/cli/azure/sql/server#create). Um servidor lógico contém um grupo de bases de dados geridas como um grupo. O exemplo seguinte cria um servidor com um nome aleatório no seu grupo de recursos com um início de sessão de administrador denominado `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma [regra de firewall ao nível do servidor da Base de Dados SQL do Azure](sql-database-firewall-configure.md) com o comando [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Uma regra de firewall ao nível do servidor permite a uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, ligar a uma base de dados SQL através da firewall do serviço Base de Dados SQL. No exemplo seguinte, a firewall apenas é aberta para outros recursos do Azure. Para ativar a conectividade externa, altere o endereço IP para um endereço adequado para o seu ambiente. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.  

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao seu servidor de Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Criar uma base de dados no servidor com dados de exemplo

Crie uma base de dados com um [nível de desempenho S0](sql-database-service-tiers.md) no servidor com o comando [az sql db create](/cli/azure/sql/db#create). O exemplo seguinte cria uma base de dados denominada `mySampleDatabase` e carrega os dados de exemplo AdventureWorksLT para esta base de dados. Substitua estes valores predefinidos conforme pretender (outros guias de introdução nesta coleção são criados mediante os valores neste guia de introdução).

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução nesta coleção são criados através deste guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, utilize os comandos seguintes para eliminar todos os recursos criados por este guia de introdução.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](sql-database-connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).

