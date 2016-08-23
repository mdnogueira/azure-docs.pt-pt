<properties
    pageTitle="Criar uma aplicação Web Node.js no App Service do Azure | Microsoft Azure"
    description="Saiba como implementar uma aplicação Node.js numa aplicação Web no App Service do Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="06/24/2016"
    ms.author="robmcm"/>

# Criar uma aplicação Web Node.js no App Service do Azure

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Este tutorial mostra como criar uma simples aplicação [Node.js](http://nodejs.org) e implementá-la numa [aplicação Web](app-service-web-overview.md) no [App Service do Azure](../app-service/app-service-value-prop-what-is.md) utilizando o [Git](http://git-scm.com). As instruções deste tutorial podem ser seguidas em qualquer sistema operativo capaz de executar Node.js.

Irá aprender:

* Como criar uma aplicação Web no App Service do Azure utilizando o Portal do Azure.
* Como implementar uma aplicação Node.js na aplicação Web ao enviar para o repositório de Git da aplicação Web.

A aplicação concluída escreve uma cadeia curta "olá, mundo" para o browser.

![Um browser a apresentar a mensagem “Olá, Mundo”.][helloworld-completed]

Para tutoriais e código de exemplo com aplicações Node.js mais complexas ou para outros tópicos sobre como utilizar o Node.js no Azure, consulte o [Centro de programadores do Node.js](/develop/nodejs/).

> [AZURE.NOTE]
> Para concluir este tutorial, necessita de uma conta do Microsoft Azure. Se não tiver uma conta, pode [ativar os benefícios de subscritor do Visual Studio](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [inscrever-se numa avaliação gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
>
> Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Aqui, pode criar imediatamente uma aplicação Web de arranque de curta duração no App Service — sem cartões de crédito e sem compromissos.

## Criar uma aplicação Web e ativar a publicação de Git

Siga estes passos para criar uma aplicação Web no App Service do Azure e ativar a publicação de Git. 

O [Git](http://git-scm.com/) é um sistema de controlo de versão distribuída que pode utilizar para implementar o seu Web site do Azure. Irá armazenar o código de escrita para a sua aplicação Web num repositório de Git local e implementar o código para o Azure ao enviar para um repositório remoto. Este método de implementação é uma funcionalidade das Web Apps do App Service.  

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Clique no ícone **+ NOVO** na parte superior esquerda do Portal do Azure.

3. Clique em **Web + Móvel** e, em seguida, clique em **Aplicação Web**.

    ![][portal-quick-create]

4. Introduza um nome para a aplicação Web na caixa **Aplicação Web**.

    Este nome tem de ser exclusivo no domínio azurewebsites.net porque o URL da aplicação Web será {name}.azurewebsites.net. Se o nome introduzido não for exclusivo, é apresentado um ponto de exclamação vermelho na caixa de texto.

5. Selecione uma **Subscrição**.

6. Selecione um **Grupo de Recursos** ou crie um novo.

    Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../resource-group-overview.md).

7. Selecione um **Plano do App Service/Localização** ou crie um novo.

    Para obter mais informações sobre os planos do App Service, consulte [Descrição geral dos planos do App Service do Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

8. Clique em **Criar**.
   
    ![][portal-quick-create2]

    Num curto período de tempo, normalmente, menos de um minuto, o Azure acaba de criar a nova aplicação Web.

9. Clique em **Web Apps > {a sua nova aplicação Web}**.

    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)

10. No painel **Aplicação Web**, clique na parte **Implementação**.

    ![][deployment-part]

11. No painel **Implementação Contínua**, clique em **Escolher Origem**

12. Clique em **Repositório de Git local** e, em seguida, clique em **OK**.

    ![][setup-git-publishing]

13. Configure as credenciais de implementação se ainda não o tiver feito.

    a. No painel Aplicação Web, clique em **Definições > Credenciais de implementação**.

    ![][deployment-credentials]
 
    b. Crie um nome de utilizador e uma palavra-passe. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)

14. No painel Aplicação Web, clique em **Definições** e, em seguida, clique em **Propriedades**.
 
    Para publicar, irá enviar para um repositório de Git remoto. O URL para o repositório está listado em **URL do GIT**. Irá utilizar este URL mais tarde no tutorial.

    ![][git-url]

## Criar e testar a aplicação localmente

Nesta secção, irá criar um ficheiro **server.js** que contém uma versão ligeiramente modificada do exemplo de "Olá, mundo" de [nodejs.org]. O código adiciona process.env.PORT como a porta a escutar ao executar numa aplicação Web do Azure.

1. Criar um diretório com o nome *olámundo*.

2. Utilize um editor de texto para criar um novo ficheiro com o nome **server.js** no diretório *olámundo*.

2. Copie o seguinte código para o ficheiro **server.js** e, em seguida, guarde o ficheiro:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abra a linha de comandos e utilize o seguinte comando para iniciar a aplicação Web localmente.

        node server.js

4. Abra o browser e navegue até http://localhost:1337. 

    É apresentada uma página Web que apresenta "Olá, mundo", conforme mostrado na seguinte captura de ecrã.

    ![Um browser a apresentar a mensagem “Olá, Mundo”.][helloworld-localhost]

## Publicar a aplicação

1. Instale o Git se ainda não o tiver feito.

    Para obter instruções de instalação para a sua plataforma, consulte a [página de transferência do Git](http://git-scm.com/download).

1. Na linha de comandos, altere os diretórios para o diretório **olámundo** e introduza o seguinte comando para inicializar um repositório de Git local.

        git init


2. Utilize os seguintes comandos para adicionar ficheiros ao repositório:

        git add .
        git commit -m "initial commit"

3. Adicione um Git remoto para enviar atualizações para a aplicação Web que criou anteriormente, utilizando o seguinte comando:

        git remote add azure [URL for remote repository]

4. Envie as suas alterações para o Azure, utilizando o seguinte comando:

        git push azure master

    É-lhe pedida a palavra-passe que criou anteriormente. O resultado será semelhante ao seguinte exemplo.

        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master

5. Para ver a sua aplicação, clique no botão **Procurar** na parte da **Aplicação Web** no Portal do Azure.

    ![Botão Procurar](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)

    ![Olá, mundo no Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## Publicar as alterações à aplicação

1. Abra o ficheiro **server.js** num editor de texto e altere “Olá, Mundo\n” para “Olá, Azure\n”. 

2. Guarde o ficheiro.

2. Na linha de comandos, altere os diretórios para o diretório **olámundo** e execute os seguintes comandos:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    É-lhe pedida novamente a sua palavra-passe.

3. Atualize a janela do browser na qual navegou para o URL da aplicação Web.

    ![Uma página Web a apresentar “Olá, Azure”][helloworld-completed]

## Reverter uma implementação

A partir do painel **Aplicação Web** pode clicar em **Definições > Implementação Contínua** para ver o histórico de implementação no painel **Implementações**. Se precisar de reverter para uma implementação anterior, pode selecioná-la e, em seguida, clicar em **Reimplementar** no painel **Detalhes da Implementação**.

## Passos seguintes

Implementou uma aplicação Node.js numa aplicação Web no App Service do Azure. Para saber mais sobre como as Web Apps do App Service executam aplicações Node.js, consulte o artigo [Web Apps do App Service do Azure: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) e [Especificar uma versão do Node.js numa aplicação Azure](../nodejs-specify-node-version-azure-apps.md).

O Node.js fornece um ecossistema rico de módulos que podem ser utilizados pelas suas aplicações. Para saber como as Web Apps funcionam com módulos, consulte o artigo [Utilizar módulos do Node.js com aplicações Azure](../nodejs-use-node-modules-azure-apps.md).

Se tiver problemas com a sua aplicação após ter sido implementada no Azure, consulte o artigo [Como depurar uma aplicação Node.js no App Service do Azure](web-sites-nodejs-debug.md) para obter informações sobre como diagnosticar o problema.

Este artigo utiliza o Portal do Azure para criar uma aplicação Web. Também pode utilizar a [Interface de Linha de Comandos do Azure](../xplat-cli-install.md) ou o [Azure PowerShell](../powershell-install-configure.md) para efetuar as mesmas operações.

Para obter mais informações sobre como desenvolver aplicações Node.js no Azure, consulte o [Centro de Programadores do Node.js](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png



<!--HONumber=Aug16_HO1-->


