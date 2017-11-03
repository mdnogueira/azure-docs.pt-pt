---
title: "Azure Active Directory B2C: Adicionar LinkedIn como um fornecedor de identidade de OAuth2 através da utilização de políticas personalizadas"
description: "Um artigo de procedimentos sobre como configurar uma aplicação LinkedIn utilizando o protocolo de OAuth2 e as políticas personalizadas"
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
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: f72fac677aa7d461f174b2b06db69df235273375
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>O Azure Active Directory B2C: Adicionar LinkedIn como um fornecedor de identidade ao utilizar políticas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o início de sessão dos utilizadores de uma conta de LinkedIn utilizando [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

## <a name="step-1-create-a-linkedin-account-application"></a>Passo 1: Criar uma aplicação da conta de LinkedIn
Para utilizar LinkedIn como um fornecedor de identidade no Azure Active Directory B2C (Azure AD B2C), tem de criar uma aplicação LinkedIn e forneça-lo com os parâmetros corretos. Pode registar uma aplicação LinkedIn acedendo ao [página inscrição do LinkedIn](https://LinkedIn.com/signup).

1. Vá para o [gestão de aplicações do LinkedIn](https://www.linkedin.com/secure/developer?newapp=) Web site, inicie sessão com as credenciais da conta LinkedIn e, em seguida, selecione **Criar aplicação**.

    ![LinkedIn conta - criar aplicação](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. No **criar uma nova aplicação** página, efetue o seguinte:

    a. Tipo sua **nome da empresa**, um descritivo **nome** da empresa e um **Descrição** da sua nova aplicação.

    b. Carregar o **logótipo de aplicação**.

    c. Selecione um **utilização de aplicações**.

    d. No **URL do site** caixa, cole **https://login.microsoftonline.com**.

    e. Tipo sua **E-Mail profissionais** endereço e **telefone da empresa** número.

    f. Na parte inferior da página, leia e aceite os termos de utilização e, em seguida, selecione **submeter**.

    ![LinkedIn conta - configurar propriedades da aplicação](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Selecione **autenticação**e, em seguida, tenha em atenção o **ID de cliente** e **segredo do cliente** valores.

4. No **autorizado URLs de redirecionamento** caixa, cole **https://login.microsoftonline.com/te/ {tenant}.onmicrosoft.com/oauth2/authresp**. Substitua {*inquilino*} com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). Certifique-se de que está a utilizar o esquema de HTTPS. 

    ![Conta LinkedIn - URLs de redirecionamento de conjunto autorizado](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >O segredo do cliente é uma credencial de segurança importantes. Não partilhe este segredo com ninguém e distribui-lo com a sua aplicação.

5. Selecione **Adicionar**.

6. Selecione **definições**, altere o **estado da aplicação** para **em direto**e, em seguida, selecione **atualização**.

    ![Conta de LinkedIn - definir estado da aplicação](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Passo 2: Adicionar a chave de aplicação do LinkedIn ao Azure AD B2C
A Federação com contas do LinkedIn requer um segredo do cliente para a conta de LinkedIn a confiança do Azure AD B2C em nome da aplicação. Para armazenar o segredo de aplicação LinkedIn no seu inquilino do Azure AD B2C, efetue o seguinte:  

1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **identidade experiência Framework**.

2. Para ver as chaves que estão disponíveis no seu inquilino, selecione **política chaves**.

3. Selecione **Adicionar**.

4. No **opções** caixa, selecione **carregar**.

5. No **nome** caixa, escreva **B2cRestClientCertificate**.  
    O prefixo *B2C_1A_* podem ser adicionados automaticamente.

6. No **segredo** box, introduza o seu segredo de aplicação LinkedIn do [Portal de registo de aplicação](https://apps.dev.microsoft.com).

7. Para **utilização da chave**, selecione **encriptação**.

8. Selecione **Criar**. 

9. Confirme que criou o `B2C_1A_LinkedInSecret`chave.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Passo 3: Adicionar um fornecedor de afirmações na política de extensão
Se pretender que os utilizadores para iniciar sessão utilizando a respetiva conta LinkedIn, tem de definir LinkedIn como um fornecedor de afirmações. Por outras palavras, tem de especificar os pontos finais do Azure AD B2C com comunica. Os pontos finais fornecem um conjunto de afirmações que são utilizados pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Definir LinkedIn como um fornecedor de afirmações, adicionando um `<ClaimsProvider>` nó no seu ficheiro de política de extensão:

1. No seu diretório de trabalho, abra o *TrustFrameworkExtensions.xml* ficheiro de política de extensão. 

2. Procure o `<ClaimsProviders>` elemento.

3. No `<ClaimsProviders>` elemento, adicione o seguinte fragmento XML: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Substitua o *client_id* valor com o ID de cliente de aplicação do LinkedIn.

5. Guarde o ficheiro.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Passo 4: Registar o fornecedor de afirmações de conta do LinkedIn
Configurou o fornecedor de identidade. No entanto, se ainda não está disponível em qualquer uma das janelas da inscrição ou início de sessão. Agora tem de adicionar o fornecedor de identidade da conta de LinkedIn seu utilizador `SignUpOrSignIn` journey de utilizador.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Passo 4.1: Fazer uma cópia de journey do utilizador
Para disponibilizar o journey de utilizador, crie um duplicado de um modelo de journey de utilizador existente e, em seguida, adicione o fornecedor de identidade do LinkedIn:

>[!NOTE]
>Se tiver copiado o `<UserJourneys>` elemento a partir do ficheiro de base da sua política para o *TrustFrameworkExtensions.xml* ficheiro de extensão, pode ignorar esta secção.

1. Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).

2. Procure o `<UserJourneys>` elemento, selecionar todo o conteúdo do `<UserJourney>` nó e, em seguida, selecione **Cortar** para mover o texto selecionado para a área de transferência.

3. Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e procure o `<UserJourneys>` elemento. Se o elemento não existir, adicioná-lo.

4. Cole os conteúdos integrais do `<UserJourney>` nó, o que é movida para a área de transferência no passo 2, para o `<UserJourneys>` elemento.

### <a name="step-42-display-the-button"></a>Passo 4.2: Apresentar no botão""
O `<ClaimsProviderSelections>` elemento define a lista de opções de seleção de fornecedor de afirmações e a sua ordem. O `<ClaimsProviderSelection>` nó é semelhante a um botão do fornecedor de identidade numa página de inscrição ou início de sessão. Se adicionar um `<ClaimsProviderSelection>` nós para uma conta de LinkedIn, um novo botão é apresentado quando um utilizador lands na página. Para adicionar este elemento, faça o seguinte:

1. Procure o `<UserJourney>` nó que contém `Id="SignUpOrSignIn"` no journey utilizador que copiou.

2. Localize o `<OrchestrationStep>` nó que inclui `Order="1"`.

3. No `<ClaimsProviderSelections>` elemento, adicione o seguinte fragmento XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Passo 4.3: Ligação o botão para uma ação
Agora que tem um botão no local, deve ligá-la a uma ação. Neste caso, é a ação para o Azure AD B2C comunicar com a conta de LinkedIn para receber um token. O botão de ligação para uma ação ao ligar o perfil técnico para o seu fornecedor de afirmações de conta LinkedIn:

1. Procure o `<OrchestrationStep>` nó que contém `Order="2"` no `<UserJourney>` nós.

2. No `<ClaimsExchanges>` elemento, adicione o seguinte fragmento XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Certifique-se de que `Id` tem o mesmo valor da `TargetClaimsExchangeId` na secção anterior.
    >* Certifique-se de que o `TechnicalProfileReferenceId` ID está definido para o perfil de técnico que criou anteriormente (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Passo 5: Carregar a política para o seu inquilino
1. No [portal do Azure](https://portal.azure.com), mude para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **do Azure AD B2C**.

2. Selecione **identidade experiência Framework**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política se existir** caixa de verificação.

6. Carregar o *TrustFrameworkBase.xml* e *TrustFrameworkExtensions.xml* ficheiros e certifique-se de que passam a validação.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Passo 6: Testar a política personalizada utilizando o executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, selecione **identidade experiência Framework**.

    >[!NOTE]
    >Executar agora requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Abra **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) entidade confiadora que carregado e, em seguida, selecione **executar agora**.  
    Agora deverá conseguir iniciar sessão utilizando a conta de LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Passo 7: Registar (opcional) a conta de LinkedIn afirmações fornecedor journey de utilizador a edição de perfil
Pode também querer adicionar o fornecedor de identidade de conta LinkedIn a sua `ProfileEdit` journey de utilizador. Para tornar o utilizador journey disponível, repetido "passo 4." De momento, selecione o `<UserJourney>` nó que contém `Id="ProfileEdit"`. Guardar, carregar e testar a sua política.

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Transferir os ficheiros de política concluída
Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
