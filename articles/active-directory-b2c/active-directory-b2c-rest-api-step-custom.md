---
title: "O Azure Active Directory B2C: API de REST afirmações trocas como um passo de orquestração | Microsoft Docs"
description: "Um tópico no Azure Active Directory B2C políticas personalizadas que se integram com uma API"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: dc319c97e64e55861b84cc3943667418077a05d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Instruções: Integrar o REST API trocas de afirmações da sua viagem do Azure AD B2C utilizador como um passo de orquestração

A estrutura de experiência de identidade (IEF) subjacente do Azure Active Directory B2C (Azure AD B2C) permite que o Programador de identidade integrar uma interação com uma API RESTful em journey um utilizador.  

No final destas instruções, poderá criar um journey de utilizador do Azure AD B2C que interage com os serviços RESTful.

O IEF envia os dados em afirmações e recebe dados novamente nas afirmações. A API REST afirmações exchange:

- Pode ser desenvolvido como um passo de orquestração.
- Pode acionar uma ação externa. Por exemplo, pode iniciar um evento numa base de dados externa.
- Pode ser utilizado para obter um valor e, em seguida, guarde-o numa base de dados do utilizador.

Pode utilizar as afirmações recebidas mais tarde para alterar o fluxo de execução.

Também pode conceber a interação como um perfil de validação. Para obter mais informações, consulte [explicação passo a passo: integrar o API de REST afirmações trocas da sua viagem do Azure AD B2C utilizador como validação na entrada de utilizador](active-directory-b2c-rest-api-validation-custom.md).

O cenário é que quando um utilizador efetua uma edição de perfil, queremos:

1. Procure o utilizador no sistema externo.
2. Obter a cidade em que o utilizador está registado.
3. Devolva esse atributo para a aplicação como uma afirmação.

## <a name="prerequisites"></a>Pré-requisitos

- Um inquilino do Azure AD B2C configurado para concluir uma conta local sessão-up/início de sessão, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).
- Um ponto final de REST API para interagir com. Estas instruções utilizam um webhook de aplicação de função do Azure simples como exemplo.
- *Recomendado*: concluir o [exchange instruções como um passo de validação de afirmações de REST API](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Passo 1: Preparar a função de REST API

> [!NOTE]
> A configuração de funções de API de REST está fora do âmbito deste artigo. [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece uma excelente toolkit para criar serviços RESTful na nuvem.

Vamos configurar uma função do Azure que recebe uma afirmação denominada `email`e, em seguida, devolve a afirmação `city` com o valor atribuído de `Redmond`. A função do Azure de exemplo está no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

O `userMessage` afirmação que devolve a função do Azure é opcional neste contexto e a IEF ignorará. Pode, potencialmente, utilizá-lo como uma mensagem transmitida para a aplicação e apresentados ao utilizador mais tarde.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Uma aplicação de função do Azure torna mais fácil obter o URL de função, o que inclui o identificador da função específica. Neste caso, o URL é: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Pode utilizá-lo para fins de teste.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Passo 2: Configurar a troca de afirmações de RESTful API como um perfil no seu ficheiro TrustFrameworExtensions.xml técnico

Um perfil técnico é a configuração completa do exchange com o serviço RESTful assim o desejar. Abra o ficheiro de TrustFrameworkExtensions.xml e adicione o seguinte fragmento XML no interior do `<ClaimsProvider>` elemento.

> [!NOTE]
> No XML de seguinte, o fornecedor RESTful `Version=1.0.0.0` será descrita como o protocolo. Considere-o como a função que irá interagir com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

O `<InputClaims>` elemento define as afirmações que serão enviadas o IEF para o serviço REST. Neste exemplo, o conteúdo da afirmação `givenName` serão enviados para o serviço REST como a afirmação `email`.  

O `<OutputClaims>` elemento define as afirmações que o IEF irá esperar do serviço REST. Independentemente do número de afirmações que são recebidos, o IEF irá utilizar apenas os identificado aqui. Neste exemplo, uma afirmação recebida como `city` serão mapeados para um IEF afirmação denominada `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Passo 3: Adicionar a nova afirmação `city` o esquema do ficheiro TrustFrameworkExtensions.xml

A afirmação `city` é ainda não definidos em qualquer lugar no nosso esquema. Por isso, adicione uma definição dentro do elemento `<BuildingBlocks>`. Pode encontrar este elemento no início do ficheiro TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Passo 4: Incluem a troca de afirmações de serviço REST como uma orquestração passo da sua perfil Editar utilizador viagem no TrustFrameworkExtensions.xml

Adicione um passo para o perfil Editar utilizador journey, depois do utilizador foi autenticado (orchestration passos 1 a 4 o seguinte XML) e o utilizador forneceu as informações de perfil atualizado (passo 5).

> [!NOTE]
> Existem muitos casos de utilização, onde a chamada de REST API pode ser utilizada como um passo de orquestração. Como um passo de orquestração, pode ser utilizado como uma atualização para um sistema externo após um utilizador foi concluída com êxito uma tarefa, como o registo da primeira vez, ou como uma atualização de perfil para manter as informações sincronizadas. Neste caso, é utilizado para aumentar as informações fornecidas para a aplicação depois de editar o perfil.

Copiar o perfil de editar o código XML de journey do utilizador do ficheiro TrustFrameworkBase.xml ao seu ficheiro TrustFrameworkExtensions.xml dentro de `<UserJourneys>` elemento. Em seguida, efetue a modificação no passo 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Se a ordem não corresponde à sua versão, certifique-se de que inserir o código como o passo antes do `ClaimsExchange` tipo `SendClaims`.

O XML para o journey do utilizador final deve ter o seguinte aspeto:

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Passo 5: Adicionar a afirmação `city` para a entidade confiadora de política de ficheiros, de modo a afirmação é enviada para a aplicação

Edite o ficheiro do ProfileEdit.xml entidade confiadora (RP) de terceiros e modifique o `<TechnicalProfile Id="PolicyProfile">` elemento para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="city" />`.

Depois de adicionar a nova afirmação, o perfil técnico este aspeto:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>Passo 6: Carregar as suas alterações e teste

Substitua as versões existentes da política.

1.  (Opcional:) Guarde a versão existente (transferindo) do seu ficheiro extensões antes de continuar. Para manter a complexidade inicial baixa, recomendamos que não carregar várias versões do ficheiro extensões.
2.  (Opcional:) Mudar o nome da nova versão do ID de política para o ficheiro de edição de política alterando `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Carregue o ficheiro de extensões.
4.  Carregue o ficheiro RP da edição de política.
5.  Utilize **executar agora** para testar a política. Reveja o token que o IEF devolve à aplicação.

Se tudo está configurado corretamente, o token irá incluir a nova afirmação `city`, com o valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passos seguintes

[Utilizar uma API REST como um passo de validação](active-directory-b2c-rest-api-validation-custom.md)

[Modificar a edição de perfil para recolher informações adicionais dos seus utilizadores](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
