---
title: "Exemplo de Script CLI do Azure - monitorizar uma aplicação web com os registos do servidor web | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - monitorizar uma aplicação web com os registos do servidor web"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 9630656a00a8a80c8ffd8fb59113dc1c8c523651
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-a-web-app-with-web-server-logs"></a>Monitorizar uma aplicação web com os registos do servidor web

Neste cenário que irá criar um grupo de recursos, o plano do app service, a aplicação web e configurar a aplicação web para permitir que os registos do servidor web. Em seguida, irá transferir os ficheiros de registo para revisão.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma aplicação web e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar plano de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano de serviço de aplicações. Trata-se como um farm de servidores para a sua aplicação web do Azure. |
| [Criar AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Cria uma aplicação web do Azure. |
| [configuração de registo do AZ webapp](https://docs.microsoft.com/cli/azure/webapp/log#az_webapp_log_config) | Configura os registos de uma aplicação web do Azure serão mantidas. |
| [transferência de registo AZ webapp](https://docs.microsoft.com/cli/azure/webapp/log#az_webapp_log_download) | Transfere os registos da uma aplicação web do Azure no seu computador local. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de aplicação serviço CLI adicionais podem ser encontrados no [documentação do App Service do Azure](../app-service-cli-samples.md).
