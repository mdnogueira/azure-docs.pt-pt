---
title: "Implementar a primeira aplicação Web PHP no Azure em cinco minutos | Microsoft Docs"
description: "Saiba como é fácil executar aplicações Web no Serviço de Aplicações mediante a implementação de uma aplicação PHP de exemplo. Comece a programar verdadeiramente em pouco tempo e a ver resultados imediatos."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 21acd587-b772-4d89-be06-9a7429c33c7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 27b5ead5a60b05f84974391954f1f087da63c2e9


---
# <a name="deploy-your-first-php-web-app-to-azure-in-five-minutes"></a>Implementar a primeira aplicação Web PHP no Azure em cinco minutos

> [!div class="op_single_selector"]
> * [Primeiro site HTML](app-service-web-get-started-html-cli-nodejs.md)
> * [Primeira aplicação .NET](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [Primeira aplicação PHP](app-service-web-get-started-php-cli-nodejs.md)
> * [Primeira aplicação Node.js](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [Primeira aplicação Python](app-service-web-get-started-python-cli-nodejs.md)
> * [Primeira aplicação Java](app-service-web-get-started-java.md)
> 
> 

Este tutorial ajuda-o a implementar a sua primeira aplicação Web PHP no [Serviço de Aplicações do Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar o Serviço de Aplicações para criar aplicações, [back-ends de aplicações móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicações API](../app-service-api/app-service-api-apps-why-best-platform.md).

Irá: 

* Criar uma aplicação Web no Serviço de Aplicações do Azure.
* Implementar código PHP de exemplo.
* Ver o seu código em execução em direto na produção.
* Atualizar a sua aplicação Web da mesma forma que [emitiria consolidações do Git](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](app-service-web-get-started-php-cli-nodejs.md) – CLI para os modelos de implementação de gestão clássica e de recursos
- [Azure CLI 2.0 (Pré-visualização)](app-service-web-get-started-php.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [Git](http://www.git-scm.com/downloads).
* [CLI do Azure](../xplat-cli-install.md).
* Uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Pode [Experimentar o Serviço de Aplicações](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie uma aplicação de introdução e experimente-a durante uma hora, sem cartão de crédito nem compromissos.
> 
> 

## <a name="deploy-a-php-web-app"></a>Implementar uma aplicação Web em PHP
1. Abra uma nova linha de comandos do Windows, uma janela do PowerShell, uma shell do Linux ou um terminal do OS X. Execute `git --version` e `azure --version` para verificar se o Git e a CLI do Azure estão instalados no sua máquina.
   
    ![Testar a instalação das ferramentas da CLI para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/1-test-tools.png)
   
    Se não tiver instalado as ferramentas, consulte [Pré-requisitos](#Prerequisites) para obter as ligações para as transferências.
2. Inicie sessão no Azure desta forma:
   
        azure login
   
    Siga a mensagem de ajuda para continuar o processo de início de sessão.
   
    ![Iniciar sessão no Azure para criar a sua primeira aplicação Web](./media/app-service-web-get-started/3-azure-login.png)
3. Altere a CLI do Azure para o modo ASM e defina o utilizador da implementação do Serviço de Aplicações. Irá implementar código com as credenciais mais tarde.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>
4. Mude para um diretório de trabalho (`CD`) e clone a aplicação PHP de exemplo desta forma:
   
        git clone https://github.com/Azure-Samples/app-service-web-php-get-started.git
5. Altere para o repositório da sua aplicação de exemplo. Por exemplo:
   
        cd app-service-web-php-get-started
6. Crie o recurso de aplicação Serviço de Aplicações no Azure, com um nome de aplicação exclusivo e o utilizador da implementação configurado anteriormente. Quando lhe for pedido, especifique o número da região pretendida.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Criar o recurso do Azure para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started-languages/php-site-create.png)
   
    Agora, a sua aplicação está criada no Azure. Além disso, o seu diretório atual é inicializado pelo Git e ligado à nova aplicação do App Service como um Git remoto.
    Pode navegar para o URL da aplicação (http://&lt;app_name>.azurewebsites.net) e ver a fantástica página HTML predefinida, mas o mais importante agora é introduzir o seu código.
7. Implemente o código de exemplo na sua aplicação do Azure, tal como emitiria qualquer código com o Git. Quando lhe for pedido, utilize a palavra-passe que configurou anteriormente.
   
        git push azure master
   
    ![Emitir código para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started-languages/php-git-push.png)
   
    O `git push` não só coloca código no Azure, como também aciona tarefas de implementação no motor de implementação. Também pode  [ativar a extensão de Compositor](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente ficheiros composer.json na sua aplicação PHP.

Parabéns! Implementou a sua aplicação no App Service do Azure.

## <a name="see-your-app-running-live"></a>Ver a sua aplicação em execução
Para ver a sua aplicação em execução no Azure, execute este comando a partir de qualquer diretório no seu repositório:

    azure site browse

## <a name="make-updates-to-your-app"></a>Efetuar atualizações à sua aplicação
Agora, pode utilizar o Git para emitir a partir da raiz do projeto (repositório) em qualquer altura para efetuar uma atualização ao site online. Pode fazê-lo tal como quando implementou o seu código pela primeira vez. Por exemplo, sempre que pretender emitir uma nova alteração que tenha testado localmente, basta executar os seguintes comandos a partir da raiz do projeto (repositório):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Passos seguintes
[Crie, configure e implemente uma aplicação Web Laravel no Azure](app-service-web-php-get-started-cli-nodejs.md). Este tutorial irá permitir-lhe obter as competências básicas de que necessita para executar qualquer aplicação Web PHP no Azure, tais como:

* Criar e configurar aplicações no Azure a partir do PowerShell/Bash.
* Definir a versão do PHP.
* Utilizar um ficheiro de arranque que não se encontra no diretório de aplicações de raiz.
* Ativar a automatização do Compositor.
* Aceder a variáveis específicas do ambiente.
* Resolver erros comuns.

Em alternativa, faça mais com a sua primeira aplicação Web. Por exemplo:

* Experimente [outras formas de implementar o seu código no Azure](web-sites-deploy.md). Por exemplo, para implementar a partir de um dos seus repositórios do GitHub, só tem de selecionar **GitHub** em vez de **Repositório de Git Local** nas **Opções de implementação**.
* Eleve a sua aplicação do Azure ao nível seguinte. Autentique os seus utilizadores. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques. Consulte [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md).




<!--HONumber=Feb17_HO2-->


