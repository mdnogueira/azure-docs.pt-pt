---
title: "O Azure Active Directory B2C: Adicionar Twitter como um fornecedor de identidade OAuth1 utilizando as políticas personalizadas"
description: "Utilizar o Twitter como um fornecedor de identidade através do protocolo OAuth1"
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
ms.openlocfilehash: f3a7936a468df7b0a2713f1f30c5b91e74d1d917
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>O Azure Active Directory B2C: Adicionar Twitter como um fornecedor de identidade OAuth1 utilizando as políticas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o início de sessão dos utilizadores de uma conta do Twitter utilizando [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

## <a name="step-1-create-a-twitter-account-application"></a>Passo 1: Criar uma aplicação de conta do Twitter
Para utilizar o Twitter como um fornecedor de identidade no Azure Active Directory B2C (Azure AD B2C), tem de criar uma aplicação do Twitter e forneça-lo com os parâmetros corretos. Pode registar uma aplicação do Twitter, acedendo ao [página inscrição do Twitter](https://twitter.com/signup).

1. Vá para o [Twitter programadores](https://apps.twitter.com/) Web site, inicie sessão com as credenciais de conta do Twitter e, em seguida, selecione **criar nova aplicação**.

    ![Conta do twitter - criar nova aplicação](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. No **criar uma aplicação** janela, efetue o seguinte procedimento:
 
    a. Tipo de **nome** e um **Descrição** para a nova aplicação. 

    b. No **Web site** caixa, cole **https://login.microsoftonline.com**. 

    c. No **URL de chamada de retorno** caixa, cole **https://login.microsoftonline.com/te/ {tenant}.onmicrosoft.com/oauth2/authresp**. Substitua {*inquilino*} com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). Certifique-se de que está a utilizar o esquema de HTTPS. 

    d. Na parte inferior da página, leia e aceite os termos de licenciamento e, em seguida, selecione **criar a sua aplicação Twitter**.

    ![Conta do twitter - adicionar uma nova aplicação](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. No **demonstração de B2C** janela, selecione **definições**, selecione o **permitem que esta aplicação a ser utilizado para iniciar sessão com o Twitter** caixa de verificação e, em seguida, selecione **atualização Definições**.

4. Selecione **chaves e os Tokens de acesso**e tenha em atenção o **consumidor chave (chave de API)** e **segredo de consumidor (API segredo)** valores.

    ![Conta do twitter - propriedades do conjunto de aplicações](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >O segredo de consumidor é uma credencial de segurança importantes. Não partilhe este segredo com ninguém e distribui-lo com a sua aplicação.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Passo 2: Adicionar a chave de aplicação de conta do Twitter ao Azure AD B2C
A Federação com o Twitter contas requer um segredo de consumidor da conta do Twitter para confiança do Azure AD B2C em nome da aplicação. Para armazenar o segredo de consumidor da aplicação Twitter no seu inquilino do Azure AD B2C, efetue o seguinte: 

1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **identidade experiência Framework**.

2. Para ver as chaves que estão disponíveis no seu inquilino, selecione **política chaves**.

3. Selecione **Adicionar**.

4. No **opções** caixa, selecione **Manual**.

5. No **nome** caixa, selecione **TwitterSecret**.  
    O prefixo *B2C_1A_* podem ser adicionados automaticamente.

6. No **segredo** box, introduza o seu segredo da aplicação Microsoft do [Portal de registo de aplicação](https://apps.dev.microsoft.com).

7. Para **utilização da chave**, utilize **encriptação**.

8. Selecione **Criar**.

9. Confirme que criou o `B2C_1A_TwitterSecret` chave.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Passo 3: Adicionar um fornecedor de afirmações na política de extensão

Se pretender que os utilizadores para iniciar sessão utilizando a conta do Twitter, tem de definir Twitter como um fornecedor de afirmações. Por outras palavras, tem de especificar os pontos finais do Azure AD B2C com comunica. Os pontos finais fornecem um conjunto de afirmações que são utilizados pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Definir o Twitter como um fornecedor de afirmações adicionando `<ClaimsProvider>` nó no seu ficheiro de política de extensão:

1. No seu diretório de trabalho, abra o *TrustFrameworkExtensions.xml* ficheiro de política de extensão. 

2. Procure o `<ClaimsProviders>` secção.

3. No `<ClaimsProviders>` nó, adicione o seguinte fragmento XML:  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4. Substitua o *client_id*' valor com a sua chave de consumidor de aplicação de conta do Twitter.

5. Guarde o ficheiro.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Passo 4: Registar o fornecedor de afirmações de conta do Twitter da sua viagem de utilizador de inscrição ou início de sessão
Configurou o fornecedor de identidade. No entanto, se ainda não está disponível em qualquer uma das janelas da inscrição ou início de sessão. Agora tem de adicionar o fornecedor de identidade de conta do Twitter para o utilizador `SignUpOrSignIn` journey de utilizador.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Passo 4.1: Fazer uma cópia de journey do utilizador
Para disponibilizar o journey de utilizador, crie um duplicado de um modelo de journey de utilizador existente e, em seguida, adicione o fornecedor de identidade do Twitter:

>[!NOTE]
>Se tiver copiado o `<UserJourneys>` elemento a partir do ficheiro de base da sua política para o *TrustFrameworkExtensions.xml* ficheiro de extensão, pode avançar para a secção seguinte.

1. Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).

2. Procure o `<UserJourneys>` elemento, selecionar todo o conteúdo do `<UserJourney>` nó e, em seguida, selecione **Cortar** para mover o texto selecionado para a área de transferência.

3. Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e, em seguida, procure o `<UserJourneys>` elemento. Se o elemento não existir, adicioná-lo.

4. Cole os conteúdos integrais do `<UserJourney>` nó, o que é movida para a área de transferência no passo 2, para o `<UserJourneys>` elemento.

### <a name="step-42-display-the-button"></a>Passo 4.2: Apresentar no botão""
O `<ClaimsProviderSelections>` elemento define a lista de opções de seleção de fornecedor de afirmações e a sua ordem. O `<ClaimsProviderSelection>` nó é semelhante a um botão do fornecedor de identidade numa página de inscrição ou início de sessão. Se adicionar um `<ClaimsProviderSelection>` nós para uma conta do Twitter, um novo botão é apresentado quando um utilizador lands na página. Para adicionar este elemento, faça o seguinte:

1. Procure o `<UserJourney>` nó que contém `Id="SignUpOrSignIn"` no journey utilizador que copiou.

2. Localize o `<OrchestrationStep>` nó que contém `Order="1"`.

3. No `<ClaimsProviderSelections>` elemento, adicione o seguinte fragmento XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Passo 4.3: Ligação o botão para uma ação
Agora que tem um botão no local, deve ligá-la a uma ação. Neste caso, é a ação para o Azure AD B2C comunicar com a conta do Twitter para receber um token. O botão de ligação para uma ação associando o perfil técnico para o fornecedor de afirmações de conta do Twitter:

1. Procure o `<OrchestrationStep>` nó que contém `Order="2"` no `<UserJourney>` nós.
2. No `<ClaimsExchanges>` elemento, adicione o seguinte fragmento XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Certifique-se de que `Id` tem o mesmo valor da `TargetClaimsExchangeId` na secção anterior.
    >* Certifique-se de que o `TechnicalProfileReferenceId` ID está definido para o perfil de técnico que criou anteriormente (Twitter-OAUTH1).

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
    Agora deverá conseguir iniciar sessão utilizando a conta do Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Passo 7: Registar (opcional) a conta do Twitter afirmações do fornecedor para a journey de utilizador de edição de perfil
Pode também querer adicionar o fornecedor de identidade de conta do Twitter para sua `ProfileEdit` journey de utilizador. Para tornar o utilizador journey disponível, repetido "passo 4." De momento, selecione o `<UserJourney>` nó que contém `Id="ProfileEdit"`. Guardar, carregar e testar a sua política.


## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Transferir os ficheiros de política concluída
Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
