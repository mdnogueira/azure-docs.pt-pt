---
title: "Script CLI do Azure de exemplo - ligar uma aplicação web numa base de dados SQL | Microsoft Docs"
description: "Script CLI do Azure de exemplo - ligar uma aplicação web numa base de dados do SQL Server"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 12e373a6503127d57f5fc1ed719c82bf56aed60c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Ligar uma aplicação web numa base de dados do SQL Server

Neste cenário, irá aprender a criar uma base de dados SQL do Azure e uma aplicação web do Azure. Em seguida, vai ligar a base de dados do SQL Server para a aplicação web utilizando as definições de aplicação.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, aplicação web, base de dados do SQL Server e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar plano de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano de serviço de aplicações. Trata-se como um farm de servidores para a sua aplicação web do Azure. |
| [Criar AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Cria uma aplicação web do Azure. |
| [servidor de sql AZ criar](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Cria um servidor de base de dados do SQL Server.  |
| [Criar BD do sql AZ](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_create) | Cria uma nova base de dados com o servidor de base de dados do SQL Server. |
| [AZ webapp configuração appsettings conjunto](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Cria ou atualiza uma definição de aplicação para uma aplicação web do Azure. As definições de aplicação são expostas como variáveis de ambiente para a sua aplicação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de aplicação serviço CLI adicionais podem ser encontrados no [documentação do App Service do Azure](../app-service-cli-samples.md).
