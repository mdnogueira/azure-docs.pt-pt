---
title: "Azure Active Directory B2C: Proteger a sua serviços RESTful, utilizando a autenticação básica de HTTP"
description: "Proteger as trocas de afirmações de REST API personalizadas no seu Azure AD B2C, utilizando a autenticação básica de HTTP"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 641e0cc691eae77ef0480e5743d85e020cd8d354
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Proteger a sua serviços RESTful, utilizando a autenticação básica de HTTP
Num [relacionado do Azure AD B2C artigo](active-directory-b2c-custom-rest-api-netfw.md), criar um serviço RESTful (web API) que se integra com o Azure Active Directory B2C percursos de utilizador (Azure AD B2C) sem autenticação. 

Neste artigo, adicionar autenticação básica de HTTP ao seu serviço RESTful, para que apenas verificar os utilizadores, incluindo B2C, pode aceder à sua API. Com a autenticação básica de HTTP, defina as credenciais de utilizador (ID de aplicação e o segredo de aplicação) na sua política personalizada. 

Para obter mais informações, consulte [a autenticação básica na API web do ASP.NET](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Pré-requisitos
Concluir os passos a [integrar o API de REST afirmações trocas da sua viagem do Azure AD B2C utilizador](active-directory-b2c-custom-rest-api-netfw.md) artigo.

## <a name="step-1-add-authentication-support"></a>Passo 1: Adicionar suporte de autenticação

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Passo 1.1: Adicionar as definições da aplicação ao ficheiro do seu projeto Web. config
1. Abra o projeto do Visual Studio que criou anteriormente. 

2. Adicione as seguintes definições de aplicação para o ficheiro Web. config no `appSettings` elemento:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Criar uma palavra-passe e, em seguida, defina o `WebApp:ClientSecret` valor.

    Para gerar uma palavra-passe complexa, execute o seguinte código do PowerShell. Pode utilizar qualquer valor arbitrário.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Passo 1.2: Instalar o bibliotecas OWIN
Para começar, adicione os pacotes de NuGet middleware OWIN para o projeto utilizando a consola do Gestor de pacotes do Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Passo 1.3: Adicionar uma classe de middleware de autenticação
Adicionar o `ClientAuthMiddleware.cs` classe sob o *App_Start* pasta. Para tal:

1. Clique com botão direito do *App_Start* pasta, selecione **adicionar**e, em seguida, selecione **classe**.

   ![Adicionar classe de ClientAuthMiddleware.cs na pasta App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. No **nome** caixa, escreva **ClientAuthMiddleware.cs**.

   ![Criar nova classe de c#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Abra o *App_Start\ClientAuthMiddleware.cs* de ficheiros e substitua o ficheiro de conteúdo com o código seguinte:

    ```C#
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Passo 1.4: Adicionar uma classe de startup da OWIN
Adicionar uma classe de startup da OWIN denominada `Startup.cs` para a API. Para tal:
1. Clique com o botão direito no projeto, selecione **adicionar** > **Novo Item**e, em seguida, procure **OWIN**.

   ![Adicionar uma classe de startup da OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Abra o *Startup.cs* de ficheiros e substitua o ficheiro de conteúdo com o código seguinte:

    ```C#
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Passo 1.5: Proteger a classe de API de identidade
Abrir Controllers\IdentityController.cs e adicione o `[Authorize]` etiqueta para a classe de controlador. Esta etiqueta restringe o acesso para o controlador para os utilizadores que cumpre o requisito de autorização.

![Adicionar a etiqueta de autorizar o controlador](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Passo 2: Publicar no Azure
Para publicar o seu projeto, no Explorador de soluções, clique com botão direito do **Contoso.AADB2C.API** projeto e, em seguida, selecione **publicar**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Passo 3: Adicionar o segredo de aplicações e o ID da aplicação de serviços RESTful ao Azure AD B2C
Depois do serviço RESTful está protegido pelo ID de cliente (nome de utilizador) e o segredo, tem de armazenar as credenciais no seu inquilino do Azure AD B2C. A política personalizada fornece as credenciais quando ele invoca sua serviços RESTful. 

### <a name="step-31-add-a-restful-services-client-id"></a>Passo 3.1: Adicionar um ID de cliente de serviços RESTful
1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **identidade experiência Framework**.


2. Selecione **política chaves** para ver as chaves que estão disponíveis no seu inquilino.

3. Selecione **Adicionar**.

4. Para **opções**, selecione **Manual**.

5. Para **nome**, tipo **B2cRestClientId**.  
    O prefixo *B2C_1A_* podem ser adicionados automaticamente.

6. No **segredo** box, introduza o ID da aplicação que definiu anteriormente.

7. Para **utilização da chave**, selecione **segredo**.

8. Selecione **Criar**.

9. Confirme que criou o `B2C_1A_B2cRestClientId` chave.

### <a name="step-32-add-a-restful-services-client-secret"></a>Passo 3.2: Adicionar um segredo do cliente de serviços RESTful
1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **identidade experiência Framework**.

2. Selecione **política chaves** para ver as chaves disponíveis no seu inquilino.

3. Selecione **Adicionar**.

4. Para **opções**, selecione **Manual**.

5. Para **nome**, tipo **B2cRestClientSecret**.  
    O prefixo *B2C_1A_* podem ser adicionados automaticamente.

6. No **segredo** box, introduza o segredo de aplicação que definiu anteriormente.

7. Para **utilização da chave**, selecione **segredo**.

8. Selecione **Criar**.

9. Confirme que criou o `B2C_1A_B2cRestClientSecret` chave.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Passo 4: Altere o perfil técnico para suportar a autenticação básica na sua política de extensão
1. No seu diretório de trabalho, abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml).

2. Procure o `<TechnicalProfile>` nó que inclui `Id="REST-API-SignUp"`.

3. Localize o `<Metadata>` elemento.

4. Alterar o *AuthenticationType* para *básico*, da seguinte forma:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Imediatamente após o fecho `<Metadata>` elemento, adicione o seguinte fragmento XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Depois de adicionar o fragmento, o seu perfil técnica deverá ter um aspeto semelhante ao seguinte código XML:
    
    ![Adicionar elementos XML de autenticação básica](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passo 5: Carregar a política para o seu inquilino

1. No [portal do Azure](https://portal.azure.com), mude para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, abra **do Azure AD B2C**.

2. Selecione **identidade experiência Framework**.

3. Abra **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e, em seguida, certifique-se de que transfere a validação.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passo 6: Testar a política personalizada utilizando o executar agora
1. Abra **definições do Azure AD B2C**e, em seguida, selecione **identidade experiência Framework**.

    >[!NOTE]
    >Executar agora requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Abra **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) entidade confiadora que carregado e, em seguida, selecione **executar agora**.

3. O processo de teste, escrevendo **teste** no **o nome fornecido** caixa.  
    O Azure AD B2C apresenta uma mensagem de erro na parte superior da janela.

    ![Testar a sua identidade API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. No **o nome fornecido** caixa, escreva um nome (diferente de "teste").  
    O Azure AD B2C se inscreve o utilizador e, em seguida, envia um número de loyalty à sua aplicação. Tenha em atenção o número neste exemplo:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Transferir os ficheiros de política concluída e o código
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Pode transferir o código de conclusão de [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Passos seguintes
* [Utilizar certificados de cliente para proteger a sua API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

