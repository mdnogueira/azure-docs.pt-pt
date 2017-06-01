---
title: "Criar uma aplicação PHP nas Aplicações Web do Azure | Microsoft Docs"
description: "Implemente em minutos a sua primeira aplicação PHP Hello World nas Aplicações Web do Serviço de Aplicações."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/04/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0541778e07193c4903a90ce0b91db224bdf60342
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-php-application-on-web-app"></a>Criar uma aplicação PHP nas Aplicações Web

Este tutorial de início rápido orienta-o ao longo do desenvolvimento e da implementação de uma aplicação PHP no Azure. Vamos executar a aplicação com um [plano do Serviço de Aplicações do Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) e criar e configurar uma Aplicação Web nova no mesmo com a CLI do Azure. Em seguida, vamos utilizar o git para implementar a nossa aplicação PHP no Azure.

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

Pode seguir os passos abaixo num computador Mac, Windows ou Linux. Deverá demorar apenas cinco minutos a concluí-los.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar este exemplo, transfira e instale o seguinte:

* [Git](https://git-scm.com/)
* [PHP](https://php.net)
* [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Clone o repositório da aplicação de exemplo Hello World para o seu computador local.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
```

Mude para o diretório que contém o código de exemplo.

```bash
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente ao abrir uma janela de terminal com uma linha de comandos `php`, para que o exemplo inicie o servidor Web PHP incorporado.

```bash
php -S localhost:8080
```

Abra um browser e navegue para o exemplo.

```bash
http://localhost:8080
```

Pode ver a mensagem **Hello Wordl** da aplicação de exemplo apresentada na página.

![localhost-hello-world-in-browser](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

Na janela do terminal, prima **Ctrl+C** para sair do servidor Web.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Vamos agora utilizar a CLI 2.0 do Azure numa janela do terminal para criar os recursos necessários para alojar a aplicação PHP no Azure. Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

<!-- ## Configure a Deployment User -->
[!INCLUDE [login-to-azure](../../includes/configure-deployment-user.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento, são implementados e geridos.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

Crie um [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) “GRATUITO” com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

<!--
 An App Service plan represents the collection of physical resources used to ..
-->
[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `quickStartPlan`, que utiliza o escalão de preços **Gratuito**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicações tiver sido criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Agora que já foi criado um plano do Serviço de Aplicações, crie uma [Aplicação Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) dentro do plano do Serviço de Aplicações `quickStartPlan`. A aplicação Web dá-nos espaço de alojamento para implementar o nosso código, bem como um URL que nos permite ver a aplicação implementada. Utilize o comando [az appservice web create](/cli/azure/appservice/web#create) para criar a Aplicação Web.

No comando abaixo, substitua o nome exclusivo da sua aplicação, onde vir o marcador de posição `<app_name>`. `<app_name>` é utilizado no site DNS predefinido da aplicação Web. Se `<app_name>` não for exclusivo, obtém a mensagem de erro amigável “O Website com o nome <nome_da_aplicação> já existe".

<!-- removed per https://github.com/Microsoft/azure-docs-pr/issues/11878
You can later map any custom DNS entry to the web app before you expose it to your users.
-->

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Quando a Aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

Navegue para o site para ver a sua Aplicação Web criada recentemente.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-php/app-service-web-service-created.png)

Está agora criada uma Aplicação Web vazia nova no Azure.

## <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

Existem várias formas de implementar na sua Aplicação Web, incluindo FTP, Git local, bem como GitHub, Visual Studio Team Services e Bitbucket.

Utilize o comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar o acesso ao git local para a Aplicação Web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Copie a saída do terminal, uma vez que vai ser utilizada no próximo passo.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

Adicione um Azure remoto para o seu repositório do Git local.

```bash
git remote add azure <paste-previous-command-output-here>
```

Envie para o Azure remoto para implementar a sua aplicação. É-lhe pedida a palavra-passe que indicou quando criou o utilizador de implementação, anteriormente. Confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```

Durante a implementação, o Serviço de Aplicações do Azure vai comunicar o progresso ao Git.

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

Desta vez, a página que apresenta a mensagem Hello World é executada com o código de PHP em execução como uma aplicação Web do Serviço de Aplicações do Azure.



## <a name="updating-and-deploying-the-code"></a>Atualizar e Implementar o Código

Utilizando um editor de texto local, abra o ficheiro `index.php` na aplicação PHP e faça uma pequena alteração ao texto na cadeia junto a `echo`:

```php
echo "Hello Azure!";
```

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois de concluída a implementação, volte para a janela do browser aberta no passo **Navegar para a aplicação** e clique em “atualizar”.

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gerir a sua nova aplicação Web do Azure

Aceda ao portal do Azure para ver a aplicação Web que acabou de criar.

Para tal, inicie sessão em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviços Aplicacionais** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

Chegou ao _painel_ da sua aplicação Web (uma página do portal que se abre na horizontal).

Por predefinição, o painel da sua aplicação Web mostra a página **Descrição Geral**. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo do painel mostram as várias páginas de configuração que pode abrir.

![Painel Serviço de Aplicações no portal do Azure](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

Estes separadores no painel mostram as muitas funcionalidades excelentes que pode adicionar à sua aplicação Web. A lista seguinte dá-lhe apenas algumas das possibilidades:

* Mapear um nome DNS personalizado
* Vincular um certificado SSL personalizado
* Configurar a implementação contínua
* Aumentar e reduzir verticalmente
* Adicionar a autenticação do utilizador

**Parabéns!** Implementou a sua primeira aplicação PHP no Serviço de Aplicações.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

> [!div class="nextstepaction"]
> [Explore sample Web Apps CLI scripts](app-service-cli-samples.md) (Explorar scripts de exemplo da CLI das Aplicações Web)


