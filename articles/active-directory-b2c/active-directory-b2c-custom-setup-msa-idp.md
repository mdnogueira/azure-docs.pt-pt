---
title: "O Azure Active Directory B2C: Adicionar a conta Microsoft (MSA) como um fornecedor de identidade através de políticas personalizadas"
description: "Exemplo com o Microsoft como fornecedor de identidade através do protocolo OpenID Connect (OIDC)"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8c981046ff41d3927ff60d6dc4f40366ae25ba74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>O Azure Active Directory B2C: Adicionar a conta Microsoft (MSA) como um fornecedor de identidade através de políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o início de sessão para os utilizadores da conta Microsoft (MSA) através da utilização de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos
Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

Estes passos incluem:

1.  Criar uma aplicação da conta Microsoft.
2.  Adicionar a chave de conta da aplicação do Microsoft Azure AD B2C
3.  Adicionar fornecedor de afirmações para uma política
4.  Registar o fornecedor de afirmações Account Microsoft para um journey de utilizador
5.  Carregar a política para um Azure AD B2C inquilino e testá-lo

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação da conta Microsoft
Para utilizar a conta Microsoft como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação da conta Microsoft e forneça-lo com os parâmetros corretos. Necessita de uma conta Microsoft. Se não tiver uma, visite [https://www.live.com/](https://www.live.com/).

1.  Vá para o [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e inicie sessão com as credenciais da conta Microsoft.
2.  Clique em **adicionar uma aplicação**.

    ![Microsoft conta - adicionar uma aplicação](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Forneça um **nome** para a sua aplicação, **contacte e-mail**, desmarque **nos ajuda a começar** e clique em **criar**.

    ![Conta Microsoft - registar a aplicação](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Copie o valor da **Id da aplicação**. É necessário para configurar a conta Microsoft como um fornecedor de identidade no seu inquilino.

    ![Conta Microsoft - copiar o valor do Id de aplicação](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Clique em **adicionar plataforma**

    ![Microsoft conta – adicionar plataforma](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  A partir da lista de plataforma, escolha **Web**.

    ![Conta Microsoft – na lista de plataforma escolha Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **redirecionar URIs** campo. Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com).

    ![Conta Microsoft - URLs de redirecionamento de conjunto](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Clique em **gerar a nova palavra-passe** sob o **aplicação segredos** secção. Copie a nova palavra-passe apresentado no ecrã. É necessário para configurar a conta Microsoft como um fornecedor de identidade no seu inquilino. Esta palavra-passe é uma credencial de segurança importantes.

    ![Microsoft conta - gerar nova palavra-passe](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Conta Microsoft - cópia a nova palavra-passe](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Selecione a caixa que indica que **suporte Live SDK** sob o **opções avançadas** secção. Clique em **Guardar**.

    ![Conta Microsoft – suporte Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Adicione a chave de aplicação de conta do Microsoft Azure AD B2C
A Federação com contas Microsoft requer um segredo do cliente para a conta Microsoft à confiança do Azure AD B2C em nome da aplicação. É necessário para armazenar o secert de aplicação de conta Microsoft no inquilino do Azure AD B2C:   

1.  Aceda ao seu inquilino do Azure AD B2C e selecione **definições do B2C** > **Framework de experiência de identidade**
2.  Selecione **política chaves** para ver as chaves disponíveis no seu inquilino.
3.  Clique em **+ adicionar**.
4.  Para **opções**, utilize **Manual**.
5.  Para **nome**, utilize `MSASecret`.  
    O prefixo `B2C_1A_` podem ser adicionados automaticamente.
6.  No **segredo** box, introduza o seu segredo da aplicação Microsoft do https://apps.dev.microsoft.com
7.  Para **utilização da chave**, utilize **assinatura**.
8.  Clique em **Criar**.
9.  Confirme que criou a chave `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um fornecedor de afirmações na política de extensão
Se pretender que os utilizadores para iniciar sessão utilizando Account Microsoft, terá de definir Account Microsoft como um fornecedor de afirmações. Por outras palavras, tem de especificar um ponto final que o Azure AD B2C comunica com. O ponto final fornece um conjunto de afirmações que são utilizados pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Definir Account Microsoft como um fornecedor de afirmações, adicionando `<ClaimsProvider>` nó no seu ficheiro de política de extensão:

1.  Abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml) do diretório de trabalho. Se precisar de um editor de XML, [tente Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma simples.
2.  Localizar o `<ClaimsProviders>` secção
3.  Adicione o seguinte fragmento XML sob o `ClaimsProviders` elemento:

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
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

4.  Substitua `client_id` valor com o Id de cliente da aplicação de Account Microsoft

5.  Guarde o ficheiro.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registo que Account Microsoft afirmações do fornecedor de início de sessão cópias de segurança ou de início de sessão journey de utilizador

Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em nenhum dos ecrãs de sessão-up/início de sessão. Agora tem de adicionar o fornecedor de identidade Account Microsoft para o utilizador `SignUpOrSignIn` journey de utilizador. Para tornar disponível, criamos um duplicado de um existente journey de utilizador do modelo.  Em seguida, iremos adicionar o fornecedor de identidade Account Microsoft:

> [!NOTE]
>
>Se anteriormente tiver copiado o `<UserJourneys>` elemento a partir do ficheiro de base da sua política para o ficheiro de extensão `TrustFrameworkExtensions.xml`, pode ignorar esta secção.

1.  Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).
2.  Localizar o `<UserJourneys>` elemento e copie todo o conteúdo de `<UserJourneys>` nós.
3.  Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o `<UserJourneys>` elemento. Se o elemento não existir, adicione uma.
4.  Colar o conteúdo completo de `<UserJournesy>` nó que copiou como subordinado do `<UserJourneys>` elemento.

### <a name="display-the-button"></a>Apresentar no botão
O `<ClaimsProviderSelections>` elemento define a lista de opções de seleção de fornecedor de afirmações e a sua ordem.  `<ClaimsProviderSelection>`elemento é semelhante a um botão do fornecedor de identidade numa página sessão-up/início de sessão. Se adicionar um `<ClaimsProviderSelection>` elemento para a conta Microsoft, um novo botão aparece quando um utilizador lands na página. Para adicionar este elemento:

1.  Localizar o `<UserJourney>` nó que inclui `Id="SignUpOrSignIn"` no journey utilizador que copiou.
2.  Localize o `<OrchestrationStep>` nó que inclui`Order="1"`
3.  Adicione o seguinte fragmento XML em `<ClaimsProviderSelections>` nó:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação
Agora que tem um botão no local, terá de ligá-la a uma ação. Neste caso, é a ação para o Azure AD B2C comunicar com o Microsoft Account para receber um token. O botão de ligação para uma ação associando o perfil técnico para o seu fornecedor de afirmações Account Microsoft:

1.  Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
2.  Adicione o seguinte fragmento XML em `<ClaimsExchanges>` nó:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Certifique-se a `Id` tem o mesmo valor da `TargetClaimsExchangeId` na secção anterior
>   * Certifique-se `TechnicalProfileReferenceId` ID está definido para o perfil de técnico que criou anteriormente (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política para o seu inquilino
1.  No [portal do Azure](https://portal.azure.com), mudar para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e abra o **do Azure AD B2C** painel.
2.  Selecione **identidade experiência Framework**.
3.  Abra o **todas as políticas** painel.
4.  Selecione **carregar política**.
5.  Verifique **substituir a política se existir** caixa.
6.  **Carregar** TrustFrameworkExtensions.xml e certifique-se de que não falha a validação

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada utilizando o executar agora

1.  Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.
> [!NOTE]
>
>**Executar agora** requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.
2.  Abra **B2C_1A_signup_signin**, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.
3.  Deverá conseguir iniciar sessão com a conta Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registar o fornecedor de afirmações Account Microsoft journey de utilizador de edição de perfil
Poderá pretender adicionar o fornecedor de identidade Account Microsoft também para o utilizador `ProfileEdit` journey de utilizador. Para disponibilizá-lo, iremos Repita os dois últimos passos:

### <a name="display-the-button"></a>Apresentar no botão
1.  Abra o ficheiro de extensão da sua política (por exemplo, TrustFrameworkExtensions.xml).
2.  Localizar o `<UserJourney>` nó que inclui `Id="ProfileEdit"` no journey utilizador que copiou.
3.  Localize o `<OrchestrationStep>` nó que inclui`Order="1"`
4.  Adicione o seguinte fragmento XML em `<ClaimsProviderSelections>` nó:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação
1.  Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
2.  Adicione o seguinte fragmento XML em `<ClaimsExchanges>` nó:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testar a política de edição de perfil personalizada utilizando o executar agora
1.  Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.
2.  Abra **B2C_1A_ProfileEdit**, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.
3.  Deverá conseguir iniciar sessão com a conta Microsoft.

## <a name="download-the-complete-policy-files"></a>Transferir os ficheiros de política concluída
Opcional: Recomendamos que crie o seu cenário utilizando a sua própria política personalizada do ficheiros depois de concluir a introdução com as políticas personalizadas guiá-em vez de utilizar estes ficheiros de exemplo.  [Ficheiros de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
