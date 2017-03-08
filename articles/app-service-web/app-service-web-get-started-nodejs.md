---
title: "Implementar a sua aplicação Web Node.js no Azure em cinco minutos (CLI 2.0 [Pré-visualização]) | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações mediante a implementação de uma aplicação Node.js de exemplo. Comece a programar verdadeiramente em pouco tempo e a ver resultados imediatos."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 0d5015182ba6e63528b03b20450e00c88e436be8
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-your-first-nodejs-web-app-to-azure-in-five-minutes-cli-20-preview"></a>Implementar a primeira aplicação Web Node.js no Azure em cinco minutos (CLI 2.0 [Pré-visualização)]
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este tutorial ajuda-o a implementar a sua primeira aplicação Web Node.js no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar o Serviço de Aplicações para criar aplicações, [back-ends de aplicações móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicações API](../app-service-api/app-service-api-apps-why-best-platform.md).

Irá: 

* Criar uma aplicação Web no Serviço de Aplicações do Azure.
* Implementar código Node.js de exemplo.
* Ver o seu código em execução em direto na produção.
* Atualizar a sua aplicação Web da mesma forma que [emitiria consolidações do Git](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](app-service-web-get-started-nodejs-cli-nodejs.md) – CLI para os modelos de implementação de gestão clássica e de recursos
- [CLI 2.0 do Azure](app-service-web-get-started-nodejs.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [Git](http://www.git-scm.com/downloads).
* [Pré-visualização da CLI 2.0 do Azure](/cli/azure/install-az-cli2).
* Uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pode [Experimentar o Serviço de Aplicações](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie uma aplicação de introdução e experimente-a durante uma hora, sem cartão de crédito nem compromissos.
> 
> 

## <a name="deploy-a-nodejs-web-app"></a>Implementar uma aplicação Web em Node.js
1. Abra uma nova linha de comandos do Windows, uma janela do PowerShell, uma shell do Linux ou um terminal do OS X. Execute `git --version` e `azure --version` para verificar se o Git e a CLI do Azure estão instalados no sua máquina.
   
    ![Testar a instalação das ferramentas da CLI para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    Se não tiver instalado as ferramentas, consulte [Pré-requisitos](#Prerequisites) para obter as ligações para as transferências.
2. Inicie sessão no Azure desta forma:
   
        az login
   
    Siga a mensagem de ajuda para continuar o processo de início de sessão.
   
    ![Iniciar sessão no Azure para criar a sua primeira aplicação Web](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. Defina o utilizador de implementação do Serviço de Aplicações. Irá implementar código com estas credenciais mais tarde.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este primeiro tutorial de Serviço de Aplicações, não é realmente necessário saber o que é.

        az group create --location "<location>" --name my-first-app-group

    Para ver os possíveis valores que pode utilizar para `<location>`, utilize o comando CLI `az appservice list-locations`.

3. Crie um novo [plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". Para este primeiro tutorial do Serviço de Aplicações, basta fica a saber que não será cobrado para aplicações Web neste plano.

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Crie uma nova aplicação Web com um nome exclusivo em `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Em seguida, obtém o código Node.js de exemplo que pretende implementar. Mude para um diretório de trabalho (`CD`) e clone a aplicação de exemplo desta forma:
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git

5. Altere para o repositório da sua aplicação de exemplo.
   
        cd app-service-web-nodejs-get-started
5. Configure a implementação de Git local para a aplicação Web do Serviço de Aplicações com o seguinte comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    Obterá uma saída JSON como esta, que significa que o repositório de Git remoto está configurado:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adicionar o URL no JSON como um Git remoto para o repositório local (denominado `azure` para simplicidade).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implemente o código de exemplo na sua aplicação do Azure, tal como emitiria qualquer código com o Git. Quando lhe for pedido, utilize a palavra-passe que configurou anteriormente.
   
        git push azure master
   
    ![Emitir código para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started-languages/node-git-push.png)
   
    O `git push` não só coloca código no Azure, como também aciona tarefas de implementação no motor de implementação. 
    Se tiver um package.json na raiz do projeto (repositório), o script de implementação restaura os pacotes necessários por si. 

Parabéns! Implementou a sua aplicação no App Service do Azure.

## <a name="see-your-app-running-live"></a>Ver a sua aplicação em execução
Para ver a sua aplicação em execução no Azure, execute este comando a partir de qualquer diretório no seu repositório:

    az appservice web browse -n <app-name> --resource-group my-first-app-group

## <a name="make-updates-to-your-app"></a>Efetuar atualizações à sua aplicação
Agora, pode utilizar o Git para emitir a partir da raiz do projeto (repositório) em qualquer altura para efetuar uma atualização ao site online. Pode fazê-lo tal como quando implementou o seu código pela primeira vez. Por exemplo, sempre que pretender emitir uma nova alteração que tenha testado localmente, basta executar os seguintes comandos a partir da raiz do projeto (repositório):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Passos seguintes
[Crie, configure e implemente uma aplicação Web Express Node.js no Azure](app-service-web-nodejs-get-started.md). Este tutorial irá permitir-lhe obter as competências básicas de que necessita para executar qualquer aplicação Web Node.js no Azure, tais como:

* Criar e configurar aplicações no Azure a partir do PowerShell/Bash.
* Definir a versão do Node.js.
* Utilizar um ficheiro de arranque que não se encontra no diretório de aplicações de raiz.
* Automatizar com NPM.
* Obter registos de erro e de resultados.

Em alternativa, faça mais com a sua primeira aplicação Web. Por exemplo:

* Experimente [outras formas de implementar o seu código no Azure](web-sites-deploy.md). Por exemplo, para implementar a partir de um dos seus repositórios do GitHub, só tem de selecionar **GitHub** em vez de **Repositório de Git Local** nas **Opções de implementação**.
* Eleve a sua aplicação do Azure ao nível seguinte. Autentique os seus utilizadores. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques. Consulte [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md).


