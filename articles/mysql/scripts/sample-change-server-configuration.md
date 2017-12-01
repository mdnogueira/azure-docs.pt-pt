---
title: "Script CLI do Azure - configurações de servidor de alteração | Microsoft Docs"
description: "Este script de exemplo do CLI apresenta uma lista de todas as configurações de servidor disponível e atualiza o valor de innodb_lock_wait_timeout."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/03/2017
ms.openlocfilehash: 9a94f257e5cd3534127e8594ddee3c5f837876df
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Configurações de lista e a atualização de uma base de dados do Azure para o servidor de MySQL utilizando a CLI do Azure
Este script de exemplo do CLI apresenta uma lista de todos os parâmetros de configuração disponíveis, bem como os respetivos valores permitidos para a base de dados do Azure para o servidor de MySQL e define o *innodb_lock_wait_timeout* para um valor que é diferente da predefinição um.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Este script de exemplo, altere as linhas realçadas para personalizar o nome de utilizador de administrador e a palavra-passe.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação de script
Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| **Comando** | **Notas** |
|---|---|
| [Criar grupo AZ](/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar servidor do AZ mysql](/cli/azure/mysql/server#az_msql_server_create) | Cria um servidor de MySQL que aloja as bases de dados. |
| [lista de configuração de servidor do AZ mysql](/cli/azure/mysql/server/configuration#az_msql_server_configuration_list) | Lista as configurações de uma base de dados do Azure para o servidor de MySQL. |
| [conjunto de configuração do servidor do AZ mysql](/cli/azure/mysql/server/configuration#az_msql_server_configuration_set) | Atualize a configuração de uma base de dados do Azure para o servidor de MySQL. |
| [Mostrar de configuração de servidor do AZ mysql](/cli/azure/mysql/server/configuration#az_msql_server_configuration_show) | Mostra a configuração de uma base de dados do Azure para o servidor de MySQL. |
| [eliminação do grupo de AZ](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview).
- Tente scripts adicionais: [amostras da CLI do Azure para a base de dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre os parâmetros de servidor, consulte [como para configurar parâmetros de servidor na base de dados do Azure para MySQL](../howto-server-parameters.md).
