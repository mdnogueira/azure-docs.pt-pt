---
title: "O Azure Active Directory B2C: Integrar trocas de afirmação de REST API da sua viagem do Azure AD B2C utilizador como a validação da intervenção do utilizador"
description: "Integre trocas de afirmação de REST API da sua viagem do Azure AD B2C utilizador como a validação da intervenção do utilizador."
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: e9a5b6ffdf2a2c30ae1bcb2bd8f91adb12f35266
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrar o REST API trocas de afirmações da sua viagem do Azure AD B2C utilizador como a validação da intervenção do utilizador
Com o Framework de experiência de identidade, subjacente que o Azure Active Directory B2C (Azure AD B2C), pode integrar com uma API RESTful em journey um utilizador. Esta explicação passo a passo, irá aprender como o Azure AD B2C interage com os serviços RESTful do .NET Framework (web API).

## <a name="introduction"></a>Introdução
Utilizando o Azure AD B2C, pode adicionar a seus próprios lógica de negócio para journey um utilizador ao chamar o seu próprio serviço RESTful. A estrutura de experiência de identidade envia dados para o serviço RESTful num *afirmações de entrada* coleção e recebe dados de reativação a partir do RESTful num *afirmações de saída* coleção. Com a integração de serviço RESTful, pode:

* **Validar dados de entrada do utilizador**: Esta ação impede que dados mal formados persistência com o Azure AD. Se o valor do utilizador não é válido, o seu serviço RESTful devolve uma mensagem de erro que dá instruções ao utilizador para fornecer uma entrada. Por exemplo, pode verificar se o endereço de e-mail fornecido pelo utilizador existe na base de dados do seu cliente.
* **Substituir afirmações de entrada**: por exemplo, se um utilizador introduz o nome próprio na todo em minúsculas ou maiúsculas todas as letras, pode formatar o nome com a primeira letra capitalized.
* **Enriqueça a dados do utilizador através da integração mais com aplicações de linha de negócio empresariais**: serviço Your RESTful pode receber o endereço de correio eletrónico do utilizador, consultar base de dados do cliente e devolver o número do utilizador loyalty ao Azure AD B2C. O retorno afirmações podem ser armazenados na conta de utilizador do Azure AD, avaliada as próximas *etapas da orquestração*, ou parte do token de acesso.
* **Execute a lógica de negócio personalizadas**: pode enviar notificações push, atualizar as bases de dados empresariais, executar um processo de migração de utilizador, gerir as permissões, as bases de dados de auditoria e efetuar outras ações.

Pode estruturar a integração com os serviços RESTful das seguintes formas:

* **Perfil de validação técnica**: chamada para o serviço RESTful acontece no perfil de técnicas de validação do perfil técnico especificado. O perfil de técnicas de validação valida os dados fornecidos pelo utilizador antes do journey utilizador move reencaminhar. Com o perfil de técnicas de validação, pode:
   * Envie afirmações de entrada.
   * Afirmações de entrada de validação e gerar mensagens de erro personalizadas.
   * Envie afirmações de saída anterior.

* **Afirmações exchange**: esta estrutura é semelhante ao perfil técnica de validação, mas ocorre dentro de um passo de orquestração. Esta definição está limitada a:
   * Envie afirmações de entrada.
   * Envie afirmações de saída anterior.

## <a name="restful-walkthrough"></a>Instruções rESTful
Nestas instruções, desenvolva uma .NET Framework API web que valida a entrada de utilizador e fornece um número de loyalty do utilizador. Por exemplo, a aplicação pode conceder acesso a *platinum vantagens* com base no número loyalty.

Descrição geral:
* Desenvolva o serviço RESTful (.NET Framework API web).
* Utilize o serviço RESTful journey o utilizador.
* Enviar afirmações de entrada e lê-los no seu código.
* Valide o nome próprio do utilizador.
* Envie novamente um número de loyalty. 
* Adicione o número de loyalty para um Token Web JSON (JWT).

## <a name="prerequisites"></a>Pré-requisitos
Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

## <a name="step-1-create-an-aspnet-web-api"></a>Passo 1: Criar uma API web do ASP.NET

1. No Visual Studio, crie um projeto selecionando **ficheiro** > **novo** > **projeto**.

2. No **novo projeto** janela, selecione **Visual c#** > **Web** > **uma aplicação Web ASP.NET (.NET Framework)**.

3. No **nome** caixa, escreva um nome para a aplicação (por exemplo, *Contoso.AADB2C.API*) e, em seguida, selecione **OK**.

    ![Criar novo projeto do visual studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. No **nova aplicação Web do ASP.NET** janela, selecione um **Web API** ou **aplicação API do Azure** modelo.

    ![Selecione o modelo de API web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Certifique-se de que a autenticação está definida como **sem autenticação**.

6. Selecione **OK** para criar o projeto.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Passo 2: Preparar o ponto final de REST API

### <a name="step-21-add-data-models"></a>Passo 2.1: Adicionar modelos de dados
Os modelos representam as afirmações de entrada e saída de dados no seu serviço RESTful afirmações. O código lê os dados de entrada ao anular a serialização do modelo de afirmações de entrada de uma cadeia JSON para um objeto de c# (o seu modelo). A API web do ASP.NET deserializes automaticamente o modelo de afirmações de saída para JSON e, em seguida, escreve os dados serializados o corpo da mensagem de resposta HTTP. 

Crie um modelo que representa as afirmações de entrada da seguinte forma:

1. Se o Explorador de soluções ainda não estiver aberto, selecione **vista** > **Explorador de soluções**. 
2. No Explorador de soluções, clique com botão direito do **modelos** pasta, selecione **adicionar**e, em seguida, selecione **classe**.

    ![Adicionar o modelo](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Designar a classe `InputClaimsModel`e, em seguida, adicione as seguintes propriedades para o `InputClaimsModel` classe:

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Criar um novo modelo `OutputClaimsModel`e, em seguida, adicione as seguintes propriedades para o `OutputClaimsModel` classe:

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Criar um modelo mais `B2CResponseContent`, que utilizar para gerar mensagens de erro de validação de entrada. Adicione as seguintes propriedades para o `B2CResponseContent` classe, forneça as referências em falta e, em seguida, guarde o ficheiro:

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Passo 2.2: Adicionar um controlador
Na API, web um _controlador_ é um objeto que processa os pedidos HTTP. Devolve o controlador de afirmações de saída ou, se o nome próprio não for válido, emite uma mensagem de erro de HTTP de conflito.

1. No Explorador de soluções, clique com botão direito do **controladores** pasta, selecione **adicionar**e, em seguida, selecione **controlador**.

    ![Adicionar novo controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. No **adicionar andaime** janela, selecione **Web API controlador - vazio**e, em seguida, selecione **adicionar**.

    ![Selecione Web API 2 controlador - vazio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. No **Adicionar controlador** janela, nome do controlador **IdentityController**e, em seguida, selecione **adicionar**.

    ![Escreva o nome de controlador](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    O andaime cria um ficheiro denominado *IdentityController.cs* no *controladores* pasta.

4. Se o *IdentityController.cs* ficheiro já não está aberto, faça duplo clique e, em seguida, substitua o código no ficheiro com o seguinte código:

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Passo 3: Publicar o projeto no Azure
1. No Explorador de soluções, clique com botão direito do **Contoso.AADB2C.API** projeto e, em seguida, selecione **publicar**.

    ![Publicar no serviço de aplicações do Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. No **publicar** janela, selecione **App Service do Microsoft Azure**e, em seguida, selecione **publicar**.

    ![Criar novo serviço de aplicações do Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    O **criar App Service** é aberta a janela. Aqui, vai criar todos os recursos do Azure necessários para executar a aplicação web do ASP.NET no Azure.

    > [!NOTE]
    >Para obter mais informações sobre a publicação, consulte [criar uma aplicação web ASP.NET no Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. No **nome da aplicação Web** caixa, escreva um nome de aplicação único (carateres válidos são. a-z, 0-9 e hífenes (-). O URL da aplicação web é http://<app_name>.azurewebsites.NET, onde *APP_NAME>.azurewebsites.NET* é o nome da sua aplicação web. Também pode aceitar o nome gerado automaticamente, que já é exclusivo.

    ![Forneça as propriedades do serviço de aplicações](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Para começar a criar recursos do Azure, selecione **criar**.  
    Depois de criada a aplicação web do ASP.NET, o assistente publica-o para o Azure e, em seguida, inicia a aplicação no browser predefinido.

6. Copie o URL da aplicação web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passo 4: Adicionar o novo `loyaltyNumber` de afirmação para o esquema do ficheiro TrustFrameworkExtensions.xml
O `loyaltyNumber` afirmação ainda não está definida no nosso esquema. Adicione uma definição dentro de `<BuildingBlocks>` elemento, que pode encontrar o início do *TrustFrameworkExtensions.xml* ficheiro.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Passo 5: Adicionar um fornecedor de afirmações 
Cada fornecedor de afirmações têm de ter um ou mais perfis técnicas, que determinam os pontos finais e protocolos necessários para comunicar com o fornecedor de afirmações. 

Um fornecedor de afirmações pode ter vários perfis técnicos por vários motivos. Por exemplo, vários perfis técnicos podem ser definidos porque o fornecedor de afirmações suporta vários protocolos, pontos finais podem ter capacidades variando ou versões podem conter afirmações que tem uma variedade de níveis de garantia. Poderá ser aceitável para libertar afirmações confidenciais journey de um utilizador mas não no outro. 

O seguinte fragmento XML contém um nó de fornecedor de afirmações com dois perfis técnicos:

* **TechnicalProfile Id = "SignUp da API REST"**: define o serviço RESTful. 
   * `Proprietary`Esta será descrita como o protocolo para um fornecedor com base na RESTful. 
   * `InputClaims`Define as afirmações que serão enviadas do Azure AD B2C para o serviço REST. 

   Neste exemplo, o conteúdo da afirmação `givenName` envia para o serviço REST como `firstName`, o conteúdo da afirmação `surname` envia para o serviço REST como `lastName`, e `email` envia como está. O `OutputClaims` elemento define as afirmações que são obtidas no serviço RESTful de volta para o Azure AD B2C.

* **TechnicalProfile Id = "LocalAccountSignUpWithLogonEmail"**: Adiciona um perfil de técnicas de validação para um perfil existente de técnico (definido na política base). Durante a inscrição journey, o perfil de técnicas de validação invoca o perfil técnico anterior. Se o serviço RESTful devolve um erro HTTP 409 (um erro de conflito), a mensagem de erro será apresentada ao utilizador. 

Localize o `<ClaimsProviders>` nó e, em seguida, adicione o seguinte fragmento XML sob o `<ClaimsProviders>` nó:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passo 6: Adicionar o `loyaltyNumber` de afirmação para o ficheiro de política de terceiros entidade confiadora para a afirmação é enviada para a aplicação
Editar o *SignUpOrSignIn.xml* confiadora (RP) de ficheiros e modificar o Id de TechnicalProfile = elemento "PolicyProfile" para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Depois de adicionar a nova afirmação, o código de terceiros entidade confiadora este aspeto:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Passo 7: Carregar a política para o seu inquilino

1. No [portal do Azure](https://portal.azure.com), mude para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, abra **do Azure AD B2C**.

2. Selecione **identidade experiência Framework**.

3. Abra **todas as políticas**. 

4. Selecione **carregar política**.

5. Selecione o **substituir a política se existir** caixa de verificação.

6. Carregar o ficheiro de TrustFrameworkExtensions.xml e certifique-se de que transfere a validação.

7. Repita o passo anterior com o ficheiro SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Passo 8: Testar a política personalizada utilizando o executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, aceda a **identidade experiência Framework**.

    > [!NOTE]
    > **Executar agora** requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Abra **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) entidade confiadora que carregado e, em seguida, selecione **executar agora**.

    ![A janela de B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. O processo de teste, escrevendo **teste** no **o nome fornecido** caixa.  
    O Azure AD B2C apresenta uma mensagem de erro na parte superior da janela.

    ![Testar a política](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  No **o nome fornecido** caixa, escreva um nome (diferente de "teste").  
    O Azure AD B2C se inscreve o utilizador e, em seguida, envia um loyaltyNumber à sua aplicação. Tenha em atenção o número neste JWT.

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
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Pode transferir o código de conclusão de [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Passos seguintes
* [Proteger a sua API RESTful com a autenticação básica (nome de utilizador e palavra-passe)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Proteger a sua API RESTful com certificados de cliente](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
