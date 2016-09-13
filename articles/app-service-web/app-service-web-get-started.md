<properties
    pageTitle="Implemente a sua primeira aplicação Web para o Azure em cinco minutos | Microsoft Azure"
    description="Saiba como é fácil executar Web Apps no App Service ao implementar uma aplicação de exemplo com apenas alguns passos. Comece a efetuar um verdadeiro desenvolvimento em cinco minutos e veja resultados de imediato."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/12/2016"
    ms.author="cephalin"
/>

# Implemente a sua primeira aplicação Web no Azure em cinco minutos

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial ajuda-o a implementar a sua primeira aplicação Web no [App Service do Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar o Serviço de Aplicações para criar aplicações, [back-ends de aplicações móveis](/documentation/learning-paths/appservice-mobileapps/) e [aplicações API](../app-service-api/app-service-api-apps-why-best-platform.md).

Com poucas medidas, irá:

- Implementar uma aplicação Web de exemplo (escolha entre ASP.NET, PHP, Node.js, Java ou Python).
- Ver a sua aplicação em execução em segundos.
- Atualizar a sua aplicação Web da mesma forma que [emitiria consolidações do Git](https://git-scm.com/docs/git-push).

Também irá obter uma primeira noção do [Portal do Azure](https://portal.azure.com) e analisar as funcionalidades aí disponíveis.

## Pré-requisitos

- [Instalar o Git](http://www.git-scm.com/downloads).
- [Instalar a CLI do Azure](../xplat-cli-install.md).
- Obter uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Veja uma aplicação Web em ação. [Experimente o Serviço de Aplicações](http://go.microsoft.com/fwlink/?LinkId=523751) de imediato e crie uma aplicação de arranque de curta duração — sem necessidade de cartões de crédito, nem compromissos.

## Implementar uma aplicação Web

Eis como implementar uma aplicação Web no App Service do Azure.

1. Abra uma nova linha de comandos do Windows, uma janela do PowerShell, uma shell do Linux ou um terminal do OS X. Execute `git --version` e `azure --version` para verificar se o Git e a CLI do Azure estão instalados no sua máquina.

    ![Testar a instalação das ferramentas da CLI para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/1-test-tools.png)

    Se não tiver instalado as ferramentas, consulte [Pré-requisitos](#Prerequisites) para obter as ligações para as transferências.

1. Mude para um diretório de trabalho (`CD`) e clone a aplicação de exemplo desta forma:

        git clone <github_sample_url>

    ![Clonar o código de exemplo de aplicação para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/2-clone-sample.png)

    Para *&lt;github_sample_url>*, utilize um dos seguintes URLs, consoante a arquitetura da sua preferência:

    - HTML + CSS + JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. Altere para o repositório da sua aplicação de exemplo. Por exemplo:

        cd app-service-web-html-get-started

3. Inicie sessão no Azure desta forma:

        azure login

    Siga a mensagem de ajuda para continuar o processo de início de sessão.

    ![Iniciar sessão no Azure para criar a sua primeira aplicação Web](./media/app-service-web-get-started/3-azure-login.png)

4. Crie o recurso de aplicações do Serviço de Aplicações no Azure com um nome de aplicação único utilizando o seguinte comando. Quando lhe for pedido, especifique o número da região pretendida.

        azure site create --git <app_name>

    ![Criar o recurso do Azure para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/4-create-site.png)

    >[AZURE.NOTE] Se nunca configurou as credenciais de implementação para a sua subscrição do Azure, ser-lhe-á pedido que as crie. O Serviço de Aplicações utiliza estas credenciais, e não as suas credenciais da conta do Azure, apenas para implementações do Git e inícios de sessão de FTP.

    Agora, a sua aplicação está criada no Azure. Além disso, o seu diretório atual é inicializado pelo Git e ligado à nova aplicação do App Service como um Git remoto.
    Pode navegar para o URL da aplicação (http://&lt;app_name>.azurewebsites.net) e ver a fantástica página HTML predefinida, mas o mais importante agora é introduzir o seu próprio código.

4. Implemente o código de exemplo na nova aplicação do Serviço de Aplicações, tal como emitiria qualquer código com o Git:

        git push azure master

    ![Emitir código para a sua primeira aplicação Web no Azure](./media/app-service-web-get-started/5-push-code.png)    

    Se utilizou uma das arquiteturas de linguagem, será apresentado um resultado diferente. Tal deve-se ao facto de que `git push` não só coloca código no Azure, como também aciona tarefas de implementação no motor de implementação. Se tiver algum ficheiro package.json (Node.js) ou requirements.txt (Python) na raiz (repositório) do projeto ou se tiver um ficheiro packages.config no seu projeto ASP.NET, o script de implementação restaura automaticamente os pacotes necessários. Também pode [ativar a extensão de Compositor](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente ficheiros composer.json na sua aplicação PHP.

Parabéns! Implementou a sua aplicação no App Service do Azure.

## Ver a sua aplicação em execução

Para ver a sua aplicação em execução no Azure, execute este comando a partir de qualquer diretório no seu repositório:

    azure site browse

## Efetuar atualizações à sua aplicação

Agora, pode utilizar o Git para emitir a partir da raiz do projeto (repositório) em qualquer altura para efetuar uma atualização ao site online. Pode fazê-lo da mesma forma que quando implementou a aplicação no Azure pela primeira vez. Por exemplo, sempre que pretender emitir uma nova alteração que tenha testado localmente, execute os seguintes comandos a partir da raiz (repositório) do projeto:

    git add .
    git commit -m "<your_message>"
    git push azure master

## Veja a sua aplicação no portal do Azure

Agora, vamos ao Portal do Azure para ver o que criou:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) com uma conta Microsoft que tenha a sua subscrição do Azure.

2. Na barra da esquerda, clique em **Serviços de Aplicações**.

3. Clique na aplicação que acabou de criar para abrir a página no portal (designada por [painel](../azure-portal-overview.md)). O painel **Definições** também é aberto por predefinição para sua comodidade.

    ![Vista do portal da sua primeira aplicação Web no Azure](./media/app-service-web-get-started/portal-view.png)

O painel do portal da sua aplicação do Serviço de Aplicações analisa um conjunto avançado de definições e ferramentas que pode utilizar para configurar, monitorizar, proteger e resolver problemas da aplicação. Efetue algumas tarefas simples para familiarizar-se com esta interface. (O número da tarefa corresponde ao número na captura de ecrã.)

1. Pare a aplicação.
2. Reinicie a aplicação.
3. Clique na ligação **Grupo de Recursos** para ver todos os recursos implementados no grupo de recursos.
4. Clique em **Definições** > **Propriedades** para ver outras informações sobre a sua aplicação.
5. Clique em **Ferramentas** para aceder a ferramentas úteis de monitorização e resolução de problemas.  

## Passos seguintes

- Eleve a sua aplicação do Azure ao nível seguinte. Ajude a protegê-la com a autenticação. Dimensione-a com base no pedido. Configure alguns alertas de desempenho. Tudo com apenas alguns cliques. Consulte [Adicionar funcionalidades à sua primeira aplicação Web](app-service-web-get-started-2.md).
- Além do Git e da CLI do Azure, estão disponíveis outras maneiras de implementar aplicações Web no Azure. Consulte o artigo [Implementar a aplicação no App Service do Azure](../app-service-web/web-sites-deploy.md).
Encontre os passos de desenvolvimento e implementação preferenciais para a arquitetura da sua linguagem ao selecionar a sua arquitetura na parte superior do artigo.



<!--HONumber=sep16_HO1-->


