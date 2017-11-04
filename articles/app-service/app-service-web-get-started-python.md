---
title: "Criar uma aplicação Web em Python no Azure | Microsoft Docs"
description: "Implemente em minutos a sua primeira aplicação Python Hello World nas Aplicações Web do Serviço de Aplicações do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/26/2017
ms.author: cephalin;cfowler
ms.custom: mvc, devcenter
ms.openlocfilehash: ae410c7fabac6d23a69922804a0a87fde63594a2
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-python-web-app-in-azure"></a>Criar uma aplicação Web em Python no Azure

[As Aplicações Web do Azure](app-service-web-overview.md) fornecem um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este guia de início rápido orienta-o ao longo do desenvolvimento e da implementação de uma aplicação Python nas Aplicações Web do Azure. Crie a aplicação Web com a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) e utilize o Git para implementar o código Python de exemplo na aplicação Web.

![Aplicação Web de exemplo em execução no Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

Pode seguir os passos abaixo num computador Mac, Windows ou Linux. Depois de instalados os pré-requisitos, demora cerca de cinco minutos a concluir todos os passos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o Python](https://www.python.org/downloads/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Mude para o diretório que contém o código de exemplo.

```bash
cd python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Instale os pacotes necessários com `pip`.

```bash
pip install -r requirements.txt
```

Execute a aplicação localmente ao abrir uma janela do terminal e utilizar o `Python`comando para iniciar o servidor Web Python incorporado.

```bash
python main.py
```

Abra um browser e navegue para a aplicação de exemplo no `http://localhost:5000`.

Pode ver a mensagem **Hello Wordl** da aplicação de exemplo apresentada na página.

![Aplicação de exemplo em execução localmente](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

Na janela do terminal, prima **Ctrl+C** para sair do servidor Web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-python-no-h.md)]

Navegue até à sua aplicação web recentemente criada. Substitua  _&lt;nome da aplicação >_ com um nome de aplicação único.

```bash
http://<app name>.azurewebsites.net
```

![Página da aplicação Web vazia](media/app-service-web-get-started-python/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)]

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

O código Python de exemplo está em execução numa aplicação Web do Serviço de Aplicações do Azure.

![Aplicação Web de exemplo em execução no Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

**Parabéns!** Implementou a sua primeira aplicação Python no Serviço de Aplicações.

## <a name="update-and-redeploy-the-code"></a>Atualizar e voltar a implementar o código

Através de um editor de texto local, abra o ficheiro `main.py` na aplicação Python e faça uma pequena alteração ao texto junto à declaração `return`:

```python
return 'Hello, Azure!'
```

Na janela de terminal do local, consolidar as alterações no Git e, em seguida, emita as alterações de código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois de concluída a implementação, volte para a janela do browser aberta que abriu no passo [Navegar para a aplicação](#browse-to-the-app) e atualize a página.

![Aplicação de exemplo atualizada em execução no Azure](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gerir a sua nova aplicação Web do Azure

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerir a aplicação Web que criou.

No menu à esquerda, clique em **Serviços de Aplicações** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Painel Serviço de Aplicações no portal do Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para as Aplicações Web do Azure](app-service-web-tutorial-custom-domain.md)
