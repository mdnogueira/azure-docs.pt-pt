---
title: "Azure AD AngularJS, introdução | Microsoft Docs"
description: "Como criar uma aplicação de página única AngularJS que se integra com o Azure AD para início de sessão e chama APIs do Azure AD protegida utilizando OAuth."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4153910bc03f112f84c26cda6f9c78f11028b934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="help-secure-angularjs-single-page-apps-by-using-azure-ad"></a>Ajudar a proteger as aplicações de página única AngularJS por utilizar o Azure AD

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) faz com que simples e fácil para adicionar o início de sessão, fim de sessão e segura API OAuth chama às suas aplicações de página única.  Permite que as suas aplicações para autenticar os utilizadores com as respetivas contas do Windows Server Active Directory e consumir quaisquer web API do Azure AD ajuda a proteger, tais como as APIs do Office 365 ou a API do Azure.

Para aplicações de JavaScript em execução num browser, o Azure AD fornece o Active Directory Authentication Library (ADAL), ou adal.js. É o único objetivo adal.js torna mais fácil para a sua aplicação obter os tokens de acesso. Para demonstrar tal como é fácil, aqui iremos irá criar uma aplicação AngularJS lista de tarefas que:

* Inicia o utilizador aplicação através da utilização do Azure AD como o fornecedor de identidade.

* Mostra algumas informações sobre o utilizador.
* Em segurança chama para a fazer do lista API a aplicação utilizando os tokens de portador do Azure AD.
* Inicia o utilizador fora da aplicação.

Para criar a aplicação de trabalho completo, tem de:

1. Registe a sua aplicação com o Azure AD.
2. Instale o ADAL e configurar a aplicação de página única.
3. Utilize a ADAL para ajudar a páginas seguras na aplicação de página única.

Para começar, [transferir a estrutura de aplicação](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Também precisa de um inquilino do Azure AD, onde pode criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Passo 1: Registar a aplicação de DirectorySearcher
Para ativar a sua aplicação autenticar os utilizadores e obter tokens, primeiro tem de registá-lo no seu inquilino do Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Se tem sessão iniciada para vários diretórios, poderá ter de Certifique-se de que está a visualizar o diretório correto. Para tal, na barra superior, clique em sua conta. Sob o **diretório** lista, escolha o inquilino do Azure AD, onde é necessário registar a sua aplicação.
3. Clique em **mais serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Siga as instruções e crie uma nova aplicação web e/ou web API:
  * **Nome** descreve a aplicação aos utilizadores.
  * **Uri de redirecionamento** é a localização para os quais do Azure AD irá devolver tokens. A localização predefinida para este exemplo é `https://localhost:44326/`.
6. Depois de concluir o registo, o Azure AD atribui um ID de aplicação único para a sua aplicação.  Irá precisar deste valor nas secções seguintes, por isso, copie-o de separador da aplicação.
7. Adal.js utiliza o fluxo implícito de OAuth para comunicar com o Azure AD. Tem de ativar o fluxo implícito para a sua aplicação:
  1. Clique na aplicação e selecione **manifesto** para abrir o editor de manifesto inline.
  2. Localize o `oauth2AllowImplicitFlow` propriedade. Defina o respetivo valor `true`.
  3. Clique em **guardar** para guardar o manifesto.
8. Conceder permissões em seu inquilino para a sua aplicação. Aceda a **definições** > **propriedades** > **permissões obrigatórias**e clique em de **conceder permissões** botão na barra superior. Clique em **Sim** para confirmar.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Passo 2: Instalar ADAL e configurar a aplicação de página única
Agora que tem uma aplicação no Azure AD, pode instalar adal.js e escrever o seu código relacionadas com identidade.

### <a name="configure-the-javascript-client"></a>Configurar o cliente de JavaScript
Comece por adicionar adal.js ao projeto TodoSPA utilizando a consola do Gestor de pacotes:
  1. Transferir [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e adicioná-la para o `App/Scripts/` diretório de projeto.
  2. Transferir [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e adicioná-la para o `App/Scripts/` diretório de projeto.
  3. Carregar cada script antes do fim do `</body>` no `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Configurar o servidor de back-end
Para back-end para fazer lista API a aplicação de página única aceitar tokens a partir do browser, o back-end tem informações de configuração sobre o registo de aplicação. No projeto TodoSPA, abra `web.config`. Substitua os valores de elementos no `<appSettings>` secção para refletir os valores que utilizou no portal do Azure. O código será referenciar estes valores, sempre que utilizar a ADAL.
  * `ida:Tenant`é o domínio de inquilino do Azure AD – por exemplo, contoso.onmicrosoft.com.
  * `ida:Audience`é o ID de cliente da aplicação que copiou do portal.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Passo 3: Utilizar a ADAL para ajudar a proteger páginas na aplicação de página única
Adal.js integra AngularJS rota e fornecedores de HTTP, para que possa ajudar vistas individuais seguras na sua aplicação de página única.

1. No `App/Scripts/app.js`, colocar no módulo adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicializar `adalProvider` utilizando os valores de configuração do registo da aplicação, também `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Ajudar a proteger o `TodoList` vista na aplicação utilizando apenas uma linha de código: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Resumo
Tem agora uma aplicação de página única segura, que pode iniciar sessão em utilizadores e emitir pedidos protegidos de token de portador para a API de back-end. Quando um utilizador clica o **TodoList** ligação, adal.js será automaticamente redirecionado para o Azure AD para início de sessão se necessário. Além disso, adal.js ligará automaticamente um token de acesso a quaisquer pedidos de Ajax que são enviados para o back-end da aplicação.  

Os passos anteriores são o bare mínimo necessário para criar uma aplicação de página única utilizando adal.js. Mas algumas outras funcionalidades são úteis na aplicação de página única:

* Explicitamente emitir pedidos de início de sessão e fim de sessão, pode definir funções os controladores que invocam adal.js.  No `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Pode querer apresentam informações de utilizador na IU da aplicação. O serviço ADAL já foi adicionado para o `userDataCtrl` controlador, para que possa aceder a `userInfo` objeto na vista de associados, `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Existem muitos cenários em que poderá ser útil de saber se o utilizador tem sessão iniciado ou não. Também pode utilizar o `userInfo` objeto para recolher estas informações.  Por exemplo, no `index.html`, pode mostrar um o **início de sessão** ou **terminar** botão com base no estado de autenticação:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

A aplicação de página única integrada no AD do Azure pode autenticar os utilizadores, em segurança chamar o back-end através da utilização de OAuth 2.0 e obter informações básicas sobre o utilizador. Se ainda não o fez, agora é o tempo para preencher o seu inquilino com alguns utilizadores. Executar a aplicação de página única de lista de tarefas e inicie sessão com uma esses utilizadores. Adicionar tarefas à lista de tarefas do utilizador, termine e inicie sessão novamente.

Adal.js torna mais fácil incorporar funcionalidades de identidade comuns na sua aplicação. -Encarrega-se de todo o trabalho de dirty para si: gestão de cache, o suporte de protocolo de OAuth, apresentando o utilizador com uma início de sessão da IU, atualizar tokens expirados e muito mais.

Para referência, o exemplo concluído (sem os valores de configuração) está disponível no [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Passos seguintes
Agora pode passar para cenários adicionais. Pode querer tente: [chamar uma API web CORS a partir de uma aplicação de página única](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
