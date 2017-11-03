---
title: B2C do Azure Active Directory | Microsoft Docs
description: "Como criar uma aplicação web que tem sessão-up/início de sessão, Editar perfil e palavra-passe reposição através do Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: barbaraselden
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 3144ced01b524abb035dc1c6f0cdf764bec46804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Criar uma aplicação web ASP.NET com o Azure Active Directory B2C perfil de inscrição, início de sessão, editar e reposição de palavra-passe

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Adicionar as funcionalidades de identidade do Azure AD B2C à sua aplicação web
> * Registar a sua aplicação web no seu diretório do Azure AD B2C
> * Criar um utilizador sessão-up/início de sessão, a edição de perfil e a política de reposição de palavra-passe para a sua aplicação web

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ligar o seu inquilino do B2C para uma conta do Azure. Pode criar uma conta do Azure gratuita [aqui](https://azure.microsoft.com/en-us/).
- Terá de [Microsoft Visual Studio](https://www.visualstudio.com/) ou programa semelhante para ver e modificar o código de exemplo.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos e muito mais. Se ainda não tiver uma, crie um diretório do B2C antes de prosseguir neste guia.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Terá de ligar o inquilino do B2C à sua subscrição do Azure. Depois de selecionar **criar**, selecione o **inquilino de ligação do Azure AD B2C existente, a minha subscrição do Azure** opção e, em seguida, no **inquilino do Azure AD B2C** pendente, selecione o inquilino que pretende associar.

## <a name="create-and-register-an-application"></a>Criar e registar uma aplicação

Em seguida, terá de criar e registar a aplicação no diretório do B2C. Este artigo fornece informações que necessita de comunicar de forma segura com a sua aplicação do Azure AD B2C. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

Quando tiver terminado, terá uma API e uma aplicação nativa nas definições da aplicação.

## <a name="create-policies-on-your-b2c-tenant"></a>Criar políticas no seu inquilino do B2C

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: **inscrição & início de sessão**, **perfil editar**, e **reposição de palavra-passe**.  Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Para cada política, lembre-se de que selecione o atributo de nome de apresentação ou afirmação e copie o nome da sua política para utilização posterior.

### <a name="add-your-identity-providers"></a>Adicionar os seus fornecedores de identidade

A partir do seu definições, selecione **fornecedores de identidade** e escolha o nome de utilizador inscrição ou inscrição de correio eletrónico.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Criar uma política de inscrição e o início de sessão

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfis

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Criar uma política de reposição de palavra-passe

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Depois de criar as políticas, está pronto para criar a sua aplicação.

## <a name="download-the-sample-code"></a>Transferir o código de exemplo

O código deste tutorial é mantido no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Pode clonar o exemplo ao executar:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Depois de transferir o código de exemplo, abra o ficheiro de .sln do Visual Studio para começar a utilizar. O ficheiro de solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp`é a aplicação de web MVC que o utilizador interage com. O `TaskService` é uma API Web de back-end da aplicação que armazena a lista de tarefas de cada utilizador. Este artigo só cobre a aplicação `TaskWebApp`. Para saber como criar `TaskService` utilizando o Azure AD B2C, consulte [nosso tutorial de api web do .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Código de atualização para utilizar o seu inquilino e políticas

O nosso exemplo está configurado para utilizar as políticas e o ID de cliente do nosso inquilino de demonstração. A ligá-lo para o seu inquilino, terá de abrir `web.config` no `TaskWebApp` projeto e substitua os valores seguintes:

* `ida:Tenant` pelo nome do seu inquilino
* `ida:ClientId` pelo ID da sua aplicação Web
* `ida:ClientSecret` pela chave de segredo da sua aplicação Web
* `ida:SignUpSignInPolicyId` pelo nome da sua política de “Inscrição ou Início de Sessão”
* `ida:EditProfilePolicyId` pelo nome da sua política de “Editar Perfil”
* `ida:ResetPasswordPolicyId` pelo nome da sua política de “Repor Palavras-Passe”

## <a name="launch-the-app"></a>Inicie a aplicação
Do Visual Studio, inicie a aplicação. Navegue até ao separador lista de tarefas e anote o URl é: https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*& client_id = *YourclientID*...

Inscreva-se a aplicação utilizando o seu nome de utilizador ou endereço de correio eletrónico. Terminar sessão, em seguida, voltar a iniciar sessão e editar o perfil ou repor a palavra-passe. Terminar sessão e inicie sessão como outro utilizador. 

## <a name="add-social-idps"></a>Adicionar IDPs redes sociais

Atualmente, a aplicação suporta apenas o utilizador, inscrição e o início de sessão utilizando **contas locais**; contas armazenadas no diretório do B2C que utilizam um nome de utilizador e palavra-passe. Ao utilizar o Azure AD B2C, pode adicionar suporte para outros **fornecedores de identidade** (IDPs) sem alterar qualquer do seu código.

Para adicionar redes sociais IDPs à sua aplicação, comece por seguir as instruções detalhadas nestes artigos. Para cada IDP que pretende suportar, tem de registar uma aplicação nesse sistema e obter um ID de cliente.

* [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Depois de adicionar os fornecedores de identidade para o seu diretório do B2C, editar cada um dos seus três políticas para incluir IDPs novo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de guardar as suas políticas, execute novamente a aplicação.  Deverá ver os novo IDPs adicionados como início de sessão e as experiências de opções de inscrição em cada de identidade.

Pode experimentar com as políticas e observar o efeito na sua aplicação de exemplo. Adicionar ou remover IDPs, manipular afirmações de aplicação ou altere os atributos de inscrição. Experimentação até que pode ver como políticas, pedidos de autenticação e OWIN associar em conjunto.

## <a name="sample-code-walkthrough"></a>Instruções de código de exemplo
As secções seguintes mostram como o código de aplicação de exemplo está configurado. Pode utilizar isto como um guia no seu desenvolvimento de aplicações futuros.

### <a name="add-authentication-support"></a>Adicionar suporte de autenticação

Agora pode configurar a sua aplicação para utilizar o Azure AD B2C. A aplicação comunica com o Azure AD B2C, enviando pedidos de autenticação OpenID Connect. Os pedidos que estipulam a experiência de utilizador a aplicação que pretende executar especificando a política. Pode utilizar a biblioteca OWIN da Microsoft para enviar estes pedidos, executar políticas, gerir sessões de utilizador e muito mais.

#### <a name="install-owin"></a>Instalar OWIN

Para começar, adicione os pacotes de NuGet middleware OWIN para o projeto utilizando a consola do Gestor de pacotes do Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de startup da OWIN

Adicione uma classe de startup da OWIN à API, com o nome `Startup.cs`  Com o botão direito no projeto, selecione **Adicionar** e **Novo Item**, e, em seguida, pesquise OWIN. O middleware da OWIN vai invocar o método `Configuration(…)` quando a aplicação for iniciada.

No nosso exemplo, alterámos a declaração da classe para `public partial class Startup` e implementámos a outra parte da classe em `App_Start\Startup.Auth.cs`. Dentro do método `Configuration`, adicionámos uma chamada para `ConfigureAuth`, que é definida em `Startup.Auth.cs`. Depois das alterações, `Startup.cs` tem o seguinte aspeto:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Configure o middleware de autenticação

Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(...)` método. Os parâmetros que fornece `OpenIdConnectAuthenticationOptions` servir como coordenadas para a sua aplicação para comunicar com o Azure AD B2C. Se não especificar determinados parâmetros, irá utilizar o valor predefinido. Por exemplo, não especificamos o `ResponseType` no exemplo, o valor predefinido `code id_token` vão ser utilizados em cada pedido de envio para o Azure AD B2C.

Terá também de configurar a autenticação de cookie. O middleware OpenID Connect utiliza cookies para manter as sessões de utilizador, entre outros.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

No `OpenIdConnectAuthenticationOptions` acima, iremos definir um conjunto de funções de chamada de retorno para notificações específicas que são recebidas pelo middleware OpenID Connect. Destes comportamentos são definidos utilizando um `OpenIdConnectAuthenticationNotifications` de objeto e armazenadas para o `Notifications` variável. No nosso exemplo, iremos definir três chamadas de retorno diferentes consoante o evento.

### <a name="using-different-policies"></a>Utilizar políticas diferentes

O `RedirectToIdentityProvider` notificação é acionada sempre que é efetuado um pedido para o Azure AD B2C. Na função de chamada de retorno `OnRedirectToIdentityProvider`, verificamos na chamada de saída, se de que pretende utilizar uma política diferente. Para fazer uma reposição de palavra-passe ou editar um perfil, terá de utilizar a política correspondente, tais como a política em vez da política predefinida de "Inscrição ou o início de sessão" de repor a palavra-passe.

No nosso exemplo, quando um utilizador pretende repor a palavra-passe ou editar o perfil, iremos adicionar a política que preferir para utilizar no contexto de OWIN. Que pode ser feita através da seguinte forma:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

E que pode implementar a função de chamada de retorno `OnRedirectToIdentityProvider` efetuando o seguinte procedimento:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Códigos de autorização de processamento

O `AuthorizationCodeReceived` notificação é acionada quando é recebido um código de autorização. O middleware OpenID Connect não suporta troca códigos para tokens de acesso. Pode trocar manualmente o código para o token de uma função de chamada de retorno. Para obter mais informações, consulte o [documentação](active-directory-b2c-devquickstarts-web-api-dotnet.md) que explica como.

### <a name="handling-errors"></a>Processamento de erros

O `AuthenticationFailed` notificação é acionada quando ocorre uma falha de autenticação. O método de chamada de retorno, pode processar os erros queira. No entanto, deve adicionar uma verificação para o código de erro `AADB2C90118`. Durante a execução da política de "Inscrição ou o início de sessão", o utilizador tem a oportunidade de selecionar um **esqueceu a palavra-passe?** ligação. Neste caso, o Azure AD B2C envia a aplicação esse código de erro que indica que a aplicação deve efetuar um pedido de através da política de reposição de palavra-passe em vez disso.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Enviar pedidos de autenticação para o Azure AD

A aplicação agora está corretamente configurada para comunicar com o Azure AD B2C, utilizando o protocolo de autenticação OpenID Connect. OWIN gere os detalhes das mensagens de autenticação de composição, tokens de validação do Azure AD B2C e manter a sessão do utilizador. Tudo o que resta é para iniciar o fluxo de cada utilizador.

Quando seleciona um utilizador **início de sessão de segurança/início de sessão**, **Editar perfil**, ou **Repor palavra-passe** na aplicação web, a ação associada é invocada no `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Também pode utilizar OWIN para terminar sessão de utilizador da aplicação. No `Controllers\AccountController.cs` temos:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Além de invocar explicitamente uma política, pode utilizar um `[Authorize]` tag em controladores de que executa uma política, se o utilizador não tem sessão iniciado. Abra `Controllers\HomeController.cs` e adicione o `[Authorize]` etiquetas para o controlador de afirmações.  Seleciona OWIN a política de último configurada quando o `[Authorize]` etiquetas é atingida.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Apresentar informações de utilizador

Quando se autentica utilizadores utilizando o OpenID Connect, o Azure AD B2C devolve um token de ID para a aplicação que contém **afirmações**. Estes são asserções sobre o utilizador. Pode utilizar afirmações para personalizar a sua aplicação.

Abra o ficheiro `Controllers\HomeController.cs`. Pode aceder às afirmações de utilizador na sua controladores através de `ClaimsPrincipal.Current` objeto principal de segurança.

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Pode aceder a quaisquer afirmações que a aplicação recebe da mesma forma.  Uma lista de todas as afirmações a aplicação recebe está disponível para si no **afirmações** página.
