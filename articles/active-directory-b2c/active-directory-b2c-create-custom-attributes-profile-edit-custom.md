---
title: "Azure Active Directory B2C: Adicionar os seus próprios atributos para as políticas personalizadas e utilizar no perfil editar | Microsoft Docs"
description: "Instruções sobre como utilizar as propriedades de extensão, atributos personalizados e incluindo-los na interface de utilizador"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: f3e4eb6fedf850dbb827fd2a10593249d2f17ef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>O Azure Active Directory B2C: Criar e utilizar atributos personalizados num perfil personalizado Editar política

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, pode criar um atributo personalizado no diretório do Azure AD B2C e utiliza este novo atributo como uma afirmação personalizada no journey de utilizador de Editar perfil.

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos no artigo [introdução com as políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Utilize os atributos personalizados para recolher informações sobre os seus clientes no Azure Active Directory B2C através de políticas personalizadas
Diretório do Azure Active Directory (Azure AD) B2C é fornecido com um conjunto de atributos incorporado: determinado nome, o apelido, cidade, Código Postal, userPrincipalName, etc.  Muitas vezes, terá de criar os seus próprios atributos.  Por exemplo:
* Uma aplicação orientado para o cliente tem de manter um atributo, tais como "LoyaltyNumber."
* Um fornecedor de identidade tem um identificador exclusivo do utilizador que tem de ser guardado como "uniqueUserGUID"."
* Necessita de um journey de utilizador personalizada manter o estado do utilizador como "migrationStatus."

Com o Azure AD B2C, pode expandir o conjunto de atributos armazenados em cada conta de utilizador. Também pode ler e escrever estes atributos utilizando o [AD Graph API do Azure](active-directory-b2c-devquickstarts-graph-dotnet.md).

As propriedades de extensão expandem o esquema dos objetos de utilizador no diretório.  A propriedade de extensão de termos de licenciamento, o atributo personalizado e afirmações personalizadas referem-se para a mesma coisa no contexto deste artigo e o nome varia consoante o contexto (aplicação, objeto, política).

As propriedades de extensão só podem ser registadas um objeto de aplicação, apesar de estes poderão conter dados para um utilizador. A propriedade está ligada à aplicação. O objeto de aplicação tem de ser concedido acesso de escrita para registar uma propriedade de extensão. 100 propriedades de extensão (em todos os tipos e todas as aplicações) podem ser escritas para qualquer objeto único. As propriedades de extensão são adicionados ao tipo de diretório de destino e torna-se imediatamente acessíveis no inquilino do diretório do Azure AD B2C.
Se a aplicação for eliminada, essas propriedades de extensão, juntamente com quaisquer dados contidos nos mesmos para todos os utilizadores também são removidas. Se uma propriedade de extensão é eliminada pela aplicação são removido os objetos de diretório de destino e os valores eliminados.

Propriedades da extensão existem apenas no contexto de uma aplicação registada no inquilino. O id de objeto dessa aplicação deve ser incluído no TechnicalProfile que a utilizam.

>[!NOTE]
>O diretório do Azure AD B2C, incluem normalmente uma aplicação Web com o nome `b2c-extensions-app`.  Esta aplicação é principalmente utilizada por políticas incorporadas do b2c para as afirmações personalizadas criadas através do portal do Azure.  É recomendado utilizar esta aplicação para registar as extensões de políticas personalizadas do b2c apenas para utilizadores avançados.  Instruções para isto são incluídas na secção passos neste artigo.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Criar uma nova aplicação para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com credenciais administrativas do diretório do B2C que pretende configurar.
1. Clique em **do Azure Active Directory** no menu de navegação esquerdo. Terá de considerar ao selecionar mais serviços >.
1. Selecione **registos de aplicação** e clique em **novo registo de aplicação**
1. Forneça as seguintes entradas recomendadas:
  * Especifique um nome para a aplicação web: **WebApp-GraphAPI-DirectoryExtensions**
  * Tipo de aplicação: app/API Web
  * Início de sessão URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Selecione * * criar. É apresentada a conclusão com êxito no **notificações**
1. Selecione a aplicação web recentemente criada: **WebApp-GraphAPI-DirectoryExtensions**
1. Selecionadas definições: **as permissões necessárias**
1. Selecionar a API **Windows Azure Active Directory**
1. Coloque uma marca de verificação nas permissões de aplicação: **leitura e escrita de dados de diretório**, e **guardar**
1. Escolha **conceder permissões** e confirme **Sim**.
1. Copie a sua área de transferência e guarde os seguintes identificadores de WebApp-GraphAPI-DirectoryExtensions > Definições > propriedades >
*  **ID da aplicação** . Exemplo:`103ee0e6-f92d-4183-b576-8c3739027780`
* **ID de objeto**. Exemplo:`80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modificar a política personalizada para adicionar o ApplicationObjectId

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
>O <TechnicalProfile Id="AAD-Common"> é referido como "comuns", porque os respetivos elementos são incluídos no e reutilizados em todas as do Azure Active Directory TechnicalProfiles utilizando o elemento:`<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>Quando escreve o TechnicalProfile pela primeira vez para a propriedade de extensão recentemente criado, pode deparar-se um erro de uso individual.  A propriedade de extensão é criada na primeira vez que é utilizado.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Utilizar a nova propriedade de extensão / personalizado o atributo em journey um utilizador


1. Abra o ficheiro de entidade Confiadora Party(RP) que descreve a política de editar journey de utilizador.  Se estiver a iniciar a, poderá ser aconselhável para transferir a versão do ficheiro RP PolicyEdit já configurada diretamente a partir da secção de política de personalizada de B2C do Azure no portal do Azure.  Em alternativa, abra o ficheiro XML a partir da sua pasta de armazenamento.
2. Adicionar uma afirmação personalizada `loyaltyId`.  Incluindo personalizada de afirmação no `<RelyingParty>` elemento, é transmitida como um parâmetro para o UserJourney TechnicalProfiles e incluída no token para a aplicação.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Adicione uma definição de afirmação para o ficheiro de política de extensão `TrustFrameworkExtensions.xml` dentro de `<ClaimsSchema>` elemento conforme mostrado.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Adicionar a mesma definição no ficheiro de Base de política de afirmações `TrustFrameworkBase.xml`.  
>Adicionar um `ClaimType` a definição de base e o ficheiro de extensões normalmente não é necessária, no entanto, uma vez que os passos seguintes irão adicionar o extension_loyaltyId ao TechnicalProfiles no ficheiro de Base, a validação de política irão rejeitar o carregamento do ficheiro de base sem ele.
>Poderá ser útil para a execução de journey o utilizador com o nome "ProfileEdit" no ficheiro TrustFrameworkBase.xml de rastreio.  Procure o journey de utilizador com o mesmo nome no seu editor e observar se Orchestration passo 5 invoca o TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate".  Procurar e inspecionar este TechnicalProfile para se familiarizar com o fluxo.
5. Adicionar loyaltyId como afirmações de entrada e saída no TechnicalProfile "SelfAsserted ProfileUpdate"
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Adicione afirmação TechnicalProfile "AAD-UserWriteProfileUsingObjectId" para manter o valor da afirmação de propriedade de extensão para o utilizador atual no diretório.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Adicione afirmação TechnicalProfile "AAD-UserReadUsingObjectId" ler o valor do atributo extensão sempre que um utilizador inicia sessão. Deste modo, até que ponto os TechnicalProfiles foram alterados no fluxo de apenas para contas locais.  Se o novo atributo for pretendido no fluxo de uma conta federada/social, um conjunto diferente de TechnicalProfiles tem de ser alterada. Consulte os passos seguintes.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>O elemento de IncludeTechnicalProfile adiciona todos os elementos do AAD comuns para este TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Testar a política personalizada utilizando "Executar agora"
1. Abra o **do Azure AD B2c** e navegue para **identidade experiência Framework > políticas personalizadas**.
1. Selecione a política personalizada que é carregado e clique em de **executar agora** botão.
1. Poderá inscrever-se utilizando um endereço de correio eletrónico.

O token de id enviado novamente à sua aplicação inclui a nova propriedade de extensão, como uma afirmação personalizada precedida por extension_loyaltyId. Consulte o exemplo.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passos seguintes

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Adicione a nova afirmação aos fluxos para inícios de sessão de conta de redes sociais alterando o TechnicalProfiles listados abaixo. Estes dois TechnicalProfiles são utilizadas pelo inícios de sessão federado/social conta para escrever e ler os dados de utilizador utilizando o alternativeSecurityId como o localizador de objeto de utilizador.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Utilizar os mesmos atributos de extensão entre as políticas incorporadas e personalizadas.
Quando adicionar atributos de extensão (também conhecido como atributos personalizados) através da experiência do portal, esses atributos são registados com o * *-extensões-aplicação b2c que existe na cada inquilino do b2c.  Para utilizar estes atributos de extensão na sua política personalizada:
1. Dentro do seu inquilino do b2c em portal.azure.com, navegue para **do Azure Active Directory** e selecione **registos de aplicação**
2. Localizar o **aplicação de extensões de b2c** e selecione-
3. Em registo 'Essentials' o **ID da aplicação** e **ID de objeto**
4. Inclui-los os seus metadados do perfil comum AAD técnica como da seguinte forma:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Para manter a consistência com a experiência do portal, criar estes atributos através da IU do portal da *antes* utilizá-los no seu políticas personalizadas.  Quando cria um atributo "ActivationStatus" no portal, tem fazem referência ao mesmo da seguinte forma:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referência

* A **perfil técnica (TP)** é um tipo de elemento que pode considerar como um *função* que define o nome de um ponto final, os metadados, o protocolo e fornece detalhes sobre a troca de afirmações que a identidade Experiência Framework deverá efetuar.  Quando isto *função* denomina-se um passo de orquestração ou de outro TechnicalProfile, o InputClaims e OutputClaims fornecidos como parâmetros pelo chamador.


* Para um total tratamento nas propriedades da extensão, consulte o artigo [EXTENSÕES de esquema de DIRETÓRIO | CONCEITOS DE API DO GRÁFICO](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Os atributos de extensão no Graph API são denominados utilizando a Convenção `extension_ApplicationObjectID_attributename`. As políticas personalizadas denominadas atributos de extensões extension_attributename, deste modo, omitindo ApplicationObjectId no XML
