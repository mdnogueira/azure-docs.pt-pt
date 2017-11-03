---
title: "Exemplo de Script CLI do Azure - enlace de certificado para uma aplicação de função um SSL personalizado | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - vincular um certificado SSL personalizado para uma aplicação de função no Azure"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 76602f63f8b7fd7adc38353756387484f0af6493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Vincular um certificado SSL personalizado para uma aplicação de função

Este script de exemplo cria uma aplicação de função no serviço de aplicações com os respetivos recursos relacionados, em seguida, vincula o certificado SSL de um nome de domínio personalizado ao mesmo. Para este exemplo, precisa de:

* Acesso à página de configuração de DNS do seu domínio da entidade de registo.
* Válido. Ficheiro PFX e a respetiva palavra-passe do certificado SSL que pretende carregar e vincular.

Para vincular um certificado SSL, a aplicação de função tem de ser criada um plano de serviço de aplicações e não num plano de consumo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar plano de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano de serviço de aplicações necessário para vincular certificados SSL. |
| [Criar AZ functionapp]() | Cria uma aplicação de função. |
| [Adicione o nome de anfitrião do AZ appservice web configuração](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Mapeia um domínio personalizado para a aplicação de função. |
| [carregamento de ssl de configuração do AZ serviço de aplicações web](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_upload) | Carrega um certificado SSL para uma aplicação de função. |
| [enlace de ssl de configuração do AZ serviço de aplicações web](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_bind) | Vincula um certificado SSL carregado para uma aplicação de função. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de aplicação serviço CLI adicionais podem ser encontrados no [documentação do App Service do Azure]().
