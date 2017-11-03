---
title: "O Azure Active Directory B2C: Adicionar um fornecedor do Azure AD através da utilização de políticas personalizadas | Microsoft Docs"
description: "Saiba mais sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c073d70debfdc3560405955d65fa9ccaa7d8b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>O Azure Active Directory B2C: Início de sessão através da utilização de contas do Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar o início de sessão para os utilizadores de uma organização específico do Azure Active Directory (Azure AD) através da utilização de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Concluir os passos a [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) artigo.

Estes passos incluem:

1. Criar um Azure Active Directory B2C inquilino (Azure AD B2C).
2. Criar uma aplicação do Azure AD B2C.
3. Registar duas aplicações de motor de política.
4. Definição de segurança das chaves.
5. Configurar o pacote de arranque.

## <a name="create-an-azure-ad-app"></a>Criar uma aplicação do Azure AD

Para ativar o início de sessão para utilizadores específicos de um organização do Azure AD, tem de registar uma aplicação dentro da organização inquilino do Azure AD.

>[!NOTE]
> Utilizamos "contoso.com" para o organizacional inquilino do Azure AD e "fabrikamb2c.onmicrosoft.com" do inquilino do Azure AD B2C nas instruções seguintes.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione a sua conta. Do **diretório** lista, escolha o organizacional inquilino do Azure AD, onde é necessário registar a sua aplicação (contoso.com).
1. Selecione **mais serviços** no painel esquerdo e procure "Registos de aplicação".
1. Selecione **novo registo de aplicação**.
1. Introduza um nome para a sua aplicação (por exemplo, `Azure AD B2C App`).
1. Selecione **aplicação Web / API** para o tipo de aplicação.
1. Para **URL de início de sessão**, introduza o seguinte URL onde `yourtenant` é substituído pelo nome do seu inquilino do Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Guardar o ID da aplicação.
1. Selecione a aplicação criada recentemente.
1. Sob o **definições** painel, selecione **chaves**.
1. Criar uma nova chave e guarde-o. Irá utilizá-lo nos passos na secção seguinte.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Adicionar a chave do Azure AD para o Azure AD B2C

Tem de armazenar a chave de aplicação contoso.com no seu inquilino do Azure AD B2C. Para efetuar este procedimento:
1. Aceda ao seu inquilino do Azure AD B2C e selecione **definições do B2C** > **identidade experiência Framework** > **política chaves**.
1. Selecione **+ adicionar**.
1. Selecione ou introduza estas opções:
   * Selecione **Manual**.
   * Para **nome**, escolha um nome que corresponde ao nome do seu inquilino do Azure AD (por exemplo, `ContosoAppSecret`).  O prefixo `B2C_1A_` é adicionado automaticamente para o nome da sua chave.
   * Colar a chave de aplicação no **segredo** caixa.
   * Selecione **assinatura**.
1. Selecione **Criar**.
1. Confirme que criou a chave `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Adicionar um fornecedor de afirmações na política de base

Se pretender que os utilizadores para iniciar sessão utilizando o Azure AD, terá de definir do Azure AD como um fornecedor de afirmações. Por outras palavras, tem de especificar um ponto final que o Azure AD B2C irão comunicar com. O ponto final irá fornecer um conjunto de afirmações que são utilizados pelo Azure AD B2C para verificar que um utilizador específico foi autenticado. 

Pode definir do Azure AD como um fornecedor de afirmações, adicionando o Azure AD para o `<ClaimsProvider>` no ficheiro de extensão da sua política de nó:

1. Abra o ficheiro de extensão (TrustFrameworkExtensions.xml) a partir do diretório de trabalho.
1. Localizar o `<ClaimsProviders>` secção. Se não existir, adicione-a sob o nó de raiz.
1. Adicione um novo `<ClaimsProvider>` nó da seguinte forma:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
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

1. Sob o `<ClaimsProvider>` nó, atualize o valor para `<Domain>` para um valor exclusivo que pode ser utilizado para distinguir de outros fornecedores de identidade.
1. Sob o `<ClaimsProvider>` nó, atualize o valor para `<DisplayName>` para um nome amigável para o fornecedor de afirmações. Este valor não é utilizado atualmente.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token do ponto final do Azure AD, terá de definir os protocolos que o Azure AD B2C devem utilizar para comunicar com o Azure AD. Isto é feito no interior do `<TechnicalProfile>` elemento do `<ClaimsProvider>`.
1. Atualizar o ID do `<TechnicalProfile>` nós. Este ID é utilizado para fazer referência a este perfil técnica de outras partes da política.
1. Atualize o valor para `<DisplayName>`. Este valor será apresentado no botão de início de sessão no seu ecrã de início de sessão.
1. Atualize o valor para `<Description>`.
1. AD do Azure utiliza o protocolo OpenID Connect, por isso, certifique-se de que o valor para `<Protocol>` é `"OpenIdConnect"`.

Tem de atualizar o `<Metadata>` secção no ficheiro XML referida anteriormente para refletir as definições de configuração específicas do seu inquilino do Azure AD. No ficheiro XML, atualize os valores de metadados da seguinte forma:

1. Definir `<Item Key="METADATA">` para `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, onde `yourAzureADtenant` é o nome do seu inquilino do Azure AD (contoso.com).
1. Abra o browser e aceda ao `METADATA` URL que acabou de atualizar.
1. No browser, procure o objeto 'emissor' e copie o valor. Deve ser semelhante ao seguinte: `https://sts.windows.net/{tenantId}/`.
1. Colar o valor para `<Item Key="ProviderName">` no ficheiro XML.
1. Definir `<Item Key="client_id">` para o ID da aplicação do registo de aplicação.
1. Definir `<Item Key="IdTokenAudience">` para o ID da aplicação do registo de aplicação.
1. Certifique-se de que `<Item Key="response_types">` está definido como `id_token`.
1. Certifique-se de que `<Item Key="UsePolicyInRedirectUri">` está definido como `false`.

Também precisa de associar o segredo do Azure AD que registou no seu inquilino do Azure AD B2C para o Azure AD `<ClaimsProvider>` informações:

* No `<CryptographicKeys>` secção no ficheiro XML anterior, atualize o valor para `StorageReferenceId` para o ID de referência do segredo que definiu (por exemplo, `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Carregue o ficheiro de extensão para verificação

Por agora, configurou a política de modo a que o Azure AD B2C sabe como comunicar com o seu diretório do Azure AD. Tente carregar o ficheiro de extensão da sua política apenas para confirmar que não tem quaisquer problemas até ao momento. Para tal:

1. Abra o **todas as políticas** painel no seu inquilino do Azure AD B2C.
1. Verifique o **substituir a política se existir** caixa.
1. Carregue o ficheiro de extensão (TrustFrameworkExtensions.xml) e certifique-se de que não falha a validação.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registar o fornecedor de afirmações do AD do Azure para um journey de utilizador

Agora tem de adicionar o fornecedor de identidade do Azure AD para uma das suas percursos de utilizador. Neste momento, o fornecedor de identidade tiver sido configurado, mas não está disponível em nenhum dos ecrãs de sessão-up/início de sessão. Para tornar disponível, iremos criar um duplicado de um existente journey de utilizador do modelo e, em seguida, modificá-lo para que ela também tem o fornecedor de identidade do Azure AD:

1. Abra o ficheiro de base da sua política (por exemplo, TrustFrameworkBase.xml).
1. Localizar o `<UserJourneys>` elemento e copie todo o `<UserJourney>` nó que inclui `Id="SignUpOrSignIn"`.
1. Abra o ficheiro de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o `<UserJourneys>` elemento. Se o elemento não existir, adicione uma.
1. Cole a toda a `<UserJourney>` nó que copiou como subordinado do `<UserJourneys>` elemento.
1. Mudar o nome de ID de journey de utilizador novo (por exemplo, mudar o nome como `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Apresentar o botão""

O `<ClaimsProviderSelection>` elemento é semelhante a um botão do fornecedor de identidade num ecrã de início de sessão-up/início de sessão. Se adicionar um `<ClaimsProviderSelection>` elemento para o Azure AD, um novo botão aparece quando um utilizador lands na página. Para adicionar este elemento:

1. Localizar o `<OrchestrationStep>` nó que inclui `Order="1"` no journey utilizador que acabou de criar.
1. Adicione o seguinte:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Definir `TargetClaimsExchangeId` para um valor adequado. Recomendamos seguindo a mesma Convenção como outros:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>O botão de ligação para uma ação

Agora que tem um botão no local, terá de ligá-la a uma ação. Neste caso, é a ação para o Azure AD B2C comunicar com o Azure AD para receber um token. O botão de ligação para uma ação associando o perfil técnico para o seu fornecedor de afirmações do Azure AD:

1. Localizar o `<OrchestrationStep>` que inclua `Order="2"` no `<UserJourney>` nós.
1. Adicione o seguinte:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Atualização `Id` para o mesmo valor da `TargetClaimsExchangeId` na secção anterior.
1. Atualização `TechnicalProfileReferenceId` para o ID do perfil técnico que criou anteriormente (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Carregue o ficheiro de extensão atualizado

Tiver terminado a modificação do ficheiro de extensão. Guardá-lo. Em seguida, carregue o ficheiro e certifique-se de que todas as validações êxito.

### <a name="update-the-rp-file"></a>Atualizar o ficheiro RP

Agora tem de atualizar o ficheiro de terceiros (RP) entidade confiadora que iniciará a journey de utilizador que acabou de criar:

1. Fazer uma cópia de SignUpOrSignIn.xml no seu diretório de trabalho e mude o nome (por exemplo, mude o nome para SignUpOrSignInWithAAD.xml).
1. Abra o ficheiro novo e a atualização a `PolicyId` atributo para `<TrustFrameworkPolicy>` com um valor exclusivo (por exemplo, SignUpOrSignInWithAAD). <br> Este será o nome da sua política (por exemplo, B2C\_1A\_SignUpOrSignInWithAAD).
1. Modificar o `ReferenceId` atributo em `<DefaultUserJourney>` para corresponder ao ID do novo journey de utilizador que criou (SignUpOrSignUsingContoso).
1. Guardar as alterações e carregar o ficheiro.

## <a name="troubleshooting"></a>Resolução de problemas

Testar a política personalizada que acabou de carregar ao abrir o painel e clicar em **executar agora**. Para diagnosticar problemas, leia sobre [resolução de problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Passos seguintes

Fornecer comentários para [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
