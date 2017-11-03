---
title: "Aplicação de início de sessão de introdução de web do Azure AD v 2.0 .NET | Microsoft Docs"
description: "Como criar uma aplicação Web MVC do .NET que assina os utilizadores com ambos os Account pessoal do Microsoft e contas profissionais ou escolares."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ba5bdf7daba6086b70aec54ebe25d4445fa708c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Adicionar início de sessão a uma aplicação de web de MVC do .NET
Com o ponto final v 2.0, pode adicionar rapidamente a autenticação para as suas aplicações web com suporte para ambas as contas pessoais da Microsoft e contas profissionais ou escolares.  Nas web apps do ASP.NET, isto é possível utilizar o middleware OWIN da Microsoft incluído no .NET Framework 4.5.

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>
>

 Aqui iremos irá criar uma aplicação web que utiliza OWIN para o utilizador iniciar sessão, apresente algumas informações sobre o utilizador e assinar o utilizador fora da aplicação.

## <a name="download"></a>Transferência
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Para acompanhar, pode [transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

A aplicação concluída é fornecida no final deste tutorial bem.

## <a name="register-an-app"></a>Registar uma aplicação
Criar uma nova aplicação em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga estas [detalhadas passos](active-directory-v2-app-registration.md).  Certifique-se de que:

* Copie o **Id da aplicação** atribuída à aplicação, irá precisar das mesmas em breve.
* Adicionar o **Web** plataforma para a sua aplicação.
* Introduza o correto **URI de redirecionamento**. Indica o uri de redirecionamento para o Azure AD, onde as respostas de autenticação devem ser direcionadas - é a predefinição para este tutorial `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Instalar e configurar a autenticação da OWIN
Aqui, iremos irá configurar o middleware OWIN para utilizar o protocolo de autenticação OpenID Connect.  OWIN será utilizada para emitir pedidos de início de sessão e fim de sessão, gerir a sessão do utilizador e obter informações sobre o utilizador, entre outras coisas.

1. Para começar, abra o `web.config` ficheiro na raiz do projeto e introduza os valores de configuração da sua aplicação de `<appSettings>` secção.

  * O `ida:ClientId` é o **Id da aplicação** atribuído à sua aplicação no portal de registo.
  * O `ida:RedirectUri` é o **Uri de redirecionamento** que introduziu no portal.

2. Em seguida, adicione os pacotes de NuGet middleware OWIN para o projeto utilizando a consola do Gestor de pacotes.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Adicionar um "OWIN arranque Class" ao projeto denominado `Startup.cs` direito clique no projeto--> **adicionar** --> **Novo Item** --> procure "OWIN".  O middleware da OWIN vai invocar o método `Configuration(...)` quando a aplicação for iniciada.
4. Alterar a declaração de classe para `public partial class Startup` -iremos já tiver implementado parte desta classe para si no outro ficheiro.  No `Configuration(...)` método, crie uma chamada para ConfigureAuth(...) para configurar a autenticação para a sua aplicação web  

        ```C#
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método.  Os parâmetros que fornece `OpenIdConnectAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com o Azure AD.  Também terá de configurar o Cookie de autenticação - utiliza cookies, por baixo bastidores, o middleware OpenID Connect.

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
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação
A aplicação agora está corretamente configurada para comunicar com o ponto final v 2.0 utilizando o protocolo de autenticação OpenID Connect.  OWIN tem Tratado todos os detalhes ugly de mensagens de autenticação de composição, tokens de validação do Azure AD e manutenção de sessão do utilizador.  Tudo o falta fazer é conceder aos seus utilizadores uma forma de iniciar sessão e terminar sessão.

- Pode utilizar autorizar as etiquetas nos controladores para exigir que o utilizador inicia sessão antes de aceder a uma determinada página.  Abra `Controllers\HomeController.cs`e adicione o `[Authorize]` etiqueta para o controlador acerca de.
        
        ```C#
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- Também pode utilizar OWIN diretamente emitir pedidos de autenticação de dentro do seu código.  Abra `Controllers\AccountController.cs`.  Nas ações SignIn() e SignOut(), emita o desafio OpenID Connect e os pedidos de fim de sessão, respetivamente.

        ```C#
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- Agora, abra `Views\Shared\_LoginPartial.cshtml`.  Este é onde irá mostrar o utilizador ligações de início de sessão e fim de sessão da sua aplicação e imprimir o nome do utilizador numa vista.

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
                    </li>
                    <li>
                        @Html.ActionLink("Sign out", "SignOut", "Account")
                    </li>
                </ul>
            </text>
        }
        else
        {
            <ul class="nav navbar-nav navbar-right">
                <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }
        ```

## <a name="display-user-information"></a>Apresentar informações de utilizador
Quando está a autenticar os utilizadores com OpenID Connect, o ponto final v 2.0 devolve uma id_token para a aplicação que contenha afirmações ou asserções sobre o utilizador.  Pode utilizar estas afirmações para personalizar a sua aplicação:

- Abra o ficheiro `Controllers\HomeController.cs`.  Pode aceder às afirmações do utilizador no seu controladores através de `ClaimsPrincipal.Current` objeto principal de segurança.

        ```C#
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>Executar
Por fim, crie e execute a sua aplicação!   Inicie sessão com uma Account Microsoft pessoal ou uma conta escolar ou profissional e repare como a identidade do utilizador é refletida na barra de navegação superior.  Tem agora uma aplicação web protegida através de protocolos padrão da indústria que podem autenticar os utilizadores com ambas as respetivas contas pessoais e de trabalho/escola.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), ou pode cloná-la a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Passos seguintes
Agora pode mover para tópicos mais avançados.  Poderá querer experimentar:

[Proteger uma API Web com o ponto final v 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obter recursos adicionais, consulte:

* [O guia para programadores de v 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Etiqueta de StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.
