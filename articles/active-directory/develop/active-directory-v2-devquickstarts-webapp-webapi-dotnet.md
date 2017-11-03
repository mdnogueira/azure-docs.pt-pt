---
title: "Azure AD v 2.0 .NET web API chamar aplicação introdução | Microsoft Docs"
description: "Como criar uma aplicação de Web de MVC do .NET que web de chamadas de serviços utilizando pessoais contas Microsoft e de trabalho ou contas profissional para início de sessão."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dc3162ae8e6ce622139125c2e78fa45d2e90d534
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>Chamar uma API web a partir de uma aplicação web do .NET
Com o ponto final v 2.0, pode adicionar autenticação para as suas aplicações web e APIs web com suporte para ambas as contas pessoais da Microsoft e contas profissionais ou escolares rapidamente.  Aqui, iremos irá criar uma aplicação web MVC que assina os utilizadores utilizando o OpenID Connect, com algumas ajuda middleware OWIN da Microsoft.  A aplicação web irá obter os tokens de acesso de OAuth 2.0 para uma web api protegidos por OAuth 2.0, que permite criar, ler e eliminar um determinado utilizador "lista de tarefas".

Este tutorial irá focar-se principalmente nas utilizando MSAL para adquirir e utilize tokens de acesso numa aplicação web, descrita em completo [aqui](active-directory-v2-flows.md#web-apps).  Como pré-requisitos, poderá pretender primeiro saber como [adicionar o início de sessão básico para uma aplicação web](active-directory-v2-devquickstarts-dotnet-web.md) ou como [corretamente proteger uma API web](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Transferir o código de exemplo
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Para acompanhar, pode [transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Em alternativa, pode [transferir a aplicação concluída como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou clonar a aplicação concluída:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registar uma aplicação
Criar uma nova aplicação em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga estas [detalhadas passos](active-directory-v2-app-registration.md).  Certifique-se de que:

* Copie o **Id da aplicação** atribuída à aplicação, irá precisar das mesmas em breve.
* Criar um **segredo da aplicação** do **palavra-passe** tipo e copie o valor para utilizar mais tarde
* Adicionar o **Web** plataforma para a sua aplicação.
* Introduza o correto **URI de redirecionamento**. Indica o uri de redirecionamento para o Azure AD, onde as respostas de autenticação devem ser direcionadas - é a predefinição para este tutorial `https://localhost:44326/`.

## <a name="install-owin"></a>Instalar OWIN
Adicionar os pacotes de NuGet middleware OWIN para o `TodoList-WebApp` projeto através da consola Gestor de pacote.  O middleware OWIN será utilizado para emitir pedidos de início de sessão e fim de sessão, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>O utilizador iniciar sessão
Configurar agora o middleware OWIN para utilizar o [protocolo de autenticação OpenID Connect](active-directory-v2-protocols.md).  

* Abra o `web.config` ficheiro na raiz do `TodoList-WebApp` projeto e introduza os valores de configuração da sua aplicação de `<appSettings>` secção.
  * O `ida:ClientId` é o **Id da aplicação** atribuído à sua aplicação no portal de registo.
  * O `ida:ClientSecret` é o **segredo da aplicação** que criou no portal de registo.
  * O `ida:RedirectUri` é o **Uri de redirecionamento** que introduziu no portal.
* Abra o `web.config` ficheiro na raiz do `TodoList-Service` projeto e substitua o `ida:Audience` com o mesmo **Id da aplicação** como acima.
* Abra o ficheiro `App_Start\Startup.Auth.cs` e adicione `using` instruções para as bibliotecas de acima.
* No mesmo ficheiro, implementar o `ConfigureAuth(...)` método.  Os parâmetros que fornece `OpenIDConnectAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com o Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Utilizar MSAL para obter os tokens de acesso
No `AuthorizationCodeReceived` notificação, queremos utilizar [OAuth 2.0 em conjunto com OpenID Connect](active-directory-v2-protocols.md) para resgatar authorization_code um token de acesso para o serviço de lista de tarefas.  MSAL pode facilitar este processo para si:

* Em primeiro lugar, instale a versão de pré-visualização do MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* E adicionar outro `using` instrução para o `App_Start\Startup.Auth.cs` ficheiro para MSAL.
* Agora, adicione um novo método, o `OnAuthorizationCodeReceived` processador de eventos.  Este processador utilizará MSAL adquirir um token de acesso para a API da lista de tarefas e irá armazenar o token na cache de token do MSAL para utilizar mais tarde:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* Nas web apps, MSAL tem uma cache de token extensível que pode ser utilizada para armazenar os tokens.  Este exemplo implementa o `NaiveSessionCache` que utiliza o armazenamento de sessão de http para tokens de cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Chamar a API Web
Agora está na altura de utilizar, na verdade, access_token que obteve no passo 3.  Abra a aplicação web `Controllers\TodoListController.cs` ficheiro, que faz com que todos os pedidos CRUD para a API da lista de tarefas.

* Pode utilizar MSAL novamente aqui para obter access_tokens da cache de MSAL.  Primeiro, adicione um `using` instrução para MSAL para este ficheiro.
  
    `using Microsoft.Identity.Client;`
* No `Index` do ação, utilize MSAL `AcquireTokenSilentAsync` método para obter um access_token que podem ser utilizados para ler dados a partir do serviço de lista de tarefas:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* O exemplo, em seguida, adiciona o token resultante para o pedido HTTP GET como o `Authorization` cabeçalho, o qual o serviço de lista de tarefas utiliza para autenticar o pedido.
* Se o serviço de lista de tarefas devolve um `401 Unauthorized` resposta, access_tokens no MSAL ficou inválido por algum motivo.  Neste caso, deve remover quaisquer access_tokens da cache de MSAL e mostrar uma mensagem que têm de iniciar sessão novamente, que irá reiniciar o fluxo de aquisição de token de utilizador.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* Da mesma forma, se MSAL for não é possível devolver um access_token por qualquer motivo, deve instruir o utilizador iniciar sessão novamente.  Isto é tão simple como obtendo qualquer `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* O mesmo exacta `AcquireTokenSilentAsync` chamada é implementd no `Create` e `Delete` ações.  Nas web apps, pode utilizar este método MSAL obter access_tokens sempre que precisar na sua aplicação.  MSAL tratará a ter de adquirir, colocação em cache e atualizar os tokens para si.

Por fim, crie e execute a sua aplicação!  Inicie sessão com uma Account Microsoft ou uma conta do Azure AD e repare como a identidade do utilizador é refletida na barra de navegação superior.  Adicionar e eliminar alguns itens da lista de tarefas do utilizador para ver que o OAuth 2.0 protegida chamadas de API em ação.  Tem agora uma aplicação web e web API, ambas protegidas através de protocolos padrão da indústria, que podem autenticar os utilizadores com ambas as respetivas contas pessoais e de trabalho/escola.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Passos Seguintes
Para obter recursos adicionais, consulte:

* [O guia para programadores de v 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Etiqueta de StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.

