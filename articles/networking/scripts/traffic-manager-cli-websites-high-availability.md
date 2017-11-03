---
title: "Exemplo de Script CLI do Azure - encaminhar o tráfego para elevada disponibilidade de aplicações | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - encaminhar o tráfego para elevada disponibilidade de aplicações"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 5c3754c3c3c96e1d2f0b5b52d8108ecc3903f788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Encaminhar o tráfego para elevada disponibilidade de aplicações

Este script cria um grupo de recursos, dois planos de serviço de aplicações, duas aplicações web, um perfil do Gestor de tráfego e dois pontos finais Gestor de tráfego. Gestor de tráfego direciona o tráfego para a aplicação de uma região como região primária e a região secundária quando a aplicação na região primária não está disponível. Antes de executar o script, tem de alterar os valores MyWebApp, MyWebAppL1 e MyWebAppL2 para valores exclusivos através do Azure. Depois de executar o script, pode aceder à aplicação na região primária com mywebapp.trafficmanager.net o URL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, pode ser utilizado o comando de seguir para remover o grupo de recursos, a aplicação do app Service e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, aplicação web, perfil do Gestor de tráfego e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar plano de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano de serviço de aplicações. Trata-se como um farm de servidores para a sua aplicação web do Azure. |
| [criar web de serviço aplicacional AZ](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | Cria uma aplicação web do Azure dentro do plano do App Service. |
| [Criar perfil de Gestor de tráfego de rede AZ](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Cria um perfil do Traffic Manager do Azure. |
| [criar o ponto final do Gestor de tráfego de rede AZ](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Adiciona um ponto final de um perfil do Traffic Manager do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script de aplicação serviço CLI adicionais podem ser encontrados no [redes do Azure documentação](../cli-samples.md).
