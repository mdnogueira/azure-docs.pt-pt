---
title: "O Azure Active Directory B2C: Adicionar um fornecedor de SAML do Salesforce ao utilizar políticas personalizadas | Microsoft Docs"
description: "Saiba mais sobre como criar e gerir políticas personalizadas do Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.openlocfilehash: 269cbd80fb6e861fa8588025eec70b6c6e2890d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>O Azure Active Directory B2C: Início de sessão através da utilização de contas do Salesforce através de SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como utilizar [políticas personalizadas](active-directory-b2c-overview-custom.md) para configurar o início de sessão para os utilizadores de uma organização específico do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-ad-b2c-setup"></a>Configuração do Azure AD B2C

Certifique-se de que concluiu todos os passos que mostram como para [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) no Azure Active Directory B2C (Azure AD B2C).

Estas incluem:

* Crie um inquilino do Azure AD B2C.
* Crie uma aplicação do Azure AD B2C.
* Registe duas aplicações de motor de política.
* Configure as chaves.
* Configure o pacote de arranque.

### <a name="salesforce-setup"></a>Configuração do Salesforce

Neste artigo, partimos do pressuposto que já efetuou o seguinte:

* Inscrito numa conta do Salesforce. Pode inscrever-se para obter um [conta de programador edição gratuita](https://developer.salesforce.com/signup).
* [Configurar um domínio My](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para a sua organização do Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Configurar o Salesforce para que os utilizadores podem federar

Para ajudar a comunicar com o Salesforce do Azure AD B2C, tem de obter o URL de metadados do Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um fornecedor de identidade

> [!NOTE]
> Neste artigo, partimos do pressuposto que está a utilizar [Salesforce Lightning experiência](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Inicie sessão no Salesforce](https://login.salesforce.com/). 
2. No menu da esquerda, em **definições**, expanda **identidade**e, em seguida, clique em **fornecedor de identidade**.
3. Clique em **ativar o fornecedor de identidade**.
4. Em **selecionar o certificado**, selecione o certificado que pretende que o Salesforce para utilizar para comunicar com o Azure AD B2C. (Pode utilizar o certificado predefinido.) Clique em **Guardar**. 

### <a name="create-a-connected-app-in-salesforce"></a>Criar uma aplicação ligada no Salesforce

1. No **fornecedor de identidade** página, aceda a **fornecedores de serviços**.
2. Clique em **fornecedores de serviços agora são criados através de aplicações ligadas. Clique aqui.**
3. Em **informações básicas**, introduza os valores necessários para a sua aplicação ligada.
4. Em **definições da aplicação Web**, selecione o **ativar SAML** caixa de verificação.
5. No **ID de entidade** campo, introduza o seguinte URL. Certifique-se de que substitua o valor para `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. No **ACS URL** campo, introduza o seguinte URL. Certifique-se de que substitua o valor para `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Deixe os valores predefinidos para todas as outras definições.
8. Desloque-se na parte inferior da lista e, em seguida, clique em **guardar**.

### <a name="get-the-metadata-url"></a>Obter o URL de metadados

1. Na página Descrição geral da sua aplicação ligada, clique em **gerir**.
2. Copie o valor para **ponto final de deteção de metadados**e guarde-o. Irá utilizá-lo neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar utilizadores de Salesforce para federar

1. No **gerir** página do seu ligado aplicação, aceda a **perfis**.
2. Clique em **gerir perfis**.
3. Selecione os perfis (ou grupos de utilizadores) que pretende federar com o Azure AD B2C. Como um administrador de sistema, selecione o **administrador de sistema** caixa de verificação, para que podem federar utilizando a conta do Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Gerar um certificado de assinatura para o Azure AD B2C

Pedidos enviados para o Salesforce tem de ser assinados pelo Azure AD B2C. Para gerar um certificado de assinatura, abra o Azure PowerShell e, em seguida, execute os seguintes comandos.

> [!NOTE]
> Certifique-se que Atualize o nome de inquilino e a palavra-passe nas linhas de top dois.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Adicionar o certificado de assinatura de SAML ao Azure AD B2C

Carregar o certificado de assinatura para o seu inquilino do Azure AD B2C: 

1. Aceda ao seu inquilino do Azure AD B2C. Clique em **definições** > **identidade experiência Framework** > **política chaves**.
2. Clique em **+ adicionar**e, em seguida:
    1. Clique em **opções** > **carregar**.
    2. Introduza um **nome** (por exemplo, SAMLSigningCert). O prefixo *B2C_1A_* é automaticamente adicionada ao nome da sua chave.
    3. Para selecionar o certificado, selecione **carregar o controlo de ficheiro**. 
    4. Introduza a palavra-passe do certificado que definiu no PowerShell script.
3. Clique em **Criar**.
4. Certifique-se de que criou uma chave (por exemplo, B2C_1A_SAMLSigningCert). Tome nota do nome completo (incluindo *B2C_1A_*). Irá referir-se para esta chave mais tarde na política.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Criar o fornecedor de afirmações de SAML do Salesforce na política de base

Tem de definir o Salesforce como um fornecedor de afirmações para que os utilizadores podem iniciar sessão utilizando o Salesforce. Por outras palavras, tem de especificar o ponto final que o Azure AD B2C irão comunicar com. O ponto final será *fornecer* um conjunto de *afirmações* que utiliza o Azure AD B2C para verificar que um utilizador específico foi autenticado. Para tal, adicione um `<ClaimsProvider>` para Salesforce no ficheiro de extensão da sua política:

1. No seu diretório de trabalho, abra o ficheiro de extensão (TrustFrameworkExtensions.xml).
2. Localizar o `<ClaimsProviders>` secção. Se não existir, crie-a sob o nó de raiz.
3. Adicione um novo `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

Sob o `<ClaimsProvider>` nó:

1. Altere o valor de `<Domain>` para um valor exclusivo que distingue `<ClaimsProvider>` partir de outros fornecedores de identidade.
2. Atualize o valor para `<DisplayName>` para um nome a apresentar para o fornecedor de afirmações. Atualmente, este valor não é utilizado.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um SAML token do Salesforce, defina os protocolos que irá utilizar o Azure AD B2C para comunicar com o Azure Active Directory (Azure AD). Fazê-lo `<TechnicalProfile>` elemento do `<ClaimsProvider>`:

1. Atualizar o ID do `<TechnicalProfile>` nós. Este ID é utilizado para fazer referência a este perfil técnica de outras partes da política.
2. Atualize o valor para `<DisplayName>`. Este valor é apresentado no botão de início de sessão na sua página de início de sessão.
3. Atualize o valor para `<Description>`.
4. Salesforce utiliza o protocolo SAML 2.0. Certifique-se de que o valor para `<Protocol>` é **SAML2**.

Atualização do `<Metadata>` secção no XML anterior para refletir as definições para a sua conta Salesforce específica. No XML, atualize os valores de metadados:

1. Atualize o valor de `<Item Key="PartnerEntity">` com o URL de metadados do Salesforce que copiou anteriormente. Tem o seguinte formato: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. No `<CryptographicKeys>` secção, atualize o valor para ambas as instâncias de `StorageReferenceId` para o nome da chave do seu certificado de assinatura (por exemplo, B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Carregue o ficheiro de extensão para verificação

A política está agora configurada para que o Azure AD B2C sabe como comunicar com o Salesforce. Tente carregar o ficheiro de extensão da sua política, para verificar que sabemos de quaisquer problemas até ao momento. Carregar o ficheiro de extensão da sua política:

1. No seu inquilino do Azure AD B2C, vá para o **todas as políticas** painel.
2. Selecione o **substituir a política se existir** caixa de verificação.
3. Carregue o ficheiro de extensão (TrustFrameworkExtensions.xml). Certifique-se de que não a falhar validação.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registar o fornecedor de afirmações de SAML do Salesforce ao journey utilizador

Em seguida, adicione o fornecedor de identidade do Salesforce SAML para um dos seus percursos de utilizador. Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em qualquer uma das páginas de inscrição ou início de sessão de utilizador. Para adicionar o fornecedor de identidade para uma página de início de sessão, em primeiro lugar, crie um duplicado de um existente journey de utilizador do modelo. Em seguida, modifique o modelo para que ela também tem o fornecedor de identidade do Azure AD.

1. Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).
2. Localizar o `<UserJourneys>` elemento e, em seguida, copie todo o `<UserJourney>` valor, incluindo o Id = "SignUpOrSignIn".
3. Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml). Localizar o `<UserJourneys>` elemento. Se o elemento não existir, crie uma.
4. Colar integralmente copiada `<UserJourney>` como subordinado do `<UserJourneys>` elemento.
5. Mudar o nome o ID da nova `<UserJourney>` (por exemplo, SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Apresentar o botão de fornecedor de identidade

O `<ClaimsProviderSelection>` elemento é semelhante a um botão do fornecedor de identidade numa página de inscrição ou início de sessão. Ao adicionar um `<ClaimsProviderSelection>` elemento do Salesforce, um novo botão é apresentado quando um utilizador passa a esta página. Para apresentar no botão de fornecedor de identidade:

1. No `<UserJourney>` que criou, localizar o `<OrchestrationStep>` com `Order="1"`.
2. Adicione o seguinte XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Definir `TargetClaimsExchangeId` para um valor lógico. Recomendamos seguindo a mesma Convenção como outras (por exemplo,  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Ligar o botão de fornecedor de identidade para uma ação

Agora que tem um botão do fornecedor de identidade no local, associá-lo a uma ação. Neste caso, a ação é para o Azure AD B2C comunicar com o Salesforce para receber um token SAML. Para fazer isto ao ligar o perfil técnico para o Salesforce SAML fornecedor de afirmações:

1. No `<UserJourney>` nó, localizar o `<OrchestrationStep>` com `Order="2"`.
2. Adicione o seguinte XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Atualização `Id` para o mesmo valor que utilizou anteriormente para `TargetClaimsExchangeId`.
4. Atualização `TechnicalProfileReferenceId` para o `Id` da técnica de perfil que criou anteriormente (por exemplo, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Carregue o ficheiro de extensão atualizado

Tiver terminado a modificação do ficheiro de extensão. Guardar e carregar este ficheiro. Certifique-se de que todas as validações êxito.

### <a name="update-the-relying-party-file"></a>Atualizar o ficheiro de terceiros entidade confiadora

Em seguida, atualize o ficheiro de terceiros (RP) entidade confiadora que inicia o journey de utilizador que criou:

1. Fazer uma cópia de SignUpOrSignIn.xml no seu diretório de trabalho. Em seguida, mude o nome (por exemplo, SignUpOrSignInWithAAD.xml).
2. Abra o ficheiro novo e a atualização a `PolicyId` atributo para `<TrustFrameworkPolicy>` com um valor exclusivo. Este é o nome da sua política (por exemplo, SignUpOrSignInWithAAD).
3. Modificar o `ReferenceId` atributo em `<DefaultUserJourney>` para fazer corresponder o `Id` do novo journey de utilizador que criou (por exemplo, SignUpOrSignUsingContoso).
4. Guardar as alterações e, em seguida, carregue o ficheiro.

## <a name="test-and-troubleshoot"></a>Testar e resolver problemas

Para testar a política de personalizada que acabou de carregar, no portal do Azure, aceda ao painel de política e, em seguida, clique em **executar agora**. Se falhar, consulte [resolver problemas de políticas personalizadas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passos seguintes

Fornecer comentários para [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
