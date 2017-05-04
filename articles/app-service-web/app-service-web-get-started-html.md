---
title: "Criar uma aplicação Web HTML estática no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação de exemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ba9b9b780da74c44f6314fa289f1d6b8c231dd30
ms.lasthandoff: 05/03/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Criar uma aplicação Web HTML estática no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este Manual de Início Rápido ajuda-o a implementar um HTML+CSS simples no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

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

Para ver os possíveis valores que pode utilizar para `--location`, utilize o comando do Azure CLI`az appservice list-locations`.


## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações
Crie um [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web
Crie uma aplicação Web com um nome exclusivo em `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="deploy-sample-application"></a>Implementar aplicação de exemplo
Implemente um site HTML de exemplo a partir do GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-html-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Navegue para a aplicação Web
Para ver a aplicação em execução em direto no Azure, execute este comando.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Parabéns, o seu primeiro site HTML estático está em execução em direto no Serviço de Aplicações do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os [Scripts de CLI de aplicações Web](app-service-cli-samples.md) criados anteriormente.

