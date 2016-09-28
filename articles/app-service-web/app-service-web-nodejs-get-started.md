<properties
    pageTitle="Introdução às Web Apps Node.js no App Service do Azure"
    description="Saiba como implementar uma aplicação Node.js numa aplicação Web no App Service do Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>


# Introdução às Web Apps Node.js no App Service do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar uma aplicação [Node.js][NODEJS] simples e implementá-la numa [aplicação Web] no [App Service do Azure] a partir de um ambiente de linha de comandos como cmd.exe ou bash. As instruções deste tutorial podem ser seguidas em qualquer sistema operativo capaz de executar Node.js.

<a name="prereq"></a>
## Pré-requisitos

- **Node.js** ([Clique aqui para instalar][NODEJS])
- **Bower** ([Clique aqui para instalar][BOWER])
- **Yeoman** ([Clique aqui para instalar][YEOMAN])
- **Git** ([Clique aqui para instalar][GIT])
- **CLI do Azure** ([Clique aqui para instalar][CLI do Azure])
- Uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita] ou [ativar os benefícios de subscritor do Visual Studio].

## Criar e implementar uma aplicação Web Node.js simples

1. Abra o terminal da linha de comandos à sua escolha e instale o [Gerador do Express para o Yeoman].

        npm install -g generator-express

2. `CD` num diretório de trabalho e gere uma aplicação expressa com a seguinte sintaxe:

        yo express
        
    Escolha as opções seguintes quando lhe for solicitado:  

    `? Would you like to create a new directory for your project?` **Sim**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **Nenhum**  
    `? Select a database to use:` **Nenhuma**  
    `? Select a build tool to use:` **Grunt**

3. `CD` no diretório de raiz da sua nova aplicação nova e inicie-a para se certificar de que é executada no seu ambiente de desenvolvimento:

        npm start

    No browser, navegue até <http://localhost:3000> para se certificar de que consegue ver a home page do Express. Depois de verificar que a sua aplicação está a ser executada corretamente, utilize `Ctrl-C` para a interromper.
    
1. Mudar para o modo ASM e iniciar sessão no Azure (para fazê-lo, precisa da [CLI do Azure](#prereq)):

        azure config mode asm
        azure login

    Siga o pedido para continuar o processo de início de sessão num browser com uma conta Microsoft que tenha a sua subscrição do Azure.

2. Certifique-se de que ainda se encontra no diretório de raiz da sua aplicação e, em seguida, crie o recurso de aplicações do App Service no Azure com um nome de aplicação único com o comando seguinte; por exemplo: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Siga o pedido para selecionar uma região do Azure para a implementação. Se nunca configurou as credenciais de implementação do Git/FTP para a sua subscrição do Azure, ser-lhe-á solicitado que as crie.

3. Abra o ficheiro ./config/config.js na raiz da aplicação e altere a porta de produção para `process.env.port`; a sua propriedade `production` no objeto `config` deve parecer-se semelhante ao seguinte exemplo.

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Esta funcionalidade permite à aplicação Node.js responder a pedidos da Web na porta predefinida que o iisnode escuta.
    
4. Guarde as alterações e, em seguida, utilize o git para implementar a sua aplicação no Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    O gerador do Express já fornece um ficheiro .gitignore, por isso, o `git push` não irá consumir largura de banda ao tentar carregar o diretório node_modules/.

5. Por fim, inicie a aplicação Azure online no browser:

        azure site browse

    Deverá ver agora a aplicação Web Node.js em execução no App Service do Azure.
    
    ![Exemplo de navegação para a aplicação implementada.][deployed-express-app]

## Atualizar a aplicação Web Node.js

Para efetuar atualizações à sua aplicação Web Node.js em execução no App Service, basta executar `git add`, `git commit` e `git push` tal como fez quando implementou a sua aplicação Web pela primeira vez.
     
## Como é implementada a sua aplicação Node.js pelo App Service

O App Service do Azure utiliza o [iisnode] para executar aplicações Node.js. A CLI do Azure e o motor do Kudu (implementação de Git) funcionam em conjunto para lhe proporcionar uma experiência otimizada ao desenvolver e implementar aplicações Node.js a partir da linha de comandos. 

- `azure site create --git` reconhece o padrão de Node.js comum de server.js ou app.js e cria um iisnode.yml no seu diretório de raiz. Pode utilizar este ficheiro para personalizar o iisnode.
- Em `git push azure master`, o Kudu automatiza as seguintes tarefas de implementação:

    - Se package.json estiver na raiz do repositório, execute `npm install --production`.
    - Gere um Web.config para o iisnode que aponta para o script de arranque no package.json (por exemplo, server.js ou app.js).
    - Personalize o Web.config para preparar a sua aplicação para a depuração com o Node-Inspector.
    
## Utilizar uma arquitetura Node.js

Se utilizar uma arquitetura Node.js popular, como [Sails.js][SAILSJS] ou [MEAN.js][MEANJS] para desenvolver aplicações, pode implementá-los no App Service. As arquiteturas populares Node.js têm as suas caraterísticas específicas e as respetivas dependências são constantemente atualizadas. No entanto, o App Service coloca à sua disposição os registos de stdout e stderr, pelo que pode saber exatamente o que está a acontecer com a aplicação e efetuar alterações em conformidade. Para obter mais informações, consulte [Obter registos de stdout e stderr do iisnode](#iisnodelog).

Os tutoriais seguinte mostrar-lhe-ão como trabalhar com uma arquitetura específica no App Service:

- [Implementar uma aplicação Web Sails.js no App Service do Azure]
- [Criar uma aplicação de chat Node.js com o Socket.IO no App Service do Azure]
- [Como utilizar o io.js com as Web Apps do App Service do Azure]

## Utilizar um motor Node.js específico

No seu fluxo de trabalho normal, pode dizer ao App Service para utilizar um motor Node.js específico, tal como faria normalmente no package.json.
Por exemplo:

    "engines": {
        "node": "5.5.0"
    }, 

O motor de implementação do Kudu determina qual o motor Node.js a utilizar pela seguinte ordem:

- Em primeiro lugar, observe o iisnode.yml para ver se `nodeProcessCommandLine` está especificado. Se sim, utilize-o.
- Em seguida, observe o package.json para ver se `"node": "..."` está especificado no objeto `engines`. Se sim, utilize-o.
- Escolha uma versão do Node.js predefinida por predefinição.

<a name="iisnodelog"></a>
## Obter registos de stdout e stderr do iisnode

Para ler os registos do iisnode, siga os passos abaixo.

> [AZURE.NOTE] Depois de concluir estes passos, os ficheiros de registo podem não existir até que ocorra um erro.

1. Abra o ficheiro iisnode.yml fornecido pela CLI do Azure.

2. Defina os dois parâmetros seguintes: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Em conjunto, dizem ao iisnode no App Service para colocar o resultado de stdout e stderror no diretório D:\home\site\wwwroot\**iisnode**.

3. Guarde as alterações e, em seguida, emita as alterações para o Azure com os seguintes comandos de Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
   O iisnode está agora configurado. Os passos seguintes mostram-lhe como aceder a estes registos.
     
4. No browser, aceda à consola de depuração do Kudu relativa à sua aplicação, que se encontra em:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Tenha em atenção que este URL é diferente do URL da aplicação Web ao ter "*.scm.*" adicionado ao nome DNS. Se omitir esse adição no URL, obterá um erro 404.

5. Navegue até D:\home\site\wwwroot\iisnode

    ![Navegar para a localização dos ficheiros de registo de iisnode.][iislog-kudu-console-find]

6. Clique no ícone **Editar** relativo ao registo que pretende ler. Também pode clicar em **Transferir** ou em **Eliminar** se pretender.

    ![Abrir um ficheiro de registo de iisnode.][iislog-kudu-console-open]

    Agora, pode ver o registo para o ajudar a depurar a sua implementação do App Service.
    
    ![Examinar um ficheiro de registo de iisnode.][iislog-kudu-console-read]

## Depurar a aplicação com o Node-Inspector

Se utilizar o Node-Inspector para depurar as suas aplicações Node.js, pode utilizá-lo para a sua aplicação do App Service online. O Node-Inspector está pré-instalado na instalação do iisnode para o App Service. Além disso, se implementar através do Git, o Web.config gerado automaticamente a partir do Kudu já contém toda a configuração de que necessita para ativar o Node-Inspector.

Para ativar o Node-Inspector, siga estes passos:

1. Abra o iisnode.yml na raiz do repositório e especifique os seguintes parâmetros: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Guarde as alterações e, em seguida, emita as alterações para o Azure com os seguintes comandos de Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Agora, basta navegar até ao ficheiro de arranque da sua aplicação conforme especificado pelo script de arranque no package.json, com /debug adicionado ao URL. Por exemplo,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## Mais recursos

- [Especificar uma versão do Node.js numa aplicação do Azure](../nodejs-specify-node-version-azure-apps.md)
- [Melhores práticas e guia de resolução de problemas para as aplicações Node.js no Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Como depurar uma aplicação Web Node.js no App Service do Azure](web-sites-nodejs-debug.md)
- [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
- [Web Apps do App Service do Azure: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro para Programadores do Node.js](/develop/nodejs/)
- [Introdução às Web Apps no App Service do Azure](app-service-web-get-started.md)
- [Explorar a Consola de Depuração do Kudu Super Secreta]

<!-- URL List -->

[CLI do Azure]: ../xplat-cli-install.md
[App Service do Azure]: ../app-service/app-service-value-prop-what-is.md
[ativar os benefícios de subscritor do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[BOWER]: http://bower.io/
[Criar uma aplicação de chat Node.js com o Socket.IO no App Service do Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implementar uma aplicação Web Sails.js no App Service do Azure]: ./app-service-web-nodejs-sails.md
[Explorar a Consola de Depuração do Kudu Super Secreta]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Gerador do Express para o Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[Como utilizar o io.js com as Web Apps do App Service do Azure]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[NODEJS]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[inscrever-se numa avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[aplicação Web]: ./app-service-web-overview.md
[YEOMAN]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Sep16_HO3-->


