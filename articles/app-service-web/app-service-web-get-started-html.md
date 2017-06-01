---
title: "Criar uma aplicação Web HTML estática no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação de exemplo."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Criar uma aplicação Web HTML estática no Azure em cinco minutos

Este guia de introdução mostra-lhe como implementar um site HTML+CSS básico no Azure. Vai executar a aplicação através de um [plano do Serviço de Aplicações do Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) e criar uma aplicação Web no mesmo com a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli). Vai utilizar o Git para implementar a aplicação no Azure. Depois de instalados os pré-requisitos, o tutorial demora cerca de cinco minutos a ser concluído.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar este exemplo, transfira e instale os componentes seguintes:

- [Git](https://git-scm.com/)
- [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do terminal, clone o repositório da aplicação de exemplo para o seu computador local:

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>Ver o HTML

Navegue para o diretório que contém o HTML de exemplo. Abra o ficheiro *index.html* no browser.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

Crie uma [Aplicação Web](app-service-web-overview.md) no plano do Serviço de Aplicações `quickStartPlan`. A aplicação Web fornece um espaço de alojamento para o código e um URL para ver a aplicação implementada.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

A página está a ser executada como uma aplicação Web do Serviço de Aplicações do Azure:

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Atualizar e reimplementar a aplicação

Abra o ficheiro *index.html*. Altere a marcação. Por exemplo, altere `Hello world!` para `Hello Azure!`

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

Depois de concluída a implementação, atualize o browser para ver as alterações.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

- Explore sample [Web Apps CLI scripts](app-service-cli-samples.md) (Explorar scripts de exemplo da CLI das Aplicações Web).
- Veja [Map a custom domain name](app-service-web-tutorial-custom-domain.md) (Mapear um nome de domínio personalizado), como contoso.com, para uma [aplicação do Serviço de Aplicações](app-service-web-tutorial-custom-domain.md).
