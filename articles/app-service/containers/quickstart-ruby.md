---
title: "Criar uma aplicação Ruby e implementar no App Service no Linux | Microsoft Docs"
description: "Saiba como criar aplicações Ruby com o serviço de aplicações no Linux."
keywords: "serviço de aplicações do Azure, linux, oss, ruby"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: cfowler
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 55ff4dc168ca6f8b2bdbb7c5743515691e8ac92d
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>Criar uma aplicação Ruby no serviço de aplicações no Linux

[Serviço de aplicações no Linux](app-service-linux-intro.md) fornece uma serviço de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este guia de introdução mostra como criar um Ruby básica na aplicação Rails, em seguida, implementá-lo para o Azure como uma aplicação Web no Linux.

![Olá mundo](./media/quickstart-ruby/hello-world-updated.png)

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4.1 ou superior](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller).
* [Git](https://git-scm.com/downloads).
* Um [subscrição do Azure Active Directory](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Na janela de terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o computador local:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

Execute o servidor de rails por ordem para a aplicação funcione. Mude para o *Olá mundo* diretório e o `rails server` comando inicia o servidor.

```bash
cd hello-world\bin
rails server
```

Utilizando o seu browser, navegue para `http://localhost:3000` para testar a aplicação localmente.

![Olá mundo](./media/quickstart-ruby/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Modificar a aplicação para apresentar a mensagem de boas-vindas

Modificar a aplicação, pelo que é apresentada uma mensagem de boas-vindas. Em primeiro lugar, tem de configurar uma rota modificando o *~/workspace/ruby-docs-hello-world/config/routes.rb* ficheiro para incluir uma rota chamada `hello`.

  ```ruby
  Rails.application.routes.draw do
      #For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
      root 'application#hello'
  end
  ```

Altere o controlador da aplicação, de modo que devolva a mensagem como HTML no browser. 

Abra *~/workspace/hello-world/app/controllers/application_controller.rb* para edição. Modificar o `ApplicationController` classe serve como o exemplo de código seguinte:

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

A aplicação está agora configurada. Utilizando o seu browser, navegue para `http://localhost:3000` para confirmar a página de destino de raiz.

![Olá, mundo configurado](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Criar uma aplicação Ruby web no Azure

É necessário um grupo de recursos que contém os recursos necessários para a sua aplicação web. Para criar um grupo de recursos, utilize o [criar grupo az]() comando.

```azurecli-interactive
az group create --location westeurope --name myResourceGroup
```

Utilize o [criar plano de serviço aplicacional az](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) comando para criar um plano do app service para a sua aplicação web.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Em seguida, emita o [az webapp criar](https://docs.microsoft.com/cli/azure/webapp) comando para criar a aplicação web que utiliza o plano de serviço criado recentemente. Tenha em atenção que o tempo de execução está definido como `ruby|2.3`. Não se esqueça de substituir `<app name>` com um nome de aplicação único.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> \
--runtime "ruby|2.3" --deployment-local-git
```

O resultado do comando revela informações sobre a aplicação web recentemente criada, bem como o URL de implementação. Deve ser semelhante ao seguinte exemplo. Copie o URL para utilização posterior neste tutorial.

```bash
https://<deployment user name>@<app name>.scm.azurewebsites.net/<app name>.git
```

Assim que a aplicação web for criada, um **descrição geral** página está disponível para ver. Navegue para a mesma. É apresentada a página inicial que se segue:

![Página inicial](./media/quickstart-ruby/splash-page.png)


## <a name="deploy-your-application"></a>Implementar a sua aplicação

Execute os seguintes comandos para implementar a aplicação local ao seu Web site do Azure:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Certifique-se de que as operações de implementação remota reportam sucesso. Os comandos produzem o resultado semelhante para o seguinte texto:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Depois de concluída a implementação, reinicie a aplicação web para a implementação tenha efeito, utilizando o [reinício de webapp az](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) comando, conforme mostrado aqui:

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Navegue para o site e verificar os resultados.

```bash
http://<app name>.azurewebsites.net
```

![aplicação web atualizadas](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Enquanto a aplicação está a ser reiniciado, a tentar procurar os resultados de site num código de estado HTTP `Error 503 Server unavailable`. Pode demorar alguns minutos para reiniciar completamente.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

[App Service do Azure no Linux FAQ](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
