---
title: "Exemplo de Script do CLI do Azure - criar uma aplicação web e implementar o código para um ambiente de teste | Microsoft Docs"
description: "Exemplo de Script do CLI do Azure - criar uma aplicação web e implementar o código para um ambiente de teste"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 449e5729a15e619c43e5f4a0643915c2d3114d17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Criar uma aplicação web e implementar o código para um ambiente de teste

Este script de exemplo cria uma aplicação web no App Service, com um bloco de implementação adicionais denominado "testes" e, em seguida, implementa uma aplicação de exemplo para a ranhura de "transição".

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create a web app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar plano de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano de serviço de aplicações. |
| [Criar AZ webapp](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Cria uma aplicação web do Azure. |
| [criar a ranhura de implementação do AZ webapp](https://docs.microsoft.com/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_create) | Crie uma ranhura de implementação. |
| [AZ webapp configuração da origem de implementação](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Associa um repositório de Git ou Mercurial uma aplicação web do Azure. |
| [Procurar de webapp AZ](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Abra uma aplicação web do Azure num browser. |
| [troca de ranhura de implementação do AZ webapp](https://docs.microsoft.com/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_swap) | Comutação uma ranhura de implementação especificado em produção. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de aplicação serviço CLI adicionais podem ser encontrados no [documentação do App Service do Azure](../app-service-cli-samples.md).
