---
title: "Azure AD Cordova, introdução | Microsoft Docs"
description: "Como criar uma aplicação Cordova que se integra com o Azure AD para início de sessão e chama APIs do Azure AD protegida utilizando OAuth."
services: active-directory
documentationcenter: 
author: vibronet
manager: mbaldwin
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: d9f53148787729d29a0a89cce1b8b2b83ba228f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrar o Azure AD com um Apache aplicação Cordova
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Pode utilizar o Apache Cordova para desenvolver aplicações HTML5/JavaScript que podem ser executadas em dispositivos móveis, como aplicações nativas totalmente funcional. Com o Azure Active Directory (Azure AD), pode adicionar capacidades de autenticação de nível empresarial às suas aplicações Cordova.

Um plug-in de Cordova encapsula num wrapper do Azure AD SDKs nativas no iOS, Android, Loja Windows e Windows Phone. Ao utilizar que Plug-in, pode melhorar a sua aplicação para suportar o início de sessão com contas do Windows Server Active Directory dos utilizadores, obter acesso ao Office 365 e APIs do Azure e, inclusivamente ajudar a proteger chamadas para sua própria web personalizado API.

Neste tutorial, iremos utilizar o Apache Cordova Plug-in para o Active Directory Authentication Library (ADAL) para melhorar a uma aplicação simples, adicionando as seguintes funcionalidades:

* Com apenas alguns linhas de código, autenticar um utilizador e obter um token.
* Utilize esse token para invocar a Graph API para consultar esse diretório e apresentar os resultados.  
* Utilize a cache de token ADAL para minimizar a pedidos de autenticação para o utilizador.

Para efetuar essas melhorias, tem de:

1. Registar uma aplicação com o Azure AD.
2. Adicione o código para a sua aplicação para pedir tokens.
3. Adicionar código para utilizar o token para consultar a Graph API e ver os resultados.
4. Crie o projeto de implementação Cordova com todas as plataformas que pretende visar, adicione a ADAL de Cordova Plug-in e testar a solução emuladores.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

* Um inquilino do Azure AD em que tenha uma conta com direitos de desenvolvimento de aplicações.
* Ambiente de desenvolvimento está configurado para utilizar o Apache Cordova.  

Se ainda tiver ambos configurar, avançar diretamente para o passo 1.

Se não tiver um inquilino do Azure AD, utilize o [obter instruções sobre como obter uma](active-directory-howto-tenant.md).

Se não tiver Apache Cordova configurar no seu computador, instale o seguinte:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (pode ser facilmente instalado através do Gestor de pacote NPM: `npm install -g cordova`)

As instalações anteriores deverão funcionar tanto no PC no Mac.

Cada plataforma de destino apresenta diferentes pré-requisitos:

* Para criar e executar uma aplicação para Windows Tablet/PC ou Windows Phone:
  * Instalar [Visual Studio 2013 para Windows com a atualização 2 ou posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (rápida ou outra versão) ou [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Para criar e executar uma aplicação para iOS:

  * Instalar Xcode 6. x ou posterior. Transfira-à partir de [site Apple Developer](http://developer.apple.com/downloads) ou o [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Instalar [ios-sim](https://www.npmjs.org/package/ios-sim). Pode utilizá-lo para iniciar as aplicações iOS no simulador iOS na linha de comandos. (Pode instalá-lo facilmente através de terminal: `npm install -g ios-sim`.)
* Para criar e executar uma aplicação para Android:

  * Instalar [Kit de desenvolvimento Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou posterior. Certifique-se `JAVA_HOME` (variável de ambiente) está corretamente definida de acordo com o caminho de instalação do JDK (por exemplo, c:\Programas\Microsoft Files\Java\jdk1.7.0_75).
  * Instalar [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) e adicione o `<android-sdk-location>\tools` localização (por exemplo, C:\tools\Android\android-sdk\tools) para o `PATH` variável de ambiente.
  * Abra o Gestor do Android SDK (por exemplo, através do terminal: `android`) e instalar:
    * *Android 5.0.1 (API 21)* plataforma SDK
    * *Ferramentas de criação de SDK Android* versão 19.1.0 ou posterior
    * *Suporte Android repositório* (Extras)

  O SDK Android não fornece qualquer instância de emulador predefinido. Crie um executando `android avd` no terminal e, em seguida, selecionar **criar**, se pretender executar a aplicação Android num emulador. Recomendamos um nível de API de 19 ou superior. Para obter mais informações sobre as opções de emulador e a criação de Android, consulte [Gestor de AVD](http://developer.android.com/tools/help/avd-manager.html) no site do Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Passo 1: Registar uma aplicação com o Azure AD
Este passo é opcional. Este tutorial fornece pré-aprovisionada valores que pode utilizar para ver o exemplo na ação sem efetuar qualquer aprovisionamento no seu próprio inquilino. No entanto, recomendamos que execute este passo e se familiarize com o processo, porque irá ser necessário quando criar as suas próprias aplicações.

O Azure AD emite tokens para apenas as aplicações conhecidas. Antes de poder utilizar o Azure AD da sua aplicação, terá de criar uma entrada para o mesmo no seu inquilino. Para registar uma nova aplicação no seu inquilino:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. No **diretório** lista, escolha o inquilino do Azure AD, onde é necessário registar a sua aplicação.
3. Clique em **mais serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Siga as instruções e criar um **aplicação cliente nativa**. (Embora aplicações Cordova HTML com base, estamos a criar uma aplicação cliente nativa aqui. O **aplicação cliente nativa** tem de selecionar a opção ou a aplicação poderá não funciona.)
  * **Nome** descreve a aplicação aos utilizadores.
  * **URI de redirecionamento** é o URI que é utilizado para devolver os tokens para a sua aplicação. Introduza **http://MyDirectorySearcherApp**.

Depois de concluir o registo, o Azure AD atribui um ID de aplicação único para a sua aplicação. Irá precisar deste valor nas secções seguintes. Pode encontrá-lo no separador da aplicação da aplicação criado recentemente.

Para executar `DirSearchClient Sample`, conceder a permissão de aplicação criado recentemente para consultar a AD Graph API do Azure:

1. Do **definições** página, selecione **permissões obrigatórias**e, em seguida, selecione **adicionar**.  
2. Para a aplicação do Azure Active Directory, selecione **Microsoft Graph** como a API e adicione o **aceder ao diretório como o utilizador com sessão iniciada** permissão em **permissões delegadas**.  Isto permite que a aplicação consultar a Graph API para os utilizadores.

## <a name="step-2-clone-the-sample-app-repository"></a>Passo 2: Clonar o repositório da aplicação de exemplo
A partir da shell ou a linha de comandos, escreva o seguinte comando:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Passo 3: Criar a aplicação Cordova
Existem várias formas para criar aplicações Cordova. Neste tutorial, iremos utilizar a interface de linha de comandos (CLI) de Cordova.

1. A partir da shell ou a linha de comandos, escreva o seguinte comando:

        cordova create DirSearchClient

   Este comando cria a estrutura de pasta e andaime para o projeto Cordova.

2. Mover para a nova pasta DirSearchClient:

        cd .\DirSearchClient

3. Copie o conteúdo do projeto de arranque na subpasta www através da utilização de um Gestor de ficheiro ou o seguinte comando na sua shell:

  * Windows:`xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * MAC:`cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Adicione a lista branca de plug-in. Isto é necessário para invocar a Graph API.

        cordova plugin add cordova-plugin-whitelist

5. Adicione todas as plataformas que pretende suportar. Para ter uma amostra de trabalho, terá de executar, pelo menos, um dos seguintes comandos. Tenha em atenção que não será possível emular iOS no Windows ou emular Windows num Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Adicione a ADAL para o plug-in Cordova ao seu projeto:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Passo 4: Adicionar código para autenticar os utilizadores e obter os tokens do Azure AD
A aplicação que estiver a desenvolver neste tutorial, irá fornecer uma funcionalidade de pesquisa de diretório simples. O utilizador pode, em seguida, escreva o alias de qualquer utilizador no diretório e visualizar alguns atributos básicos. O projeto de arranque contém a definição da interface de utilizador básico da aplicação (em www/index.html) e andaime que cablagem um evento de aplicações básica ciclos, enlaces de interface de utilizador e apresentar lógica (www/js/index.js) de resultados. A tarefa apenas à esquerda para consiste em Adicionar a lógica que implementa as tarefas de identidade.

A primeira coisa que precisa de fazer no seu código é introduzir os valores de protocolo do Azure AD utiliza para identificar a sua aplicação e os recursos que o destino. Esses valores serão utilizados para construir os pedidos de token mais tarde. Insira o seguinte fragmento no topo do ficheiro index.js:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

O `redirectUri` e `clientId` valores devem corresponder os valores que descrevem a sua aplicação no Azure AD. Pode encontrar as do **configurar** separador no portal do Azure, como descrito no passo 1, anteriormente neste tutorial.

> [!NOTE]
> Se tiver optado por não registar uma nova aplicação no seu próprio inquilino, pode colar simplesmente os valores pré-configuradas, tal como está. Em seguida, pode ver o exemplo em execução, embora sempre deve criar sua própria entrada para as aplicações que se destinam para produção.

Em seguida, adicione o código de pedido de token. Insira o seguinte fragmento entre o `search` e `renderData` definições:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Vamos examinar essa função ao eliminar-a para baixo em duas partes principais.
Este exemplo foi concebido para trabalhar com qualquer inquilino, por oposição a ser associado a um determinado. Utiliza o "/ comuns" ponto final, que permite ao utilizador introduzir qualquer conta no momento de autenticação e redireciona o pedido para o inquilino onde pertence.

Esta primeira parte do método inspeciona a cache ADAL para ver se um token já está armazenado. Se Sim, o método utiliza os inquilinos onde o token provém para reinicializar ADAL. Isto é necessário para evitar pedidos adicionais, porque a utilização de "/ comuns" sempre resulta num pedir ao utilizador introduzir uma nova conta.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
A segunda parte do método efetua o pedido de token adequado. O `acquireTokenSilentAsync` método pede-lhe ADAL para devolver um token para o recurso especificado sem qualquer UX. a mostrar Que pode acontecer se a cache já tem um token de acesso adequado armazenado, ou se um token de atualização pode ser utilizado para aceder um novo token sem que mostra a qualquer linha de comandos. Se que a tentativa falhar, iremos reverter `acquireTokenAsync`– que visibly pedirá ao utilizador para autenticar.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Agora que temos o token, iremos finally pode invocar a Graph API e executar a consulta de pesquisa que queremos. Insira o seguinte fragmento abaixo o `authenticate` definição:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Os ficheiros a partir do ponto de indicar um UX simple para introduzir o alias do utilizador na caixa de texto. Este método utiliza esse valor para construir uma consulta, combiná-la com o token de acesso, envia-as à Microsoft Graph e analisar os resultados. O `renderData` método, já está presente no ficheiro de ponto de início, trata-se de visualizar os resultados.

## <a name="step-5-run-the-app"></a>Passo 5: Executar a aplicação
A aplicação está finally pronta para ser executado. Funcionamento é simple: quando a aplicação for iniciada, introduza o alias do utilizador que pretende procurar e, em seguida, clique no botão. Lhe for pedida a autenticação. Após a autenticação com êxito e êxito pesquisa, são apresentados os atributos de utilizador procurar.

Execuções subsequentes irão efetuar a pesquisa sem que mostra a qualquer linha de comandos, graças à presença do token anteriormente adquirida na cache.

Os passos para executar a aplicação concretos variam consoante a plataforma.

### <a name="windows-10"></a>Windows 10
   Tablet/PC:`cordova run windows --archs=x64 -- --appx=uap`

   Dispositivo móvel (requer um dispositivo Windows 10 Mobile ligado a um PC):`cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Durante a primeira execução, poderá ser-lhe pedido para iniciar sessão uma licença de programador. Para obter mais informações, consulte [licença de programador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Windows 8.1 Tablet/PC
   `cordova run windows`

   > [!NOTE]
   > Durante a primeira execução, poderá ser-lhe pedido para iniciar sessão uma licença de programador. Para obter mais informações, consulte [licença de programador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Para executar num dispositivo ligado:`cordova run windows --device -- --phone`

   Para executar o emulador predefinido:`cordova emulate windows -- --phone`

   Utilize `cordova run windows --list -- --phone` para ver todos os destinos disponíveis e `cordova run windows --target=<target_name> -- --phone` para executar a aplicação num dispositivo específico ou emulador (por exemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Para executar num dispositivo ligado:`cordova run android --device`

   Para executar o emulador predefinido:`cordova emulate android`

   Certifique-se de que criou uma instância de emulador utilizando o Gestor de AVD, conforme descrito anteriormente na secção "Pré-requisitos".

   Utilize `cordova run android --list` para ver todos os destinos disponíveis e `cordova run android --target=<target_name>` para executar a aplicação num dispositivo específico ou emulador (por exemplo, `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Para executar num dispositivo ligado:`cordova run ios --device`

   Para executar o emulador predefinido:`cordova emulate ios`

   > [!NOTE]
   > Certifique-se de que tem o `ios-sim` instalado para ser executada no emulador do pacote. Para obter mais informações, consulte a secção "Pré-requisitos".

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Passos seguintes
Para referência, o exemplo concluído (sem os valores de configuração) está disponível no [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Agora pode passar para os cenários mais avançados (e mais interessantes). Pode querer tente: [proteger uma API Web do Node.js com o Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
