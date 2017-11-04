---
title: "Azure AD v2 ASP.NET Web Server começar - utilize | Microsoft Docs"
description: "Implementar Microsoft início de sessão numa solução ASP.NET com uma aplicação de baseadas no browser web tradicional utilizando o padrão de OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 3b7d29e48c91f40e8782a5e32a52998b815fe331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Adicionar um controlador para processar pedidos de início de sessão e fim de sessão

Este passo mostra como criar um novo controlador para expor os métodos de início de sessão e fim de sessão.

1.  Clique com o botão direito do `Controllers` pastas e selecione`Add` > `Controller`
2.  Selecione `MVC (.NET version) Controller – Empty`.
3.  Clique em *adicionar*
4.  Nome `HomeController` e clique em *adicionar*
5.  Adicionar *OWIN* referências para a classe:

```csharp
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
```
<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Adicione os dois métodos abaixo para lidar com início de sessão e fim de sessão para o seu controlador iniciando um desafio de autenticação através de código:
</li>
</ol>

```csharp
/// <summary>
/// Send an OpenID Connect sign-in request.
/// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
/// </summary>
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
    HttpContext.GetOwinContext().Authentication.SignOut(
            OpenIdConnectAuthenticationDefaults.AuthenticationType,
            CookieAuthenticationDefaults.AuthenticationType);
}
```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Criar a home page a aplicação para iniciar sessão de utilizadores através de um botão de início de sessão

No Visual Studio, crie uma nova vista para adicionar o botão de início de sessão e apresentar informações de utilizador após a autenticação:

1.  Clique com o botão direito do `Views\Home` pastas e selecione`Add View`
2.  Dê-lhe o nome `Index`.
3.  Adicione o seguinte HTML, que inclui o botão de início de sessão, para o ficheiro:

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Guide</title>
</head>
<body>
@if (!Request.IsAuthenticated)
{
    <!-- If the user is not authenticated, display the sign-in button -->
    <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
        <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
        <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
        <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
        <rect x="150" y="129" width="122" height="122" fill="#F35325" />
        <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
        <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
        <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
        <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
        </svg>
    </a>
}
else
{
    <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
    <br /><br />
    @Html.ActionLink("See Your Claims", "Index", "Claims")
    <br /><br />
    @Html.ActionLink("Sign out", "SignOut", "Home")
}
@if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
{
    <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
}
</body>
</html>
```
<!--start-collapse-->
### <a name="more-information"></a>Mais Informações
> Esta página adiciona um botão de início de sessão no formato SVG com um fundo preto:<br/>![Início de sessão com a Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-use/aspnetsigninbuttonsample.png)<br/> Para obter mais início de sessão botões, visite o [nesta página](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "diretrizes de imagem corporativa").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Adicionar um controlador para apresentar afirmações do utilizador
Este controlador demonstra as utilizações do `[Authorize]` atributo para proteger um controlador. Este atributo restringe o acesso para o controlador, permitindo apenas que os utilizadores autenticados. O código abaixo utilizam o atributo para apresentar as afirmações de utilizador que foram obtidas como parte de início de sessão.

1.  Clique com o botão direito do `Controllers` pasta:`Add` > `Controller`
2.  Selecione `MVC {version} Controller – Empty`.
3.  Clique em *adicionar*
4.  Nome`ClaimsController`
5.  Substitua o código da sua classe de controlador com o código abaixo - esta ação adiciona a `[Authorize]` atributo para a classe:

```csharp
[Authorize]
public class ClaimsController : Controller
{
    /// <summary>
    /// Add user's claims to viewbag
    /// </summary>
    /// <returns></returns>
    public ActionResult Index()
    {
        var claimsPrincipalCurrent = System.Security.Claims.ClaimsPrincipal.Current;
        //You get the user’s first and last name below:
        ViewBag.Name = claimsPrincipalCurrent.FindFirst("name").Value;

        // The 'preferred_username' claim can be used for showing the username
        ViewBag.Username = claimsPrincipalCurrent.FindFirst("preferred_username").Value;

        // The subject claim can be used to uniquely identify the user across the web
        ViewBag.Subject = claimsPrincipalCurrent.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier).Value;

        // TenantId is the unique Tenant Id - which represents an organization in Azure AD
        ViewBag.TenantId = claimsPrincipalCurrent.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;

        return View();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mais Informações
> Devido à utilização do `[Authorize]` atributo, todos os métodos deste controlador só pode ser executado se o utilizador ser autenticado. Se o utilizador não é autenticado e tenta aceder ao controlador de, OWIN irá iniciar um desafio de autenticação e forçar o utilizador a autenticar. O código acima analisa a coleção de afirmações da `ClaimsPrincipal.Current` instância para atributos de utilizador específico incluídas no token do utilizador. Estes atributos incluem o nome do utilizador completo e nome de utilizador, bem como o assunto de identificador do utilizador global. Também contém a *ID do inquilino*, que representa o ID de organização do utilizador. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Criar uma vista para apresentar afirmações do utilizador

No Visual Studio, crie uma nova vista para apresentar afirmações do utilizador numa página web:

1.  Clique com o botão direito do `Views\Claims` pasta e:`Add View`
2.  Dê-lhe o nome `Index`.
3.  Adicione o seguinte HTML para o ficheiro:

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Sample</title>
    <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
</head>
<body style="padding:50px">
    <h3>Main Claims:</h3>
    <table class="table table-striped table-bordered table-hover">
        <tr><td>Name</td><td>@ViewBag.Name</td></tr>
        <tr><td>Username</td><td>@ViewBag.Username</td></tr>
        <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
        <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
    </table>
    <br />
    <h3>All Claims:</h3>
    <table class="table table-striped table-bordered table-hover table-condensed">
    @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
    {
        <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
    }
</table>
    <br />
    <br />
    @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
</body>
</html>
```
