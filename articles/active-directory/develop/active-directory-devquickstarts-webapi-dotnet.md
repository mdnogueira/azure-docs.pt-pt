---
title: "Web AD .NET do Azure API introdução | Microsoft Docs"
description: "Como criar uma API que se integra com o Azure AD para autenticação e autorização da web de MVC do .NET."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f44d75f45073a5d9aa9b1863ed227aba4efcf785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>Ajudar a proteger uma API web utilizando tokens de portador do Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se estiver a criar uma aplicação que fornece acesso a recursos protegidos, terá de saber como impedir unwarranted acesso a esses recursos.
Azure Active Directory (Azure AD) faz com que esta simples e fácil de ajudar a proteger uma API web utilizando o acesso de portador do OAuth 2.0 tokens com apenas alguns linhas de código.

Nas web apps do ASP.NET, pode realizar esta proteção utilizando a implementação Microsoft do middleware OWIN Comunidade incluída no .NET Framework 4.5. Aqui vamos utilizar OWIN para criar uma API web de "Lista de tarefas" que:

* Designa que as APIs são protegidos.
* Valida que as chamadas de web API contém um token de acesso válido.

Para criar a para fazer lista API, terá primeiro de:

1. Registar uma aplicação com o Azure AD.
2. Configure a aplicação para utilizar o pipeline de autenticação da OWIN.
3. Configure uma aplicação de cliente para chamar a API web.

Para começar, [transferir a estrutura de aplicação](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Cada uma é uma solução do Visual Studio 2013. Também precisa de um inquilino do Azure AD na qual pode registar a sua aplicação. Se ainda não tiver uma, [saber como obter um](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Passo 1: Registar uma aplicação com o Azure AD
Para ajudar a proteger a aplicação, terá primeiro de criar uma aplicação no seu inquilino e fornecer do Azure AD com alguns peças chave de informações.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Na barra superior, clique em sua conta. No **diretório** lista, escolha o inquilino do Azure AD, onde é necessário registar a sua aplicação.

3. Clique em **mais serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.

4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.

5. Siga as instruções e crie um novo **aplicação Web e/ou Web API**.
  * **Nome** descreve a aplicação aos utilizadores. Introduza **para listar serviço**.
  * **Uri de redirecionamento** é uma combinação de esquema e a cadeia que utiliza o Azure AD para devolver qualquer tokens que a aplicação pediu. Introduza `https://localhost:44321/` para este valor.

6. Do **definições** -> **propriedades** página para a sua aplicação, atualize o URI de ID de aplicação. Introduza um identificador de inquilino específico. Por exemplo, introduza `https://contoso.onmicrosoft.com/TodoListService`.

7. Guarde a configuração. Deixe o portal aberta, porque também terá de registar a aplicação de cliente em breve.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Passo 2: Configurar a aplicação para utilizar o pipeline de autenticação da OWIN
Para validar pedidos de entrada e de tokens, terá de configurar a sua aplicação para comunicar com o Azure AD.

1. Para começar, abra a solução e adicionar os pacotes de NuGet middleware OWIN para o projeto de TodoListService utilizando a consola do Gestor de pacotes.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adicionar uma classe de Startup da OWIN ao projeto denominado TodoListService `Startup.cs`.  Clique com o botão direito no projeto, selecione **adicionar** > **Novo Item**e, em seguida, procure **OWIN**. O middleware da OWIN vai invocar o método `Configuration(…)` quando a aplicação for iniciada.

3. Alterar a declaração de classe para `public partial class Startup`. Iremos já tiver implementado parte desta classe para si no outro ficheiro. No `Configuration(…)` método, crie uma chamada para `ConfgureAuth(…)` para configurar a autenticação para a sua aplicação web.

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Abra o ficheiro `App_Start\Startup.Auth.cs` e implementar o `ConfigureAuth(…)` método. Os parâmetros que fornecem nos `WindowsAzureActiveDirectoryBearerAuthenticationOptions` irá servir como coordenadas para a sua aplicação para comunicar com o Azure AD.

    ```C#
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Agora, pode utilizar `[Authorize]` atributos para o ajudar a proteger os seus controladores e ações com a autenticação de portador do Token Web JSON (JWT). OptionalFieldAttribute a `Controllers\TodoListController.cs` classe com uma etiqueta de autorizar. Isto irá forçar o utilizador iniciar sessão antes de aceder a essa página.

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Quando um autor da chamada autorizado com êxito invoca um do `TodoListController` APIs, a ação poderá ter acesso a informações sobre o autor da chamada. OWIN fornece acesso a afirmações dentro do token de portador através de `ClaimsPrincpal` objeto.  

6. Um requisito comum das APIs Web é validar os “âmbitos” presentes no token. Isto garante que o utilizador consentiu as permissões necessárias para aceder a fazer lista de serviços.

    ```C#
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Abra o `web.config` ficheiro na raiz do projeto TodoListService e introduza os valores de configuração no `<appSettings>` secção.
  * `ida:Tenant`é o nome do inquilino do Azure AD – por exemplo, contoso.onmicrosoft.com.
  * `ida:Audience`é o URI de ID de aplicação da aplicação que introduziu no portal do Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Passo 3: Configurar uma aplicação cliente e executar o serviço
Antes de poder ver a fazer lista serviços em ação, terá de configurar o cliente de lista de tarefas para que possa obter os tokens do Azure AD e efetuar chamadas para o serviço.

1. Volte à [portal do Azure](https://portal.azure.com).

2. Crie uma nova aplicação no seu inquilino do Azure AD e selecione **aplicação cliente nativa** na linha de resultante.
  * **Nome** descreve a aplicação aos utilizadores.
  * Introduza `http://TodoListClient/` para o **Uri de redirecionamento** valor.

3. Depois de concluir o registo, o Azure AD atribui um ID de aplicação único para a sua aplicação. Terá este valor nos passos, por isso, copie-o partir da página da aplicação.

4. Do **definições** página, selecione **permissões obrigatórias**e, em seguida, selecione **adicionar**. Localize e selecione a fazer lista de serviços, adicione o **acesso TodoListService** permissão em **permissões delegadas**e, em seguida, clique em **feito**.

5. No Visual Studio, abra `App.config` no TodoListClient projeto e, em seguida, introduza os valores de configuração no `<appSettings>` secção.

  * `ida:Tenant`é o nome do inquilino do Azure AD – por exemplo, contoso.onmicrosoft.com.
  * `ida:ClientId`é o ID da aplicação que copiou do portal do Azure.
  * `todo:TodoListResourceId`é o URI de ID de aplicação da aplicação para o fazer de serviço de lista que introduziu no portal do Azure.

## <a name="next-steps"></a>Passos seguintes
Por fim, apagar, criar e executar cada projeto. Se ainda não o fez, agora é o tempo para criar um novo utilizador no seu inquilino com um *. c o m domínio. Iniciar sessão para o cliente de lista de tarefas com esse utilizador e adicionar algumas tarefas à lista de tarefas do utilizador.

Para referência, o exemplo concluído (sem os valores de configuração) está disponível no [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Agora pode passar para os cenários mais de identidade.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
