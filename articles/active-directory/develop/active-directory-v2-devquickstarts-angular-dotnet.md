---
title: "Aplicação Azure AD v 2.0 .NET AngularJS única página Introdução | Microsoft Docs"
description: "Como criar uma aplicação de página única de JS angular para que inicia sessão dos utilizadores com ambas as contas pessoais da Microsoft e contas profissionais ou escolares."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 6a341781-278f-461b-92ca-7572a06e6852
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c68180c0ecabf5c0732f0db77ef1f3cc93be965b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-angularjs-single-page-app---net"></a>Adicionar início de sessão a uma aplicação de página única de AngularJS - .NET
Neste artigo adicionaremos inicie sessão com contas Microsoft ligada a uma aplicação de AngularJS utilizando o ponto de final de v 2.0 do Azure Active Directory.  O ponto final v 2.0 permite-lhe executar uma única integração na sua aplicação e autenticar os utilizadores com contas pessoais e trabalho/escola.

Este exemplo é uma aplicação de página única de lista de tarefas simples que armazena as tarefas num back-end da API REST, escritas utilizando a estrutura de MVC do .NET 4.5 e protegidas através de tokens de portador do OAuth do Azure AD.  A aplicação de AngularJS utilizará a nossa biblioteca de autenticação de JavaScript de código aberto [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) para lidar com início de sessão completo processo e adquirir tokens para chamar a API REST.  O mesmo padrão pode ser aplicado para autenticar a outras APIs REST, como o [Microsoft Graph](https://graph.microsoft.com).

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download"></a>Transferência
Para começar, terá de transferir e instalar o Visual Studio.  Em seguida, pode clonar ou [transferir](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) uma aplicação de estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

A aplicação esqueleto inclui todos os o código de automático para uma aplicação AngularJS simples, mas está em falta tudo relacionadas com identidade.  Se não quiser acompanhar, em vez disso, pode clonar ou [transferir](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) o exemplo concluído.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## <a name="register-an-app"></a>Registar uma aplicação
Em primeiro lugar, crie uma aplicação no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga estas [detalhadas passos](active-directory-v2-app-registration.md).  Certifique-se de que:

* Adicionar o **Web** plataforma para a sua aplicação.
* Introduza o correto **URI de redirecionamento**. A predefinição para este exemplo é `https://localhost:44326/`.
* Deixe o **permitir fluxo implícito** caixa de verificação ativada. 

Copie o **ID da aplicação** atribuída à sua aplicação, irá precisar das mesmas em breve. 

## <a name="install-adaljs"></a>Instalar adal.js
Para começar, navegue para o projeto que transferiu e instalar adal.js.  Se tiver [bower](http://bower.io/) instalado, pode apenas executar este comando.  Para qualquer correspondência de versão de dependência, selecione apenas a versão superior.

```
bower install adal-angular#experimental
```

Em alternativa, pode transferir manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) e [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Adicionar ambos os ficheiros para o `app/lib/adal-angular-experimental/dist` diretório do `TodoSPA` projeto.

Agora abra o projeto no Visual Studio e carregar adal.js no fim do corpo da página principal:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurar a API REST
Enquanto que estiver a configurar coisas, vamos abordar o trabalho de REST API de back-end.  Na raiz do projeto, abra `web.config` e substitua o `audience` valor.  A API REST irá utilizar este valor para validar os tokens que recebe da aplicação angular para nos pedidos de AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>

...
```

É sempre que vamos gaste debater como funciona a API REST.  Não hesite poke em torno no código, mas se pretender saber mais sobre como proteger web APIs com o Azure AD, veja [neste artigo](active-directory-v2-devquickstarts-dotnet-api.md). 

## <a name="sign-users-in"></a>Utilizadores de início de sessão no
Tempo até ao escrever alguns códigos de identidade.  Poderá ter já reparado que adal.js contém um fornecedor AngularJS, que desempenha corretamente com angular para mecanismos de encaminhamento.  Comece por adicionar o módulo de adal para a aplicação:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Agora pode a inicializar o `adalProvider` com o seu ID de aplicação:

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

Excelente, adal.js tem agora todas as informações de que tem para proteger a sua aplicação e aos utilizadores iniciar sessão.  Para forçar o início de sessão para uma rota específica na aplicação, tudo o que demora é uma linha de código:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Agora quando um utilizador clica o `TodoList` ligação, adal.js será automaticamente redirecionado para o Azure AD para início de sessão se necessário.  Pode também explicitamente enviar pedidos de início de sessão e fim de sessão invocando adal.js no seu controladores:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>Apresentar informações de utilizador
Agora que o utilizador tem sessão iniciado, provavelmente terá de aceder a dados de autenticação de utilizador com sessão iniciada na sua aplicação.  Adal.js expõe estas informações no `userInfo` objeto.  Para aceder a este objeto numa vista, adicione primeiro adal.js para o âmbito de raiz do controlador de correspondente:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Em seguida, o que pode abordar diretamente o `userInfo` objeto na vista: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Também pode utilizar o `userInfo` objecto para determinar se o utilizador tem sessão iniciado ou não.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Chamar a API REST
Por fim, está na altura de obter alguns tokens e chamar a API REST para criar, ler, atualizar e eliminar tarefas.  Bem-adivinhar que?  Não tem de fazer *uma coisa*.  Adal.js automaticamente se tratará de introdução, colocação em cache e atualizar tokens.  É também tratará de anexar esses tokens para enviados pedidos de AJAX enviar para a API REST.  

Exatamente como isto funciona? É tudo graças à magia de [AngularJS intercetores](https://docs.angularjs.org/api/ng/service/$http), que lhe permite adal.js transformar enviar e receber mensagens de http.  Além disso, adal.js parte do princípio de que todos os pedidos de enviam ao mesmo domínio, como a janela deve utilizar tokens concebidos para o mesmo ID de aplicação que a aplicação de AngularJS.  É por isso é utilizado o mesmo ID de aplicação na aplicação angular para e a API de REST do NodeJS.  Obviamente, pode substituir este comportamento e dizer adal.js para obter os tokens para outras APIs REST, se necessário - mas para este cenário simple executará as predefinições.

Eis um fragmento que mostra como é fácil para enviar pedidos com tokens de portador do Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Parabéns!  A aplicação de página única integrada do Azure AD está agora concluída.  Avançar, assumir um bow.  Este pode autenticar os utilizadores, em segurança chamar a API de REST com OpenID Connect de back-end e obter informações básicas sobre o utilizador.  A Box, suporta a qualquer utilizador com um Account pessoal do Microsoft ou uma conta de trabalho/escola do Azure AD.  Executar a aplicação e, num browser, navegue para `https://localhost:44326/`.  Inicie sessão com uma conta Microsoft pessoal ou uma conta de trabalho/escola.  Adicionar tarefas à lista de tarefas do utilizador e terminar sessão.  Tente iniciar sessão com o outro tipo de conta. Se precisar de um inquilino do Azure AD para criar utilizadores de trabalho/profissional, [saber como obter um aqui](active-directory-howto-tenant.md) (é gratuito).

Para saber mais sobre o ponto final v 2.0, head de volta para a nossa [guia para programadores de v 2.0](active-directory-appmodel-v2-overview.md).  Para obter recursos adicionais, consulte:

* [Exemplos do Azure no GitHub >>](https://github.com/Azure-Samples)
* [Azure AD no capacidade excedida da pilha >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* Documentação de AD do Azure no [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.

