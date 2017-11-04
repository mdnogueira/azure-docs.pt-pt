---
title: "O Azure Active Directory B2C: Adicionar Google + como um fornecedor de identidade de OAuth2 através de políticas personalizadas"
description: "Exemplo utilizando Google + como fornecedor de identidade através do protocolo de OAuth2"
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
ms.openlocfilehash: 0d84dde1f70023abcfd0c15f5425d3cbaeb8c765
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>O Azure Active Directory B2C: Adicionar Google + como um fornecedor de identidade de OAuth2 através de políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este guia mostra como ativar o início de sessão para os utilizadores da conta Google + através da utilização de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

Estes passos incluem:

1.  Criar uma aplicação da conta Google +.
2.  Adicionar a chave de aplicação da conta Google + Azure AD B2C
3.  Adicionar fornecedor de afirmações para uma política
4.  Registar o Google + conta fornecedor de afirmações para um journey de utilizador
5.  Carregar a política para um Azure AD B2C inquilino e testá-lo

## <a name="create-a-google-account-application"></a>Criar uma aplicação da conta Google +
Para utilizar o Google + como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação do Google + e forneça-lo com os parâmetros corretos. Pode registar uma Google + aplicação aqui: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Vá para o [consola de programadores da Google](https://console.developers.google.com/) e inicie sessão com as credenciais da conta Google +.
2.  Clique em **criar projeto**, introduza um **nome do projeto**e, em seguida, clique em **criar**.

3.  Clique em de **menu projetos**.

    ![Google + conta - Selecione projeto](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Clique em de  **+**  botão.

    ![Google + conta - criar novo projeto](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Introduza um **nome do projeto**e, em seguida, clique em **criar**.

    ![Google + conta – novo projeto](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Aguarde até que o projeto está pronto e clique em de **menu projetos**.

    ![Google + conta - Aguarde até que o novo projeto está pronto a utilizar](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Clique no nome do seu projeto.

    ![Google + conta - Selecione o novo projeto](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Clique em **Gestor de API** e, em seguida, clique em **credenciais** no painel de navegação esquerdo.
9.  Clique em de **ecrã de consentimento do OAuth** separador no topo.

    ![Google + conta - ecrã de consentimento do OAuth definido](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Selecione ou especifique uma válida **endereço de correio eletrónico**, forneça um **nome de produto**e clique em **guardar**.

    ![Google + - as credenciais de aplicação](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Clique em **novas credenciais** e, em seguida, escolha **ID de cliente OAuth**.

    ![Google + - criar novas credenciais de aplicação](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  Em **tipo de aplicação**, selecione **aplicação Web**.

    ![Google + - tipo selecionar aplicação](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Forneça um **nome** para a sua aplicação, introduza `https://login.microsoftonline.com` no **autorizado JavaScript origens** campo, e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **autorizados redirecionar os URIs** campo. Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). O **{inquilino}** valor diferencia maiúsculas de minúsculas. Clique em **Criar**.

    ![Google + - forneça origens autorizado JavaScript e URI de redirecionamento](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Copie os valores de **Id de cliente** e **segredo do cliente**. Terá de a configurar Google + como um fornecedor de identidade no seu inquilino. **Segredo do cliente** é uma credencial de segurança importantes.

    ![Google + - copie os valores do segredo do cliente e o Id de cliente](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Adicionar a chave de aplicação da conta Google + Azure AD B2C
A Federação com o Google + contas requer um segredo do cliente de conta Google + à confiança do Azure AD B2C em nome da aplicação. É necessário para armazenar o seu segredo de aplicação do Google + no inquilino do Azure AD B2C:  

1.  Aceda ao seu inquilino do Azure AD B2C e selecione **definições do B2C** > **Framework de experiência de identidade**
2.  Selecione **política chaves** para ver as chaves disponíveis no seu inquilino.
3.  Clique em **+ adicionar**.
4.  Para **opções**, utilize **Manual**.
5.  Para **nome**, utilize `GoogleSecret`.  
    O prefixo `B2C_1A_` podem ser adicionados automaticamente.
6.  No **segredo** box, introduza o seu segredo de aplicação do Google do [consola de programadores da Google](https://console.developers.google.com/) que copiou acima.
7.  Para **utilização da chave**, utilize **assinatura**.
8.  Clique em **Criar**.
9.  Confirme que criou a chave `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um fornecedor de afirmações na política de extensão

Se pretender que os utilizadores para iniciar sessão utilizando a conta do Google +, tem de definir a conta do Google + como um fornecedor de afirmações. Por outras palavras, tem de especificar um ponto final que o Azure AD B2C comunica com. O ponto final fornece um conjunto de afirmações que são utilizados pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Definir a conta do Google + como um fornecedor de afirmações, adicionando `<ClaimsProvider>` nó no seu ficheiro de política de extensão:

1.  Abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml) do diretório de trabalho. Se precisar de um editor de XML, [tente Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma simples.
2.  Localizar o `<ClaimsProviders>` secção
3.  Adicione o seguinte fragmento XML sob o `ClaimsProviders` elemento e substitua `client_id` valor com o Google + aplicação cliente ID da conta antes de guardar o ficheiro.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registar o Google + conta fornecedor de afirmações para inscrever ou iniciar sessão no journey de utilizador

Configurar o fornecedor de identidade.  No entanto, não está disponível em nenhum dos ecrãs de sessão-up/início de sessão. Adicionar o Google + conta fornecedor de identidade para o utilizador `SignUpOrSignIn` journey de utilizador. Para tornar disponível, criamos um duplicado de um existente journey de utilizador do modelo.  Em seguida, iremos adicionar Google + conta fornecedor de identidade:

>[!NOTE]
>
>Se tiver copiado o `<UserJourneys>` elemento a partir do ficheiro de base da sua política para o ficheiro de extensão (TrustFrameworkExtensions.xml), pode ignorar esta secção.

1.  Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).
2.  Localizar o `<UserJourneys>` elemento e copie todo o conteúdo de `<UserJourneys>` nós.
3.  Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o `<UserJourneys>` elemento. Se o elemento não existir, adicione uma.
4.  Colar o conteúdo completo de `<UserJournesy>` nó que copiou como subordinado do `<UserJourneys>` elemento.

### <a name="display-the-button"></a>Apresentar no botão
O `<ClaimsProviderSelections>` elemento define a lista de opções de seleção de fornecedor de afirmações e a sua ordem.  `<ClaimsProviderSelection>`elemento é semelhante a um botão do fornecedor de identidade numa página sessão-up/início de sessão. Se adicionar um `<ClaimsProviderSelection>` elemento para a conta do Google +, um novo botão aparece quando um utilizador lands na página. Para adicionar este elemento:

1.  Localizar o `<UserJourney>` nó que inclui `Id="SignUpOrSignIn"` no journey utilizador que copiou.
2.  Localize o `<OrchestrationStep>` nó que inclui`Order="1"`
3.  Adicione o seguinte fragmento XML em `<ClaimsProviderSelections>` nó:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação
Agora que tem um botão no local, terá de ligá-la a uma ação. Neste caso, é a ação para o Azure AD B2C comunicar com a conta Google + receber um token.

1.  Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
2.  Adicione o seguinte fragmento XML em `<ClaimsExchanges>` nó:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Certifique-se a `Id` tem o mesmo valor da `TargetClaimsExchangeId` na secção anterior
> * Certifique-se `TechnicalProfileReferenceId` ID está definido para o perfil de técnico que criou anteriormente (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política para o seu inquilino
1.  No [portal do Azure](https://portal.azure.com), mudar para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e abra o **do Azure AD B2C** painel.
2.  Selecione **identidade experiência Framework**.
3.  Abra o **todas as políticas** painel.
4.  Selecione **carregar política**.
5.  Verifique **substituir a política se existir** caixa.
6.  **Carregar** TrustFrameworkExtensions.xml e certifique-se de que não falha a validação

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada utilizando o executar agora
1.  Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.

    >[!NOTE]
    >
    >    **Executar agora** requer, pelo menos, uma aplicação para preregistered no inquilino. 
    >    Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.


2.  Abra **B2C_1A_signup_signin**, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.
3.  Deverá conseguir iniciar sessão com a conta do Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registar o Google + conta fornecedor de afirmações para journey de utilizador de edição de perfil
Poderá pretender adicionar o Google + conta fornecedor de identidade também para o utilizador `ProfileEdit` journey de utilizador. Para disponibilizá-lo, iremos Repita os dois últimos passos:

### <a name="display-the-button"></a>Apresentar no botão
1.  Abra o ficheiro de extensão da sua política (por exemplo, TrustFrameworkExtensions.xml).
2.  Localizar o `<UserJourney>` nó que inclui `Id="ProfileEdit"` no journey utilizador que copiou.
3.  Localize o `<OrchestrationStep>` nó que inclui`Order="1"`
4.  Adicione o seguinte fragmento XML em `<ClaimsProviderSelections>` nó:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação
1.  Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
2.  Adicione o seguinte fragmento XML em `<ClaimsExchanges>` nó:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testar a política de edição de perfil personalizada utilizando o executar agora

1.  Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.
2.  Abra **B2C_1A_ProfileEdit**, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.
3.  Deverá conseguir iniciar sessão com a conta do Google +.

## <a name="download-the-complete-policy-files"></a>Transferir os ficheiros de política concluída
Opcional: Recomendamos que crie o seu cenário utilizando a sua própria política personalizada do ficheiros depois de concluir a introdução com as políticas personalizadas guiá-em vez de utilizar estes ficheiros de exemplo.  [Ficheiros de política de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
