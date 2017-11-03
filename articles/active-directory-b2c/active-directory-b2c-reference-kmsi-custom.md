---
title: 'B2C do Azure Active Directory: KMSI | Microsoft Docs'
description: "Um tópico demonstrar como configurar 'keep-me com sessão iniciado'"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: ajalexander
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: d09e15c6f6765eac436f573f89c6703039cd8397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Ativar o 'Manter a minha sessão iniciada (KMSI)'  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C agora permite o seu web e aplicações nativas para ativar a funcionalidade 'Manter a minha sessão iniciada (KMSI)'. Esta funcionalidade concede acesso para devolver os utilizadores à aplicação sem pedir confirmação ao introduzir o nome de utilizador e palavra-passe. Este acesso é revogado quando o utilizador terminar a sessão. 

Recomenda-se contra utilizadores selecionar esta opção em computadores públicos. 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>Pré-requisitos

Conta de um inquilino do Azure AD B2C configurado para permitir local sessão-up/início de sessão, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Como ativar KMSI

Efetue as seguintes alterações na política de extensões de estrutura de confiança.

## <a name="adding-a-content-definition-element"></a>Adicionar um elemento de definição de conteúdo 

O `BuildingBlocks` nós do seu ficheiro de extensão tem de incluir um `ContentDefinitions` elemento. 

1. No `ContentDefinitions` secção, definir uma nova `ContentDefinition` com o ID `api.signuporsigninwithkmsi`.
2. A nova `ContentDefinition` tem de incluir um `LoadUri`, `RecoveryUri` e `DataUri` da seguinte forma.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` é um identificador de compreensíveis máquina aparece uma caixa de verificação KMSI nas páginas de início de sessão. Certifique-se de que não altere este valor. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>Adicionar um fornecedor de afirmações de início de sessão de conta local 

Pode definir início de sessão de conta Local como um fornecedor de afirmações para o `<ClaimsProvider>` no ficheiro de extensão da sua política de nó:

1. Abra o ficheiro de extensão (TrustFrameworkExtensions.xml) a partir do diretório de trabalho. 
2. Localizar o `<ClaimsProviders>` secção. Se não existir, adicione-a sob o nó de raiz.
3. O pacote de arranque de [introdução](active-directory-b2c-get-started-custom.md) vem com um fornecedor de afirmações 'SignIn de conta Local'. 
4. Caso contrário, adicione um novo `<ClaimsProvider>` nó da seguinte forma:

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="login-NonInteractive">
           <Metadata>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
           </Metadata>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar os IDs de aplicações na sua política personalizada

Adicionar os IDs de aplicação para o ficheiro de extensões (`TrustFrameworkExtensions.xml`):

1. O ficheiro de extensões (TrustFrameworkExtensions.xml), localize o elemento `<TechnicalProfile Id="login-NonInteractive">` e`<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Substitua todas as instâncias de `IdentityExperienceFrameworkAppId` com o ID da aplicação Framework de experiência de identidade, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md). Segue-se um exemplo:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Substitua todas as instâncias de `ProxyIdentityExperienceFrameworkAppId` com o ID da aplicação Framework de experiência de identidade de Proxy, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).

4. Guarde o ficheiro de extensões.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Criar um KMSI no journey utilizador ativado

Agora tem de adicionar o fornecedor de afirmações de início de sessão de conta Local da sua viagem de utilizador. 

1. Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).
2. Localizar o `<UserJourneys>` elemento e copie todo o `<UserJourney>` nó que inclui `Id="SignUpOrSignIn"`.
3. Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o `<UserJourneys>` elemento. Se o elemento não existir, adicione uma.
4. Cole a toda a `<UserJourney>` nó que copiou como subordinado do `<UserJourneys>` elemento.
5. Mudar o nome de ID de journey de utilizador novo (por exemplo, mudar o nome como `SignUpOrSignInWithKmsi`).
6. Por fim, em `OrchestrationStep 1` alterar o `ContentDefinitionReferenceId` para `api.signuporsigninwithkmsi` , definidas nos passos anteriores. Isto permite que a caixa de verificação journey o utilizador. 
7. Tiver terminado a modificação do ficheiro de extensão. Guardar e carregar este ficheiro. Certifique-se de que todas as validações êxito.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>Criar um ficheiro de terceiros (RP) entidade confiadora

Em seguida, atualize o ficheiro de terceiros (RP) entidade confiadora que inicia o journey de utilizador que criou:

1. Fazer uma cópia de SignUpOrSignIn.xml no seu diretório de trabalho. Em seguida, mude o nome (por exemplo, SignUpOrSignInWithKmsi.xml).

2. Abra o ficheiro novo e a atualização a `PolicyId` atributo para `<TrustFrameworkPolicy>` com um valor exclusivo. Este é o nome da sua política (por exemplo, SignUpOrSignInWithKmsi).

3. Modificar o `ReferenceId` atributo em `<DefaultUserJourney>` para fazer corresponder o `Id` do novo journey de utilizador que criou (por exemplo, SignUpOrSignInWithKmsi).

4. KMSI está configurado no `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`**controla o tempo durante o qual o utilizador permanecer com sessão iniciada. No exemplo seguinte, sessão KMSI expira automaticamente depois de 14 dias, independentemente da frequência o utilizador efetua a autenticação automática.

   Definição `KeepAliveInDays` valor como 0 desativa a funcionalidade KMSI. Por predefinição, este valor é 0

6. Se  **`SessionExpiryType`**  é *Rolling*, em seguida, a sessão KMSI for prolongada até nos últimos 14 dias sempre que o utilizador efetua a autenticação automática.  Se *Rolling* é selecionado, recomendamos que mantenha o número de dias ao mínimo. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. Guardar as alterações e, em seguida, carregue o ficheiro.

8. Para testar a política personalizada que carregou, no portal do Azure, aceda ao painel de política e, em seguida, clique em **executar agora**.


## <a name="link-to-sample-policy"></a>Associar a política de exemplo

Pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








