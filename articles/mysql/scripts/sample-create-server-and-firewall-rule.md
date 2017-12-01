---
title: CLI do Azure Script - criar uma base de dados do Azure para MySQL | Microsoft Docs
description: "Este script de exemplo do CLI cria uma base de dados do Azure para o servidor de MySQL e configura uma regra de firewall ao nível do servidor."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/02/2017
ms.openlocfilehash: ee0d7e1d060492cabc78eeff098057bf9c09c6d1
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Criar um servidor de MySQL e configurar uma regra de firewall utilizando a CLI do Azure
Este script de exemplo do CLI cria uma base de dados do Azure para o servidor de MySQL e configura uma regra de firewall ao nível do servidor. Assim que o script é executado com êxito, o servidor de MySQL é acedido por todos os serviços do Azure e o endereço IP configurado.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Este script de exemplo, edite as linhas realçadas para personalizar o nome de utilizador de administrador e a palavra-passe.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação de script
Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| **Comando** | **Notas** |
|---|---|
| [Criar grupo AZ](/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar servidor do AZ mysql](/cli/azure/mysql/server#az_msql_server_create) | Cria um servidor de MySQL que aloja as bases de dados. |
| [criar a firewall do servidor AZ mysql](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) | Cria uma regra de firewall para permitir o acesso ao servidor e bases de dados-lo do intervalo de endereços IP introduzido. |
| [eliminação do grupo de AZ](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview).
- Tente scripts adicionais: [amostras da CLI do Azure para a base de dados do Azure para MySQL](../sample-scripts-azure-cli.md)
