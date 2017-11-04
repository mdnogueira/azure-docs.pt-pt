---
title: "Azure AD v2 JS SPA orientado configuração - utilização | Microsoft Docs"
description: "Como aplicações de JavaScript SPA podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: f52157df298ddfc1c1b29a18dc9a54aae59b52a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utilizar a biblioteca de autenticação da Microsoft (MSAL) para início de sessão do utilizador

1.  Crie um ficheiro denominado `app.js`. Se estiver a utilizar o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com o botão direito e selecione: `Add`  >  `New Item`  >  `JavaScript File`:
2.  Adicione o seguinte código no seu `app.js` ficheiro:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });

    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - the token type: usually id_token
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>Mais Informações

Depois de um utilizador clica o *'Chamar Microsoft Graph API'* botão pela primeira vez, `callGraphApi` chamadas de método `loginRedirect` a sessão do utilizador. Este método resulta em redirecionar o utilizador a *ponto final do Microsoft Azure Active Directory v2* a linha de comandos e validar as credenciais do utilizador. Como resultado de um bem-sucedida início de sessão, o utilizador é redirecionado para original *index.html* página e um token é recebida, processados pelo `msal.js` e as informações contidas no token é colocado em cache. Este token é conhecido como o *ID token* e contém informações básicas sobre o utilizador, tais como o nome a apresentar do utilizador. Se pretender utilizar os dados fornecidos por este token para quaisquer fins, tem de certificar-se de que este token é validado pelo seu servidor de back-end para garantir que o token foi emitido para um utilizador válido para a sua aplicação.

A SPA gerada por este guia não faz utiliza diretamente o token de ID – em vez disso, aquele invoca `acquireTokenSilent` e/ou `acquireTokenRedirect` adquirir um *token de acesso* utilizado para consultar o Microsoft Graph API. Se precisar de uma amostra que valida o token de ID, observe [isto](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "exemplo active-directory-javascript-singlepageapp-dotnet-webapi-v2 Github") aplicação de exemplo no GitHub – o exemplo utiliza um ASP API web do .NET para validação do token.

#### <a name="getting-a-user-token-interactively"></a>A obter um utilizador token interativamente

Depois do inicial início de sessão, não pretender que a peça aos utilizadores reautenticação sempre que precisam de pedir um token de acesso, para um recurso – *acquireTokenSilent* deve ser utilizada a maioria do tempo para adquirir tokens. Existem no entanto situações que precisa forçar os utilizadores interagem com o ponto final do Azure Active Directory v2 – alguns exemplos incluem:
-   Os utilizadores podem ter de reintroduzir as suas credenciais porque a palavra-passe expirou
-   A aplicação está a solicitar acesso a um recurso que o utilizador tem de autorizar
-   Não é necessária autenticação de dois fatores

Chamar o *acquireTokenRedirect(scope)* resultar em redirecionar utilizadores para o ponto final do Azure Active Directory v2 (ou *acquireTokenPopup(scope)* resultado de uma janela de pop-up) onde os utilizadores têm de interagir com por confirmar as suas credenciais, concedendo o consentimento para o recurso necessário ou concluir os dois fator autenticação.

#### <a name="getting-a-user-token-silently"></a>A obter um utilizador token automaticamente
O ` acquireTokenSilent` método processa aquisições token e a renovação sem qualquer interação do utilizador. Depois de `loginRedirect` (ou `loginPopup`) é executado pela primeira vez, `acquireTokenSilent` é o método costuma ser utilizado para obter os tokens utilizados para aceder a recursos protegidos para chamadas subsequentes - como as chamadas para pedir ou renovar tokens são efetuadas automaticamente.
`acquireTokenSilent`poderá falhar em alguns casos-por exemplo, palavra-passe o utilizador expirou. A aplicação pode processar esta exceção de duas formas:

1.  Efetuar uma chamada para `acquireTokenRedirect` imediatamente, o que resulta numa pedir ao utilizador para iniciar sessão. Este padrão é normalmente utilizado em aplicações online em que não existe nenhum conteúdo não autenticado na aplicação disponível para o utilizador. O exemplo gerado por esta configuração orientada utiliza este padrão.

2. Aplicações também podem efetuar uma indicação de visual para o utilizador que um interativa início de sessão é necessário, para que o utilizador pode seleccionar no momento certo para iniciar sessão ou a aplicação pode tentar novamente `acquireTokenSilent` numa altura posterior. Isto é normalmente utilizado quando o utilizador pode utilizar outras funcionalidades da aplicação sem ser interrompidos - por exemplo, não há conteúdo não autenticado na aplicação. Neste caso, o utilizador pode decidir quando iniciar sessão para aceder ao recurso protegido ou para atualizar as informações Desatualizadas.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chamar o Microsoft Graph API utilizando o token obtido apenas

Adicione o seguinte código no seu `app.js` ficheiro:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Obter mais informações sobre como efetuar uma chamada REST em relação a uma API protegida

A aplicação de exemplo criado por este guia, o `callWebApiWithToken()` método é utilizado para efetuar um HTTP `GET` contra um recurso protegido que necessita de um token de pedido e, em seguida, devolver o conteúdo para o autor da chamada. Este método adiciona o token adquirido no *cabeçalho de autorização de HTTP*. Para a aplicação de exemplo criada por este guia, o recurso é o Microsoft Graph API *-me* ponto final – apresenta informações de perfil do utilizador.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para terminar sessão de utilizador

Adicione o seguinte código no seu `app.js` ficheiro:

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
