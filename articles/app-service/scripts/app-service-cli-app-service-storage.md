---
title: "Script CLI do Azure de exemplo - ligar uma aplicação web a uma conta de armazenamento | Microsoft Docs"
description: "Script CLI do Azure de exemplo - ligar uma aplicação web a uma conta de armazenamento"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: a07c2ae64be2081a2dba172ada810b76bfd7dfd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-a-storage-account"></a>Ligar uma aplicação web a uma conta de armazenamento

Neste cenário, irá aprender a criar uma conta de armazenamento do Azure e uma aplicação web do Azure. Em seguida, vai ligar a conta de armazenamento para a aplicação web utilizando as definições de aplicação.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, a aplicação web, a conta de armazenamento e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar plano de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano de serviço de aplicações. Trata-se como um farm de servidores para a sua aplicação web do Azure. |
| [Criar AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Cria uma aplicação web do Azure. |
| [criar conta de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. Este é onde serão armazenados os elementos estáticos. |
| [AZ armazenamento conta Mostrar--cadeia de ligação](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_show_connection_string) | |
| [AZ webapp configuração appsettings conjunto](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Cria ou atualiza uma definição de aplicação para uma aplicação web do Azure. As definições de aplicação são expostas como variáveis de ambiente para a sua aplicação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de aplicação serviço CLI adicionais podem ser encontrados no [documentação do App Service do Azure](../app-service-cli-samples.md).
