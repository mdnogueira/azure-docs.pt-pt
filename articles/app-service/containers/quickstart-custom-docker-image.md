---
title: "Executar uma imagem personalizada do Docker Hub na aplicação de Web do Azure para contentores | Microsoft Docs"
description: "Como utilizar uma imagem personalizada do Docker para aplicação de Web do Azure para contentores."
keywords: "serviço de aplicações do Azure, aplicação web, linux, docker, contentor"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
ms.openlocfilehash: 8e7afd89def170ce756aae9e76daf91d78cc20e0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Executar uma imagem personalizada do Docker Hub na aplicação de Web do Azure para contentores

O App Service fornece pilhas de aplicação previamente definido no Linux com suporte para versões específicas, tais como PHP 7.0 e 4.5 do Node.js. Também pode utilizar uma imagem personalizada do Docker para executar a aplicação web numa pilha de aplicação que já não está definida no Azure. Este guia de introdução mostra como criar uma aplicação web e implementar o [imagem oficial do Nginx Docker](https://hub.docker.com/r/_/nginx/) ao mesmo. Criar a aplicação web através de [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Aplicação de exemplo em execução no Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Criar uma aplicação Web para o contentor

Crie uma [aplicação Web](../app-service-web-overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp#create). Não se esqueça de substituir `<app name>` com um nome de aplicação único.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

No comando anterior, `--deployment-container-image-name` aponta para a imagem de Hub de Docker pública [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

Quando a aplicação web tiver sido criada, a CLI do Azure mostra resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Procure o seguinte URL a utilizar o seu browser.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicação de exemplo em execução no Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Parabéns!** Implementar uma imagem personalizada do Docker à aplicação Web para contentores.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Utilizar uma imagem personalizada do Docker](tutorial-custom-docker-image.md)
