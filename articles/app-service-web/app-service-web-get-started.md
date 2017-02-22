---
title: "Implemente a sua primeira aplicação Web para o Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações ao implementar uma aplicação de exemplo. Comece a programar verdadeiramente em pouco tempo e a ver resultados imediatos."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 65c9bdd9-8763-4c56-8e15-f790992e951e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9ea90a7e2f69344fa86e20bc869f5b8af629a226
ms.openlocfilehash: 77de5a58dc792ff2d65855e726dd35d9824276cb


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>Implemente a sua primeira aplicação Web no Azure em cinco minutos
Este tutorial ajuda-o a implementar a sua primeira aplicação Web no [App Service do Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar o Serviço de Aplicações para criar aplicações, [back-ends de aplicações móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicações API](../app-service-api/app-service-api-apps-why-best-platform.md).

Irá: 

* Criar uma aplicação Web no Serviço de Aplicações do Azure.
* Implementar código de exemplo (escolha entre ASP.NET, PHP, Node.js, Java ou Python).
* Ver o seu código em execução em direto na produção.
* Atualizar a sua aplicação Web da mesma forma que [emitiria consolidações do Git](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](app-service-web-get-started-cli-nodejs.md) – CLI para os modelos de implementação de gestão clássica e de recursos
- [Azure CLI 2.0 (Pré-visualização)](app-service-web-get-started.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [Git](http://www.git-scm.com/downloads).
* [Pré-visualização da CLI 2.0 do Azure](/cli/azure/install-az-cli2).
* Uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pode [Experimentar o Serviço de Aplicações](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie uma aplicação de introdução e experimente-a durante uma hora, sem cartão de crédito nem compromissos.
> 
> 

## <a name="deploy-a-web-app"></a>Implementar uma aplicação Web
Eis como implementar uma aplicação Web no App Service do Azure.

1. Abra uma nova linha de comandos do Windows, uma janela do PowerShell, uma shell do Linux ou um terminal do OS X. Execute `git --version` e `az --version` para verificar se o Git e a CLI do Azure estão instalados no sua máquina.
   
    ![Testar a instalação das ferramentas da CLI para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/1-test-tools-2.0.png)
   
    Se não tiver instalado as ferramentas, consulte [Pré-requisitos](#Prerequisites) para obter as ligações para as transferências.

2. Inicie sessão no Azure desta forma:
   
        az login
   
    Siga a mensagem de ajuda para continuar o processo de início de sessão.
   
    ![Iniciar sessão no Azure para criar a sua primeira aplicação Web](./media/app-service-web-get-started/3-azure-login-2.0.png)

3. Defina o utilizador de implementação do Serviço de Aplicações. Irá implementar código com estas credenciais mais tarde.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este primeiro tutorial de Serviço de Aplicações, não é realmente necessário saber o que é.

        az group create --location "<location>" --name my-first-app-group

    Para ver os possíveis valores que pode utilizar para `<location>`, utilize o comando CLI `az appservice list-locations`.

3. Crie um novo [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". Para este primeiro tutorial do Serviço de Aplicações, basta fica a saber que não será cobrado para aplicações Web neste plano.

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Crie uma nova aplicação Web com um nome exclusivo em `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Em seguida, irá obter o código de exemplo que pretende implementar. Mude para um diretório de trabalho (`CD`) e clone a aplicação de exemplo desta forma:
   
        cd <working_directory>
        git clone <github_sample_url>
   
    Para *&lt;github_sample_url>*, utilize um dos seguintes URLs, consoante a arquitetura da sua preferência:
   
   * HTML + CSS + JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
   * ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
   * PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
   * Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
   * Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
   * Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

    ![Clonar o código de exemplo de aplicação para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/2-clone-sample.png)
   
5. Altere para o repositório da sua aplicação de exemplo. Por exemplo:
   
        cd app-service-web-html-get-started

5. Configure a implementação de Git local para a aplicação Web do Serviço de Aplicações com o seguinte comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    Obterá uma saída JSON como esta, que significa que o repositório de Git remoto está configurado:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adicionar o URL no JSON como um Git remoto para o repositório local (denominado `azure` para simplicidade).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implementar o código de exemplo para o Git remoto `azure`. Quando lhe for pedido, utilize as credenciais de implementação que configurou anteriormente.
   
        git push azure master
   
    ![Emitir código para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/5-push-code.png)
   
    Se utilizou uma das arquiteturas de linguagem, será apresentado um resultado diferente. O `git push` não só coloca código no Azure, como também aciona tarefas de implementação no motor de implementação. Se tiver algum ficheiro package.json (Node.js) ou requirements.txt (Python) na raiz (repositório) do projeto ou se tiver um ficheiro packages.config no seu projeto ASP.NET, o script de implementação restaura automaticamente os pacotes necessários. Também pode [ativar a extensão de Compositor](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente ficheiros composer.json na sua aplicação PHP.

Parabéns! Implementou a sua aplicação no App Service do Azure.

## <a name="see-your-app-running-live"></a>Ver a sua aplicação em execução

Para ver a aplicação em execução em direto no Azure, execute este comando:

    az appservice web browse --name <app_name> --resource-group my-first-app-group

## <a name="make-updates-to-your-app"></a>Efetuar atualizações à sua aplicação

Agora, pode utilizar o Git para emitir a partir da raiz do projeto (repositório) em qualquer altura para efetuar uma atualização ao site online. Pode fazê-lo tal como quando implementou o seu código pela primeira vez. Por exemplo, sempre que pretender emitir uma nova alteração que tenha testado localmente, basta executar os seguintes comandos a partir da raiz do projeto (repositório):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Passos seguintes

Localize os passos de desenvolvimento e de implementação preferenciais para a arquitetura da sua linguagem:

* [.NET](web-sites-dotnet-get-started.md)
* [PHP](app-service-web-php-get-started.md)
* [Node.js](app-service-web-nodejs-get-started.md)
* [python](web-sites-python-ptvs-django-mysql.md)
* [Java](web-sites-java-get-started.md)

Em alternativa, faça mais com a sua primeira aplicação Web. Por exemplo:

* Experimente [outras formas de implementar o seu código no Azure](web-sites-deploy.md). Por exemplo, para implementar a partir de um dos seus repositórios do GitHub, só tem de selecionar **GitHub** em vez de **Repositório de Git Local** nas **Opções de implementação**.
* Eleve a sua aplicação do Azure ao nível seguinte. Autentique os seus utilizadores. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques. Consulte [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md).




<!--HONumber=Feb17_HO1-->


