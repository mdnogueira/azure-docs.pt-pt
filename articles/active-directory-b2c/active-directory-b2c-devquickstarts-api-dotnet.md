---
title: Azure AD B2C | Microsoft Docs
description: "Como utilizar o Azure Active Directory B2C para criar uma API Web .NET, protegida com tokens de acesso de autenticação do OAuth 2.0."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 3dd207805c1a8f53c6cc74da08cc9378609581c5
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: criar uma API Web do .NET

Com o Azure Active Directory (Azure AD) B2C, pode proteger uma API web utilizando tokens de acesso de OAuth 2.0. Estes tokens permitem que as aplicações cliente se autentiquem na API. Este artigo mostra-lhe como criar uma API de "lista de tarefas" de MVC .NET que permite aos utilizadores da sua aplicação cliente realizar tarefas CRUD. A API Web é protegida ao utilizar o Azure AD B2C e só permite que os utilizadores autenticados façam a gestão da respetiva lista de tarefas.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

> [!NOTE]
> A aplicação cliente e a API Web têm de utilizar o mesmo diretório do Azure AD B2C.
>

## <a name="create-a-web-api"></a>Criar uma API Web

Em seguida, tem de criar uma aplicação API Web no seu diretório do B2C. Isto proporciona ao Azure AD as informações de que necessita para comunicar de forma segura com a sua aplicação. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

* Incluir uma **aplicação web** ou uma **API Web** na aplicação.
* Utilize o **URI de Redirecionamento**`https://localhost:44332/` da aplicação Web. Esta é a localização predefinida da aplicação Web cliente para este exemplo de código.
* Copiar a **ID da Aplicação** atribuída à aplicação. Precisará dela mais tarde.
* Introduza um identificador de aplicação em **URI de ID de Aplicação**.
* Adicione permissões através do menu **Âmbitos publicados**.

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Terá de criar uma política para comunicar com o Azure AD B2C. Recomendamos utilizar a política de inscrição/início de sessão combinada, conforme descrito no [artigo de referência de políticas](active-directory-b2c-reference-policies.md). Quando criar a política, certifique-se de que:

* Escolhe o **Nome a apresentar** e outros atributos de inscrição na política.
* Escolher as afirmações **Nome a apresentar** e **ID de objeto** como afirmações de aplicação em cada política. Também pode escolher outras afirmações.
* Copiar o **Nome** de cada política depois de a criar. Precisará do nome da política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar a política com êxito, está pronto para criar a sua aplicação.

## <a name="download-the-code"></a>Transferir o código

O código deste tutorial é mantido no [GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi). Pode clonar o exemplo ao executar:

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

Depois de transferir o código de exemplo, abra o ficheiro de .sln do Visual Studio para começar a utilizar. O ficheiro de solução contém dois projetos: `TaskWebApp` e `TaskService`. A `TaskWebApp` é uma aplicação Web de MVC com a qual o utilizador interage. O `TaskService` é uma API Web de back-end da aplicação que armazena a lista de tarefas de cada utilizador. Este artigo só cobre a aplicação `TaskService`. Para saber como utilizar o Azure AD B2C para criar `TaskWebApp`, veja o [nosso tutorial de aplicações Web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Atualizar a configuração do Azure AD B2C

O nosso exemplo está configurado para utilizar as políticas e o ID de cliente do nosso inquilino de demonstração. Se quiser utilizar o seu próprio inquilino, terá de fazer o seguinte:

1. Abra `web.config` no projeto `TaskService` e substitua os valores de:
    * `ida:Tenant` pelo nome do seu inquilino
    * `ida:ClientId` pelo ID da sua aplicação da API Web
    * `ida:SignUpSignInPolicyId` pelo nome da sua política de “Inscrição ou Início de Sessão”

2. Abra `web.config` no projeto `TaskWebApp` e substitua os valores de:
    * `ida:Tenant` pelo nome do seu inquilino
    * `ida:ClientId` pelo ID da sua aplicação Web
    * `ida:ClientSecret` pela chave de segredo da sua aplicação Web
    * `ida:SignUpSignInPolicyId` pelo nome da sua política de “Inscrição ou Início de Sessão”
    * `ida:EditProfilePolicyId` pelo nome da sua política de “Editar Perfil”
    * `ida:ResetPasswordPolicyId` pelo nome da sua política de “Repor Palavras-Passe”


## <a name="secure-the-api"></a>Proteger a API

Quando tiver um cliente que chama a API, pode utilizar tokens de portador do OAuth 2.0 para proteger a sua API (por exemplo, `TaskService`). Isto garante que cada pedido para a API só será válido se tiver um token de portador. A sua API pode aceitar e validar os tokens de portador através da biblioteca OWIN (Open Web Interface para .NET) da Microsoft.

### <a name="install-owin"></a>Instalar OWIN

Comece por instalar o pipeline de autenticação OAuth da OWIN com a Consola do Gestor de Pacotes do Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Esta ação instala o middleware da OWIN, que vai aceitar e validar os tokens de portador.

### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de startup da OWIN

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

### <a name="configure-oauth-20-authentication"></a>Configurar a autenticação OAuth 2.0

Abra o ficheiro `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`. Por exemplo, poderá ter o aspeto seguinte:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Proteger o controlador de tarefa

Depois de configurar aplicação para utilizar a autenticação OAuth 2.0, pode proteger a sua API Web adicionando uma etiqueta `[Authorize]` ao controlador de tarefas. Este é o controlador onde ocorre a manipulação de lista de ações pendentes, pelo que deve proteger todo o controlador a nível de classe. Também pode adicionar a etiqueta `[Authorize]` para ações individuais para obter um controlo mais preciso.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obter informações de utilizador a partir do token

O `TasksController` armazena as tarefas numa base de dados onde cada tarefa tem um utilizador associado que é o “proprietário” da tarefa. O proprietário é identificado pela **ID de objeto** do utilizador. (É por isso que é necessário adicionar a ID de objeto como uma afirmação de aplicação em todas as suas políticas.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Validar as permissões no token

Um requisito comum das APIs Web é validar os “âmbitos” presentes no token. Isto garante que o utilizador consentiu as permissões necessárias para aceder ao serviço da lista de tarefas.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Por último, crie e execute `TaskWebApp` e `TaskService`. Crie algumas tarefas na lista de ações a fazer do utilizador e repare como persistem na API mesmo depois de parar e reiniciar o cliente.

## <a name="edit-your-policies"></a>Editar as suas políticas

Depois de proteger uma API com o Azure AD B2C, pode experimentar com a sua política de Inscrição/Início de Sessão e ver os efeitos (ou falta deles) na API. Pode manipular afirmações de aplicação nas políticas e alterar as informações de utilizador que estão disponíveis na API Web. Qualquer afirmações que adicionar estarão disponíveis para a API Web de MVC do .NET no objeto `ClaimsPrincipal`, como descrito anteriormente neste artigo.

