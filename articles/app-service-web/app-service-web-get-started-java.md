---
title: "Criar a primeira aplicação Web Java no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação de exemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: e48e03e86a325b8f39809a49cdd19820dfa78bdc
ms.lasthandoff: 03/10/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Criar a primeira aplicação Web Java no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Este Manual de Início Rápido ajuda-o a implementar a sua primeira aplicação Web Java no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

Antes de iniciar este Manual de Início Rápido, certifique-se de que [a CLI do Azure está instalada](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) no computador.

## <a name="create-a-java-web-app-in-azure"></a>Criar uma aplicação Web Java no Azure
2. Iniciar sessão no Azure através da execução de `az login` e das seguintes direções apresentadas no ecrã.
   
    ```azurecli
    az login
    ```
   
3. Criar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Este é o local onde colocar todos os recursos Azure que pretende gerir em conjunto, tais como a aplicação web e o respetivo back-end da Base de Dados SQL.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Para ver os possíveis valores que pode utilizar para `---location`, utilize o comando do Azure CLI`az appservice list-locations`.

3. Crie um [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Crie uma nova aplicação Web com um nome exclusivo em `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Implemente uma aplicação Java de exemplo a partir do GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/azure-appservice-samples/JavaCoffeeShopTemplate.git" --branch master --manual-integration 
    ```


5. Para ver a aplicação em execução em direto no Azure, execute este comando.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Parabéns, a primeira aplicação Web Java está em execução em direto no Serviço de Aplicações do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os [Scripts de CLI de aplicações Web](app-service-cli-samples.md) criados anteriormente.

