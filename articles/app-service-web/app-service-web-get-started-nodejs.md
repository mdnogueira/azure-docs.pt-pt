---
title: "Criar uma aplicação Node.js nas Aplicações Web | Microsoft Docs"
description: "Implemente em minutos a sua primeira aplicação Node.js Hello World nas Aplicações Web do Serviço de Aplicações."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/28/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: c32cb52e4bb7bacde20e21820f277b4e86877e74
ms.lasthandoff: 04/25/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>Criar uma aplicação Node.js nas Aplicações Web

Este tutorial de início rápido orienta-o ao longo do desenvolvimento e da implementação de uma aplicação Node.js no Azure. Vamos executar a aplicação com um Serviço de Aplicações do Azure baseado no Linux e criar e configurar uma Aplicação Web nova no mesmo ao utilizar a CLI do Azure. Em seguida, vamos utilizar o git para implementar a nossa aplicação Node.js no Azure.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Pode seguir os passos abaixo num computador Mac, Windows ou Linux. Deverá demorar apenas cinco minutos a concluí-los.

## <a name="before-you-begin"></a>Antes de começar

Antes de executar este exemplo, instale localmente os seguintes pré-requisitos:

1. [Transferir e instalar o git](https://git-scm.com/)
1. [Transferir e instalar Node.js e NPM](https://nodejs.org/)
1. Transferir e instalar a [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Clone o repositório da aplicação de exemplo Hello World para o seu computador local.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

> [!TIP]
> Em alternativa, pode [transferir o exemplo](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) como um ficheiro zip e extraí-lo.

Mude para o diretório que contém o código de exemplo.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente ao abrir uma janela de terminal e utilizar o script `npm start` para que o exemplo inicie o servidor http Node.js incorporado.

```bash
npm start
```

Abra um browser e navegue para o exemplo.

```bash
http://localhost:1337
```

Pode ver a mensagem **Hello Wordl** da aplicação de exemplo apresentada na página.

![localhost-hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Na janela do terminal, prima **Ctrl+C** para sair do servidor Web.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Vamos agora utilizar a CLI 2.0 do Azure numa janela do terminal para criar os recursos necessários para alojar a aplicação Node.js no Azure. Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Configurar um Utilizador de Implementação

Para FTP e Git local, é necessário ter configurado um utilizador de implementação no servidor, para autenticar a implementação. A criação do utilizador de implementação é uma configuração única; tome nota do nome de utilizador e da palavra-passe, pois vão ser utilizados num passo abaixo.

> [!NOTE]
> Para implementação de FTP e Git Local numa Aplicação Web, é necessário um utilizador de implementação.
> `username` e `password` são ao nível da conta, pelo que, como tal, são diferentes das credenciais da sua subscrição do Azure. Estas credenciais só têm de ser criadas uma vez.
>

Utilize o comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para criar as suas credenciais ao nível da conta.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure, como aplicações Web, bases de dados e contas de armazenamento, são implementados e geridos.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Criar um Serviço de Aplicações do Azure

Crie um Plano do Serviço de Aplicações baseado no Linux com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

> [!NOTE]
> Os planos do Serviço de Aplicações representam a coleção de recursos físicos utilizados para alojar as suas aplicações. Todas as aplicações atribuídas a um plano do Serviço de Aplicações partilham os recursos definidos pelo mesmo, o que lhe permite economizar quando alojar várias aplicações.
>
> Os planos do Serviço de Aplicações definem:
> * Região (Europa do Norte, E.U.A. Leste, Sudeste Asiático)
> * Tamanho da Instância (Pequena, Média, Grande)
> * Contagem de Escalas (uma, duas, três instâncias, etc.)
> * SKU (Gratuito, Partilhado, Básico, Standard, Premium)
>

O exemplo seguinte cria um Plano do Serviço de Aplicações em funções de trabalho do Linux com o nome `quickStartPlan`, que utiliza o escalão de preços **Standard**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o Plano do Serviço de Aplicações tiver sido criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
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

Agora que já foi criado um Plano do Serviço de Aplicações, crie uma Aplicação Web dentro do Plano do Serviço de Aplicações `quickStartPlan`. A aplicação Web dá-nos espaço de alojamento para implementar o nosso código, bem como um URL que nos permite ver a aplicação implementada. Utilize o comando [az appservice web create](/cli/azure/appservice/web#create) para criar a Aplicação Web.

No comando abaixo, substitua o nome exclusivo da sua aplicação, onde vir o marcador de posição `<app_name>`. O `<app_name>` vai ser utilizado como o site de DNS predefinido para a aplicação Web, daí que o nome tenha de ser exclusivo relativamente a todas as aplicações no Azure. Posteriormente, pode mapear qualquer entrada DNS personalizada para a aplicação Web antes de a expor aos utilizadores.

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

![app-service-web-service-created](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

Está agora criada uma Aplicação Web vazia nova no Azure. Vamos então configurar a Aplicação Web para utilizar Node.js e implementá-la no mesmo.

## <a name="configure-to-use-nodejs"></a>Configurar para utilizar Node.js

Utilize o comando [az appservice web config update](/cli/azure/app-service/web/config#update) para configurar a Aplicação Web para utilizar a versão `6.9.3` de Node.js.

> [!TIP]
> A definição da versão de Node.js desta forma utiliza um contentor predefinido disponibilizado pela plataforma. Se quiser utilizar o seu próprio contentor, veja a referência da CLI relativa ao comando [az appservice web config container update](/cli/azure/appservice/web/config/container#update).

```azurecli
az appservice web config update --linux-fx-version "NODE|6.9.3" --startup-file process.json --name <app_name> --resource-group myResourceGroup
```

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

Envie para o Azure remoto para implementar a sua aplicação. Vai ser-lhe pedida a palavra-passe que indicou anteriormente, como parte da criação do utilizador de implementação.

```azurecli
git push azure master
```

Durante a implementação, o Serviço de Aplicações do Azure vai comunicar o progresso face ao Git.

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

Desta vez, a página que apresenta a mensagem Hello World é executada com o código de Node.js em execução como uma aplicação Web do Serviço de Aplicações do Azure.

## <a name="updating-and-deploying-the-code"></a>Atualizar e Implementar o Código

Utilizando um editor de texto local, abra o ficheiro `index.js` na aplicação Node.js e faça uma pequena alteração ao texto na chamada para `response.end`:

```nodejs
response.end("Hello Azure!");
```

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois de concluída a implementação, volte para a janela do browser aberta no passo “Navegar para a aplicação” e clique em “atualizar”.

![hello-world-in-browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gerir a sua nova aplicação Web do Azure

Aceda ao portal do Azure para ver a aplicação Web que acabou de criar.

Para tal, inicie sessão em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviços Aplicacionais** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Chegou ao _painel_ da sua aplicação Web (uma página do portal que se abre na horizontal).

Por predefinição, o painel da sua aplicação Web mostra a página **Descrição Geral**. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo do painel mostram as várias páginas de configuração que pode abrir.

![Painel Serviço de Aplicações no portal do Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Estes separadores no painel mostram as muitas funcionalidades excelentes que pode adicionar à sua aplicação Web. A lista seguinte dá-lhe apenas algumas das possibilidades:

* Mapear um nome DNS personalizado
* Vincular um certificado SSL personalizado
* Configurar a implementação contínua
* Aumentar e reduzir verticalmente
* Adicionar a autenticação do utilizador

**Parabéns!** Implementou a sua primeira aplicação Node.js no Serviço de Aplicações.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os [scripts da CLI das Aplicações Web](app-service-cli-samples.md) criados previamente.

