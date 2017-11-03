---
title: "Adicionar início de sessão a uma API utilizando o ponto final v 2.0 do Azure AD da web de MVC do .NET | Microsoft Docs"
description: Como criar uma Api de Web de MVC do .NET que aceita tokens a partir de ambos os Account Microsoft pessoal e contas profissionais ou escolares.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: bb332196aef59a497a1f80b67ab5c1eeef3dee0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-an-mvc-web-api"></a>Proteger uma API web MVC
Com o Azure Active Directory o ponto final v 2.0, pode proteger uma Web API utilizando [OAuth 2.0](active-directory-v2-protocols.md) aceder tokens, permitindo que os utilizadores com ambos os conta Microsoft pessoal e profissional ou escolar contas para acesso com segurança a API Web.

> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
>
>

Nas ASP.NET web APIs, isto é possível utilizar o middleware OWIN da Microsoft incluído no .NET Framework 4.5.  Aqui iremos utilizar OWIN para criar uma API Web MVC de "Lista de tarefas" que permite aos clientes criar e ler tarefas da lista de tarefas de um utilizador.  A API web irá validar que os pedidos recebidos contém um token de acesso válido e rejeitar quaisquer pedidos que não são transmitidas validação numa rota protegida.  Este exemplo foi criado com o Visual Studio 2015.

## <a name="download"></a>Transferência
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Para acompanhar, pode [transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

A aplicação esqueleto inclui o todo o código automático de uma API simple, mas está em falta tudo relacionadas com identidade. Se não quiser acompanhar, em vez disso, pode clonar ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registar uma aplicação
Criar uma nova aplicação em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga estas [detalhadas passos](active-directory-v2-app-registration.md).  Certifique-se de que:

* Copie o **Id da aplicação** atribuída à aplicação, irá precisar das mesmas em breve.

Esta solução do visual studio também contém um "TodoListClient", que é uma simple aplicação WPF.  O TodoListClient é utilizado para demonstrar como um utilizador inicia sessão e a forma como um cliente pode emitir pedidos para a API Web.  Neste caso, o TodoListClient e o TodoListService são representados pela mesma aplicação.  Para configurar o TodoListClient, deve também:

* Adicionar o **Mobile** plataforma para a sua aplicação.

## <a name="install-owin"></a>Instalar OWIN
Agora que já registou uma aplicação, terá de configurar a sua aplicação para comunicar com o ponto final v 2.0 para validar pedidos de entrada & tokens.

* Para começar, abra a solução e adicionar os pacotes de NuGet middleware OWIN para o projeto de TodoListService utilizando a consola do Gestor de pacotes.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurar a autenticação do OAuth
* Adicionar uma classe de Startup da OWIN ao projeto denominado TodoListService `Startup.cs`.  Clique direito no projeto--> **adicionar** --> **Novo Item** --> procure "OWIN".  O middleware da OWIN vai invocar o método `Configuration(…)` quando a aplicação for iniciada.
* Alterar a declaração de classe para `public partial class Startup` -iremos já tiver implementado parte desta classe para si no outro ficheiro.  No `Configuration(…)` método, crie uma chamada para ConfgureAuth(...) para configurar a autenticação para a sua aplicação web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(…)` método, o que irá configurar a API Web para aceitar tokens a partir do ponto final v 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Agora, pode utilizar `[Authorize]` atributos para proteger os seus controladores e ações com autenticação de portador do OAuth 2.0.  OptionalFieldAttribute a `Controllers\TodoListController.cs` classe com uma etiqueta de autorizar.  Isto irá forçar o utilizador iniciar sessão antes de aceder a essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Quando um autor da chamada autorizado com êxito invoca um do `TodoListController` APIs, a ação poderá ter acesso a informações sobre o autor da chamada.  OWIN fornece acesso a afirmações dentro do token de portador através de `ClaimsPrincipal` objeto.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Por fim, abra o `web.config` ficheiro na raiz do projeto TodoListService e introduza os valores de configuração no `<appSettings>` secção.
  * O `ida:Audience` é o **Id da aplicação** da aplicação que introduziu no portal.

## <a name="configure-the-client-app"></a>Configurar a aplicação de cliente
Antes de poder ver o serviço de lista de tarefas em ação, terá de configurar o cliente de lista de tarefas para que possa obter os tokens do ponto final v 2.0 e efetuar chamadas para o serviço.

* No projeto TodoListClient, abra `App.config` e introduza os valores de configuração no `<appSettings>` secção.
  * O `ida:ClientId` Id da aplicação que copiou do portal.

Por fim, apagar, criar e executar cada projeto!  Tem agora uma API de Web de MVC do .NET que aceita tokens a partir de ambas as contas pessoais da Microsoft e contas profissionais ou escolares.  Inicie sessão no TodoListClient e chamar web api para adicionar tarefas à lista de tarefas do utilizador.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), ou pode cloná-la a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Passos seguintes
Agora pode mover para tópicos adicionais.  Poderá querer experimentar:

[Chamar uma API Web a partir de uma aplicação Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Para obter recursos adicionais, consulte:

* [O guia para programadores de v 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Etiqueta de StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.
