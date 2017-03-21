---
title: "CLI do Azure: Criar e consultar uma única base de dados SQL | Microsoft Docs"
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados com a CLI do Azure."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Criar e consultar uma única base de dados SQL do Azure com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha a utilização da CLI do Azure para implementar uma base de dados SQL do Azure.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um servidor lógico com o comando [az sql server create](/cli/azure/sql/server#create). O exemplo seguinte cria um servidor com um nome aleatório no seu grupo de recursos com um início de sessão de administrador denominado `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma regra de firewall ao nível do servidor com o comando [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Uma regra de firewall ao nível do servidor permite a uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, ligar a uma base de dados SQL através da firewall do serviço Base de Dados SQL. O exemplo seguinte cria uma regra de firewall para um intervalo de endereços predefinido que, neste exemplo, constitui todo o intervalo de endereços IP possível. Substitua estes valores predefinidos pelos valores do seu endereço IP externo ou intervalo de endereços IP. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Criar uma base de dados no servidor

Crie uma base de dados no servidor com o comando [az sql db create](/cli/azure/sql/db#create). O exemplo seguinte cria uma base de dados vazia denominada `mySampleDatabase`. Substitua este valor predefinido conforme quiser.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados por este Manual de Início Rápido, execute o seguinte comando:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Para ligar com o Visual Studio, veja [Ligar e consultar com o Visual Studio](sql-database-connect-query.md).
- Para obter uma descrição geral técnica da Base de Dados SQL, veja [Acerca do serviço Base de Dados SQL](sql-database-technical-overview.md).

