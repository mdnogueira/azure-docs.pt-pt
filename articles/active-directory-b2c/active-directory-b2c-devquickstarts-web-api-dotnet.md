---
title: B2C do Azure Active Directory | Microsoft Docs
description: "Como criar uma aplicação .NET Web e chamar uma web api utilizando os tokens de acesso do Azure Active Directory B2C e OAuth 2.0."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 48452eb68f826d1c7aa61d5e5531f941ac1422b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>O Azure AD B2C: Chamar uma API web do .NET a partir de uma aplicação web do .NET

Ao utilizar o Azure AD B2C, pode adicionar as funcionalidades de gestão de identidade poderosas para as suas aplicações web e web APIs. Este artigo aborda como pedir tokens de acesso e chamadas de disponibilizar a partir de uma aplicação web do .NET "lista de tarefas" para um .NET api web.

Este artigo não abrange como implementar o início de sessão, inscrição e gestão de perfis com o Azure AD B2C. Concentra-se em chamadas das APIs web depois do utilizador já está autenticado. Se ainda não o fez, deve:

* Começar com uma [aplicação web do .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Começar com uma [api web do .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Pré-requisito

Para criar uma aplicação web que chama uma web api, terá de:

1. [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).
2. [Registar uma web api](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Registar uma aplicação web](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Configurar políticas](active-directory-b2c-reference-policies.md).
5. [Web de conceder permissões de aplicação para utilizar o web api](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> A aplicação cliente e a API Web têm de utilizar o mesmo diretório do Azure AD B2C.
>

## <a name="download-the-code"></a>Transferir o código

O código deste tutorial é mantido no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Pode clonar o exemplo ao executar:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Depois de transferir o código de exemplo, abra o ficheiro de .sln do Visual Studio para começar a utilizar. O ficheiro de solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp`é uma aplicação web MVC que o utilizador interage com. O `TaskService` é uma API Web de back-end da aplicação que armazena a lista de tarefas de cada utilizador. Este artigo não abrange a criação de `TaskWebApp` aplicação web ou o `TaskService` web api. Para saber como criar a aplicação web do .NET utilizando o Azure AD B2C, consulte a nossa [tutorial da aplicação web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Para saber como criar o .NET APIS da web protegidas com o Azure AD B2C, consulte a nossa [tutorial de .NET de web API](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Atualizar a configuração do Azure AD B2C

O nosso exemplo está configurado para utilizar as políticas e o ID de cliente do nosso inquilino de demonstração. Se gostaria de utilizar o seu inquilino:

1. Abra `web.config` no projeto `TaskService` e substitua os valores de:

    * `ida:Tenant` pelo nome do seu inquilino
    * `ida:ClientId`com o seu ID de aplicação de api web
    * `ida:SignUpSignInPolicyId` pelo nome da sua política de “Inscrição ou Início de Sessão”

2. Abra `web.config` no projeto `TaskWebApp` e substitua os valores de:

    * `ida:Tenant` pelo nome do seu inquilino
    * `ida:ClientId` pelo ID da sua aplicação Web
    * `ida:ClientSecret` pela chave de segredo da sua aplicação Web
    * `ida:SignUpSignInPolicyId` pelo nome da sua política de “Inscrição ou Início de Sessão”
    * `ida:EditProfilePolicyId` pelo nome da sua política de “Editar Perfil”
    * `ida:ResetPasswordPolicyId` pelo nome da sua política de “Repor Palavras-Passe”



## <a name="requesting-and-saving-an-access-token"></a>Pedir e guardar um token de acesso

### <a name="specify-the-permissions"></a>Especifique as permissões

Para efetuar a chamada para a API web, tem de autenticar o utilizador (utilizando a política de início de sessão-up/início de sessão) e [receber um token de acesso](active-directory-b2c-access-tokens.md) do Azure AD B2C. Para poder receber um token de acesso, primeiro tem de especificar as permissões que pretende que o token de acesso para conceder. As permissões estão especificadas no `scope` parâmetro quando efetua o pedido para o `/authorize` ponto final. Por exemplo, para adquirir um token de acesso com a permissão "ler" para a aplicação de recursos que tem o URI de ID de aplicação de `https://contoso.onmicrosoft.com/tasks`, o âmbito seria `https://contoso.onmicrosoft.com/tasks/read`.

Para especificar o âmbito no nosso exemplo, abra o ficheiro `App_Start\Startup.Auth.cs` e definir o `Scope` variável na OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>O código de autorização para um token de acesso do Exchange

Depois de um utilizador conclui a experiência de inscrição ou início de sessão, a aplicação irá receber um código de autorização do Azure AD B2C. O middleware OWIN OpenID Connect irá armazenar o código, mas não a será exchange para um token de acesso. Pode utilizar o [biblioteca MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) para tornar o exchange. No nosso exemplo, configurámos uma chamada de retorno de notificação para o middleware OpenID Connect sempre que é recebido um código de autorização. A chamada de retorno, utilizamos MSAL para o código para obter um token do exchange e guardar o token para a cache.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Chamar a API web

Esta secção descreve como utilizar o token foi recebido durante a sessão-up/início de sessão com o Azure AD B2C para poder aceder a API web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Obter o token guardado nos controladores

O `TasksController` é responsável para comunicar com a API web e para enviar pedidos HTTP para a API para ler, criar e eliminar tarefas. Porque a API está protegida pelo Azure AD B2C, tem de obter primeiro o token que guardou no passo anterior.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Ler tarefas da API web

Quando tiver um token, pode anexá-lo para o HTTP `GET` pedir do `Authorization` cabeçalho para chamar com segurança `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Criar e eliminar tarefas na API web

Siga o mesmo padrão quando envia `POST` e `DELETE` pedidos para o web API, utilizando MSAL para obter o token de acesso da cache.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Por fim, crie e execute ambas as aplicações. Inscrever-se e iniciar sessão e crie tarefas para o utilizador com sessão iniciada. Terminar sessão e inicie sessão como outro utilizador. Crie tarefas para esse utilizador. Repare como as tarefas são armazenadas por utilizador na API, porque a API extrai a identidade do utilizador a partir do token recebe. Experimente também reproduzir com os âmbitos. Remova a permissão "escrita" e, em seguida, tente adicionar uma tarefa. Basta certificar-se terminar sessão sempre que alterar o âmbito.

