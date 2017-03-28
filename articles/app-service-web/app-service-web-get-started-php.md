---
title: "Criar a primeira aplicação Web PHP no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações mediante a implementação de uma aplicação PHP de exemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: afecc8997631bf507c797e56a9e3fc0d1df27614
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-php-web-app-in-azure-in-five-minutes"></a>Criar a primeira aplicação Web PHP no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Este Manual de Início Rápido ajuda-o a implementar a sua primeira aplicação Web PHP no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Iniciar sessão no Azure através da execução de `az login` e das seguintes direções apresentadas no ecrã.
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>Criar um grupo de recursos   
Criar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Este é o local onde colocar todos os recursos Azure que pretende gerir em conjunto, tais como a aplicação web e o respetivo back-end da Base de Dados SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Para ver os possíveis valores que pode utilizar para `---location`, utilize o comando do Azure CLI`az appservice list-locations`.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações
Criar um [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "Standard" que execute um contentor do Linux. 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web
Crie uma aplicação Web com um nome exclusivo em `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>Configurar o contentor do Linux
Configure o contentor do Linux ao utilizar a imagem PHP 7.0.6 predefinida.

```azurecli
az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
```
## <a name="deploy-sample-application"></a>Implementar aplicação de exemplo
Implemente uma aplicação PHP de exemplo a partir do GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Navegue para a aplicação Web
Para ver a aplicação em execução em direto no Azure, execute este comando.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Parabéns, a primeira aplicação Web PHP está em execução em direto no Serviço de Aplicações do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os [Scripts de CLI de aplicações Web](app-service-cli-samples.md) criados anteriormente.

