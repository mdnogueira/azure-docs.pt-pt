---
title: "Aplicação web do Azure AD .NET, introdução | Microsoft Docs"
description: "Crie uma aplicação de web de MVC do .NET que se integra com o Azure AD para início de sessão."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3c1e558c9d41e385f80939203a3457b74e30973b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Aplicação web do ASP.NET início de sessão e fim de sessão com o Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ao fornecer um único início de sessão e fim de sessão com apenas alguns linhas de código, Azure Active Directory (Azure AD) permite simples para que possa outsource gestão de identidade da aplicação web. Pode iniciar a sessão de utilizadores e para aplicações web ASP.NET, utilizando a implementação Microsoft da Open Web Interface para middleware .NET (OWIN). Comunidade middleware OWIN está incluída no .NET Framework 4.5. Este artigo mostra como utilizar OWIN para:

* Iniciar sessão utilizadores para aplicações web através do Azure AD como o fornecedor de identidade.
* Apresente algumas informações de utilizador.
* Início de sessão dos utilizadores as aplicações.

## <a name="before-you-get-started"></a>Antes de começar
* Transferir o [estrutura aplicação](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou transferir o [exemplo concluído](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* Também precisa de um inquilino do Azure AD na qual pode registar a aplicação. Se ainda não tiver um inquilino do Azure AD, [saber como obter um](active-directory-howto-tenant.md).

Quando estiver pronto, siga os procedimentos das secções junto quatro.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Passo 1: Registar a nova aplicação com o Azure AD
Para configurar a aplicação para autenticar os utilizadores, primeiro registá-lo no seu inquilino efetuando o seguinte procedimento:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em nome da sua conta. Sob o **diretório** lista, selecione o inquilino do Active Directory onde é necessário registar a aplicação.
3. Clique em **mais serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Siga as instruções para criar um novo **aplicação Web e/ou End WebAPI**.
  * **Nome** descreve a aplicação aos utilizadores.
  * **URL de início de sessão** é o URL de base da aplicação. URL de predefinido a estrutura é https://localhost:44320 /.
6. Após concluir o registo, o Azure AD atribui a aplicação um ID de aplicação único. Copie o valor a partir da página da aplicação para utilizar nas secções seguintes.
7. Do **definições** -> **propriedades** página para a sua aplicação, atualize o URI de ID de aplicação. O **URI de ID de aplicação** é um identificador exclusivo para a aplicação. A Convenção de nomenclatura é `https://<tenant-domain>/<app-name>` (por exemplo, `https://contoso.onmicrosoft.com/my-first-aad-app`).

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Passo 2: Configurar a aplicação para utilizar o pipeline de autenticação da OWIN
Neste passo, configure o middleware OWIN para utilizar o protocolo de autenticação OpenID Connect. Utilize OWIN para emitir pedidos de início de sessão e fim de sessão, gerir sessões de utilizador, obter informações de utilizador e assim sucessivamente.

1. Para começar, adicione os pacotes de NuGet middleware OWIN para o projeto utilizando a consola do Gestor de pacotes.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Para adicionar uma classe de Startup da OWIN ao projeto denominado `Startup.cs`, clique com o botão direito no projeto, selecione **adicionar**, selecione **Novo Item**e, em seguida, procure **OWIN**. O middleware OWIN invoca o **Configuration(...)**  método quando a aplicação for iniciada.
3. Alterar a declaração de classe para `public partial class Startup`. Iremos já tiver implementado parte desta classe para si no outro ficheiro. No **Configuration(...)**  método, crie uma chamada para **ConfgureAuth(...)**  para configurar a autenticação da aplicação.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Abra o ficheiro de App_Start\Startup.Auth.cs e, em seguida, implementar o **ConfigureAuth(...)**  método. Os parâmetros que fornece *OpenIDConnectAuthenticationOptions* servir como coordenadas para a aplicação para comunicar com o Azure AD. Terá também de configurar a autenticação de cookie, porque o middleware OpenID Connect utiliza cookies em segundo plano.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. Abra o ficheiro Web. config na raiz do projeto e, em seguida, introduza os valores de configuração no `<appSettings>` secção.
  * `ida:ClientId`: O GUID que copiou do portal do Azure no "passo 1: registar a nova aplicação com o Azure AD."
  * `ida:Tenant`: O nome do inquilino do Azure AD (por exemplo, contoso.onmicrosoft.com).
  * `ida:PostLogoutRedirectUri`: O indicador de que diz ao Azure AD em que um utilizador deve ser redirecionado após um início de sessão pedido é concluído com êxito.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Passo 3: Utilize OWIN para emitir pedidos de início de sessão e fim de sessão para o Azure AD
A aplicação agora está corretamente configurada para comunicar com o Azure AD através do protocolo de autenticação OpenID Connect. OWIN processou todos os detalhes das mensagens de autenticação de composição, tokens de validação do Azure AD e manutenção de sessões de utilizador. Tudo o falta fazer é conceder aos seus utilizadores uma forma de iniciar sessão e terminar sessão.

1. Pode utilizar as etiquetas nos controladores de pedir aos utilizadores iniciar sessão antes de poderem acederem a determinadas páginas de autorizar. Para tal, abra Controllers\HomeController.cs e, em seguida, adicione o `[Authorize]` etiqueta para a ação acerca de.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. Também pode utilizar OWIN diretamente emitir pedidos de autenticação de dentro do seu código. Para tal, abra Controllers\AccountController.cs. Em seguida, nas ações SignIn() e SignOut(), emita o desafio de OpenID Connect e os pedidos de fim de sessão.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Abrir Views\Shared\_LoginPartial.cshtml para mostrar o utilizador as ligações de início de sessão e fim de sessão da aplicação e para imprimir o nome do utilizador numa vista.

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
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

## <a name="step-4-display-user-information"></a>Passo 4: Apresentar informações de utilizador
Quando autentica os utilizadores com OpenID Connect, o Azure AD devolve uma id_token para a aplicação que contém "afirmações" ou asserções sobre o utilizador. Pode utilizar estas afirmações para personalizar a aplicação da seguinte forma:

1. Abra o ficheiro Controllers\HomeController.cs. Pode aceder às afirmações do utilizador no seu controladores através de `ClaimsPrincipal.Current` objeto principal de segurança.

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Crie e execute a aplicação. Se ainda não já criou um novo utilizador no seu inquilino com um domínio onmicrosoft.com, agora é o tempo para o fazer. Eis como:

  a. Inicie sessão com esse utilizador e, tenha em atenção a como a identidade do utilizador é refletida na barra superior.

  b. Termine sessão e, em seguida, inicie sessão novamente como outro utilizador no seu inquilino.

  c. Se estiver a feeling particularmente ambicioso, registar e executar outra instância desta aplicação (com a suas próprias clientId) e veja único início de sessão em ação.

## <a name="next-steps"></a>Passos seguintes
Para referência, consulte [o exemplo concluído](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (sem os valores de configuração).

Agora pode aceder a tópicos mais avançados. Por exemplo, experimente [proteger uma API Web com o Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
