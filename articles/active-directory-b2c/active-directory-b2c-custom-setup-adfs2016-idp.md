---
title: "O Azure Active Directory B2C: Adicionar ADFS como um fornecedor de identidade através de políticas personalizadas"
description: "Um artigo de procedimentos sobre como configurar 2016 do AD FS utilizando o protocolo SAML e as políticas personalizadas"
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
ms.openlocfilehash: 8713fc7dd27023e1244ccb00673dd1652689baf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>O Azure Active Directory B2C: Adicionar ADFS como um fornecedor de identidade através de políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o início de sessão para os utilizadores da conta do AD FS através da utilização de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

Estes passos incluem:

1.  Criar uma confiança de entidade Confiadora de ADFS.
2.  Adiciona o certificado do ADFS confiança de entidade Confiadora do Azure AD B2C.
3.  A adicionar o fornecedor de afirmações para uma política.
4.  Registar o ADFS conta afirmações do fornecedor para um journey de utilizador.
5.  Carregar a política para um Azure AD B2C inquilino e testá-lo.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Para criar um suporte para afirmações de confiança da entidade Confiadora

Para utilizar o AD FS como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar um ADFS confiança da entidade Confiadora e forneça-lo com os parâmetros corretos.

Para adicionar uma nova confiança de entidade confiadora, utilizando o snap-in Gestão do AD FS e configurar manualmente as definições, execute o seguinte procedimento num servidor de Federação.

A associação **administradores**, ou equivalente, no computador local é o mínimo requerido para concluir este procedimento. Reveja os detalhes sobre como utilizar as contas apropriadas e as associações em [locais e grupos predefinidos do domínio](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  No Gestor de servidor, clique em **ferramentas**e, em seguida, selecione **ADFS gestão**.

2.  Clique em **adicionar confiança da entidade Confiadora**.
    ![Adicionar confiança da entidade Confiadora](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  No **boas-vindas** página, escolha **com suporte para afirmações** e clique em **iniciar**.
    ![Na página de boas-vindas, escolha com suporte para afirmações](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  No **selecionar origem de dados** página, clique em **introduzir dados sobre a entidade confiadora manualmente**e, em seguida, clique em **seguinte**.
    ![Introduzir dados sobre a entidade confiadora](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  No **Especificar nome a apresentar** , escreva um nome na **nome a apresentar**, em **notas** escreva uma descrição para esta confiança da entidade confiadora e, em seguida, clique em **seguinte**.
    ![Especifique o nome a apresentar e notas](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Opcional. Se tiver um certificado de encriptação de tokens opcional, em seguida, o **configurar certificado** página, clique em **procurar** para localizar o ficheiro de certificado e, em seguida, clique em **seguinte**.
    ![Configurar o certificado](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  No **configurar URL** página, selecione o **ativar o suporte para o protocolo SAML 2.0 WebSSO** caixa de verificação. Em **URL de serviço SAML 2.0 SSO da entidade Confiadora**, escreva o URL de ponto final do serviço de Security Assertion Markup Language (SAML) para esta confiança da entidade confiadora e, em seguida, clique em **seguinte**.  Para o **URL de serviço SAML 2.0 SSO da entidade Confiadora**, cole o `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Substitua {inquilino} com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com) e substitua a {política} com o nome da política extensões (por exemplo, B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >O nome da política é o signup_or_signin política herda, neste caso é: `B2C_1A_TrustFrameworkExtensions`.
    >Por exemplo o URL pode ser: https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![URL do serviço SAML 2.0 SSO de entidade confiadora](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. No **configurar identificadores** página, especifique o mesmo URL que o passo anterior, clique em **adicionar** para adicioná-los à lista e, em seguida, clique em **seguinte**.
    ![Entidade confiadora identificadores de confiança de entidade](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  No **escolha política de controlo de acesso** selecionar uma política e clique em **seguinte**.
    ![Escolha a política de controlo de acesso](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  No **pronto para adicionar confiança** página, reveja as definições e, em seguida, clique em **seguinte** para guardar a entidade confiadora informações sobre a confiança.
    ![Guardar as informações de confiança de entidade entidade confiadora](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  No **concluir** página, clique em **fechar**, esta ação apresenta automaticamente a **editar regras de afirmação** caixa de diálogo.
    ![Editar regras de afirmação](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Clique em **Adicionar regra**.  
      ![Adicionar nova regra](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  No **modelo de regra de afirmação**, selecione **enviar atributos LDAP como afirmações**.
    ![Selecione enviar atributos LDAP como regra de modelo de afirmações](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Fornecer **nome da regra de afirmação**. Para o **arquivo de atributos** selecione **selecione Active Directory** adicionar as seguintes afirmações, em seguida, clique em **concluir** e **OK**.
    ![Defina as propriedades de regra](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  No Gestor de servidor, selecione **confianças da entidade Confiadora** , em seguida, selecione a entidade confiadora confia que criou e clique em **propriedades**.
    ![Entidade confiadora propriedades de edição de terceiros](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Um clique a janela de propriedades de fidedignidade (B2C demonstração) de terceiros entidade confiadora **assinatura** separador e clique em **adicionar**.  
    ![Assinatura de conjunto](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Adicione o certificado de assinatura (ficheiro .cert, sem chave privada).  
    ![Adicionar o certificado de assinatura](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  Na janela de propriedades de fidedignidade (B2C demonstração) de terceiros entidade confiadora clique **avançadas** separador e altere o **o algoritmo hash seguro** para **SHA-1**, clique em **Ok**.  
    ![Definir o algoritmo hash seguro para SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Adicione a chave de aplicação de conta do AD FS para o Azure AD B2C
A Federação com contas do AD FS requer um segredo do cliente para a conta do AD FS para confiança do Azure AD B2C em nome da aplicação. Tem de armazenar o certificado do AD FS no seu inquilino do Azure AD B2C. 

1.  Aceda ao seu inquilino do Azure AD B2C e selecione **definições do B2C** > **Framework de experiência de identidade**
2.  Selecione **política chaves** para ver as chaves disponíveis no seu inquilino.
3.  Clique em **+ adicionar**.
4.  Para **opções**, utilize **carregar**.
5.  Para **nome**, utilize `ADFSSamlCert`.  
    O prefixo `B2C_1A_` podem ser adicionados automaticamente.
6.  No carregamento de ficheiros, * * selecionar o ficheiro de. pfx do certificado com chave privada. Nota: este certificado (com a chave privada) deve ser o mesmo que emitido e utilizado para a AD FS entidade confiadora.
![Carregar política chave](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Clique em **Criar**.
8.  Confirme que criou a chave `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um fornecedor de afirmações na política de extensão
Se pretender que os utilizadores para iniciar sessão utilizando a conta do AD FS, tem de definir a conta ADFS como um fornecedor de afirmações. Por outras palavras, tem de especificar um ponto final que o Azure AD B2C comunica com. O ponto final fornece um conjunto de afirmações que são utilizados pelo Azure AD B2C para verificar que um utilizador específico foi autenticado.

Definir o ADFS como um fornecedor de afirmações, adicionando `<ClaimsProvider>` nó no seu ficheiro de política de extensão:

1. Abra o ficheiro de política de extensão (TrustFrameworkExtensions.xml) do diretório de trabalho. Se precisar de um editor de XML, [tente Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma simples.
2. Localizar o `<ClaimsProviders>` secção
3. Adicione o seguinte fragmento XML sob o `ClaimsProviders` elemento e substitua `identityProvider` com o seu DNS (valor arbitrário que indica o seu domínio) e guarde o ficheiro. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registar o fornecedor de afirmações do ADFS conta início de sessão se ou iniciar sessão no journey de utilizador
Neste momento, o fornecedor de identidade foi configurado.  No entanto, não está disponível em nenhum dos ecrãs de sessão-up/início de sessão. Agora tem de adicionar o fornecedor de identidade de conta do AD FS para o utilizador `SignUpOrSignIn` journey de utilizador. Para tornar disponível, criamos um duplicado de um existente journey de utilizador do modelo.  Em seguida, iremos modificá-lo para que inclui o fornecedor de identidade do AD FS:

>[!NOTE]
>Se anteriormente tiver copiado o `<UserJourneys>` elemento a partir do ficheiro de base da sua política para o ficheiro de extensão (TrustFrameworkExtensions.xml), pode ignorar esta secção.

1.  Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).
2.  Localizar o `<UserJourneys>` elemento e copie todo o conteúdo de `<UserJourneys>` nós.
3.  Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o `<UserJourneys>` elemento. Se o elemento não existir, adicione uma.
4.  Colar o conteúdo completo de `<UserJournesy>` nó que copiou como subordinado do `<UserJourneys>` elemento.

### <a name="display-the-button"></a>Apresentar no botão
O `<ClaimsProviderSelections>` elemento define a lista de opções de seleção de fornecedor de afirmações e a sua ordem.  `<ClaimsProviderSelection>`elemento é semelhante a um botão do fornecedor de identidade numa página sessão-up/início de sessão. Se adicionar um `<ClaimsProviderSelection>` elemento para a conta do AD FS, um novo botão aparece quando um utilizador lands na página. Para adicionar este elemento:

1.  Localizar o `<UserJourney>` nó que inclui `Id="SignUpOrSignIn"` no journey utilizador que copiou.
2.  Localize o `<OrchestrationStep>` nó que inclui`Order="1"`
3.  Adicione o seguinte fragmento XML em `<ClaimsProviderSelections>` nó:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação

Agora que tem um botão no local, terá de ligá-la a uma ação. Neste caso, é a ação para o Azure AD B2C comunicar com a conta do ADFS para receber um token. O botão de ligação para uma ação associando o perfil técnico para o fornecedor de afirmações do ADFS conta:

1.  Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
2.  Adicione o seguinte fragmento XML em `<ClaimsExchanges>` nó:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Certifique-se a `Id` tem o mesmo valor da `TargetClaimsExchangeId` na secção anterior.
> * Certifique-se `TechnicalProfileReferenceId` está definido para o perfil de técnico que criou anteriormente (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política para o seu inquilino
1.  No [portal do Azure](https://portal.azure.com), mudar para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e abra o **do Azure AD B2C** painel.
2.  Selecione **identidade experiência Framework**.
3.  Abra o **todas as políticas** painel.
4.  Selecione **carregar política**.
5.  Verifique **substituir a política se existir** caixa.
6.  **Carregar** TrustFrameworkExtensions.xml e certifique-se de que não falha a validação

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada utilizando o executar agora
1.  Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.
2.  Abra **B2C_1A_signup_signin**, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.
3.  Deverá conseguir iniciar sessão com a conta ADFS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registar o fornecedor de afirmações do ADFS conta para journey de utilizador de edição de perfil
Poderá pretender adicionar o fornecedor de identidade da conta ADFS também para o utilizador `ProfileEdit` journey de utilizador. Para disponibilizá-lo, iremos Repita os dois últimos passos:

### <a name="display-the-button"></a>Apresentar no botão
1.  Abra o ficheiro de extensão da sua política (por exemplo, TrustFrameworkExtensions.xml).
2.  Localizar o `<UserJourney>` nó que inclui `Id="ProfileEdit"` no journey utilizador que copiou.
3.  Localize o `<OrchestrationStep>` nó que inclui`Order="1"`
4.  Adicione o seguinte fragmento XML em `<ClaimsProviderSelections>` nó:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação
1.  Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
2.  Adicione o seguinte fragmento XML em `<ClaimsExchanges>` nó:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testar a política de edição de perfil personalizada utilizando o executar agora
1.  Abra **definições do Azure AD B2C** e aceda a **identidade experiência Framework**.
2.  Abra **B2C_1A_ProfileEdit**, entidade confiadora (RP) de terceiros política personalizada do que carregou. Selecione **executar agora**.
3.  Deverá conseguir iniciar sessão com a conta ADFS.

## <a name="download-the-complete-policy-files"></a>Transferir os ficheiros de política concluída
Opcional: Recomendamos que crie o seu cenário utilizando a sua própria política personalizada do ficheiros depois de concluir a introdução com as políticas personalizadas guiá. [Ficheiros de política de exemplo para referência apenas](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
