<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Como criar uma API Web do .NET utilizando o Azure Active Directory B2C, protegida pela utilização de tokens de acesso de autenticação do OAuth 2.0 ."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# Azure Active Directory B2C: criar uma API Web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Azure Active Directory (Azure AD) B2C, pode proteger uma API web utilizando tokens de acesso de OAuth 2.0. Estes tokens permitem que as aplicações de cliente que utilizam o Azure AD B2C se autentiquem na API. Este artigo mostra-lhe como criar uma API de "lista de tarefas" do Controlador de Vista de Modelo (MVC) do .NET. que permite aos utilizadores realizar tarefas CRUD. A API Web é protegida ao utilizar o Azure AD B2C e só permite que os utilizadores autenticados façam a gestão da respetiva lista de tarefas.

## Criar um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## Criar uma aplicação

Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona ao Azure AD as informações de que necessita para comunicar de forma segura com a sua aplicação. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

- Incluir uma **aplicação web** ou uma **API Web ** na aplicação.
- Utilizar o **Uniform Resource Identifier de redirecionamento ** `https://localhost:44316/` para a aplicação web. Esta é a localização predefinida da aplicação Web cliente para este exemplo de código.
- Copiar a **ID da aplicação** atribuída à aplicação. Precisará dela mais tarde.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). O cliente neste exemplo de código contém três experiências de identidade: inscrever-se, iniciar sessão e editar perfil. Precisa de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando criar as três políticas, não se esqueça de:

- Escolher a **Inscrição de ID de utilizador** ou **Inscrição de email ** no painel de fornecedores de identidade.
- Escolher o **Nome a apresentar** e outros atributos de inscrição na sua política de inscrição.
- Escolher as afirmações **Nome a apresentar** e **ID de objeto** como afirmações de aplicação em cada política. Também pode escolher outras afirmações.
- Copiar o **Nome** de cada política depois de a criar. Precisará destes nomes de políticas mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a sua aplicação.

## Transferir o código

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para criar a amostra à medida que avança, pode [transferir o projeto de estrutura em ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

A aplicação concluída está também [disponível como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou no `complete` ramo do mesmo repositório.

Depois de transferir o código de exemplo, abra o ficheiro de .sln do Visual Studio para começar a utilizar. O ficheiro de solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp` é uma aplicação Web de MVC com a qual o utilizador interage. `TaskService` é API Web back-end da aplicação que armazena a lista de tarefas de cada utilizador.

## Configurar a aplicação Web da tarefa

Quando um utilizador interage com `TaskWebApp`, o cliente envia pedidos ao Azure AD e recebe de volta tokens que podem ser utilizados para chamar a API Web `TaskService`. Para iniciar o utilizador e obter tokens, precisa de fornecer a `TaskWebApp` algumas informações sobre a sua aplicação. No projeto `TaskWebApp`, abra o ficheiro `web.config` na raiz do projeto e substitua os valores na secção `<appSettings>`.  Pode deixar os valores `AadInstance`, `RedirectUri` e valores `TaskServiceUrl` como estão.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Este artigo não abrange a criação do cliente `TaskWebApp`.  Para saber como criar uma aplicação Web utilizando Azure AD B2C, veja [o nosso tutorial da aplicação Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Proteger a API

Quando tiver um cliente que chama a API em nome dos utilizadores, pode proteger o `TaskService` utilizando os tokens de portador do OAuth 2.0. A sua API pode aceitar e validar os tokens utilizando a biblioteca OWIN (Interface de Web aberto da Open Web Interface for .NET) da Microsoft.

### Instalar OWIN
Comece por instalar o pipeline de autenticação OAuth da OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Introduza os seus detalhes do B2C
Abra o ficheiro `web.config` na raiz do projeto `TaskService` e substitua os valores na secção `<appSettings>`. Estes valores serão utilizados em toda a biblioteca de API e OWIN.  Pode deixar o valor `AadInstance` inalterado.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### Adicionar uma classe de startup da OWIN
Adicionar uma classe de startup da OWIN para o projeto `TaskService` denominado `Startup.cs`.  Com o botão direito no projeto, selecione **Adicionar** e **Novo Item**, e, em seguida, pesquise OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### Configurar a autenticação OAuth 2.0
Abra o ficheiro `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### Proteger o controlador de tarefa
Depois de configurar aplicação para utilizar a autenticação OAuth 2.0, pode proteger a sua API Web adicionando uma etiqueta `[Authorize]` ao controlador de tarefas. Este é o controlador onde ocorre a manipulação de lista de ações pendentes, pelo que deve proteger todo o controlador a nível de classe. Também pode adicionar a etiqueta `[Authorize]` para ações individuais para obter um controlo mais preciso.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### Obter informações de utilizador a partir do token
`TasksController` armazena as tarefas numa base de dados onde cada tarefa tem um utilizador associado que é o “proprietário” da tarefa. O proprietário é identificado pela **ID de objeto** do utilizador. (É por isso que é necessário adicionar a ID de objeto como uma afirmação de aplicação em todas as suas políticas.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## Execute a aplicação de exemplo

Por último, crie e execute `TaskWebApp` e `TaskService`. Inscreva-se na aplicação utilizando um endereço de email ou nome de utilizador. Crie algumas tarefas na lista de ações a fazer do utilizador e repare como persistem na API mesmo depois de parar e reiniciar o cliente.

## Editar as suas políticas

Depois de proteger uma API utilizando o Azure AD B2C, pode experimentar com as políticas da sua aplicação e ver os efeitos (ou a sua falta) na API. Pode manipular afirmações de aplicação nas políticas e alterar as informações de utilizador que estão disponíveis na API Web. Qualquer afirmações que adicionar estarão disponíveis para a API Web de MVC do .NET no objeto `ClaimsPrincipal`, como descrito anteriormente neste artigo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Aug16_HO1-->


