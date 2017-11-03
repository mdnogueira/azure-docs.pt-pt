---
title: "Complexidade de palavra-passe em políticas personalizadas - Azure AD B2C | Microsoft Docs"
description: "Como configurar os requisitos de complexidade de palavras-passe numa política personalizada"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: c28a1d30127956f6cc589b1efef6bff12117e3f1
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="configure-password-complexity-in-custom-policies"></a>Configurar a complexidade da palavra-passe em políticas personalizadas

> [!NOTE]
> **Esta funcionalidade está em pré-visualização.**  Contacte [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) ter o seu inquilino de teste ativado com esta funcionalidade.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo é uma descrição avançada como funciona a complexidade de palavra-passe e é ativado através de políticas personalizadas do Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>O Azure AD B2C: Configurar os requisitos de complexidade de palavras-passe

O Azure Active Directory B2C (Azure AD B2C) suporta alterar os requisitos de complexidade de palavras-passe fornecidas por um utilizador final ao criar uma conta.  Por predefinição, o Azure AD B2C utiliza **forte** palavras-passe.  Azure AD B2C suporta também para controlar a complexidade de palavras-passe que os clientes podem utilizar as opções de configuração.  Este artigo aborda como configurar a complexidade de palavra-passe em políticas personalizadas.  Também é possível utilizar [configurar a complexidade da palavra-passe nas políticas incorporadas](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Pré-requisitos

Um inquilino do Azure AD B2C configurado para concluir uma conta local sessão-up/início de sessão, conforme descrito em [introdução](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Como configurar a complexidade de palavra-passe numa política personalizada

Para configurar a complexidade da palavra-passe numa política personalizada, a estrutura geral da sua política personalizada tem de incluir um `ClaimsSchema`, `Predicates`, e `InputValidations` elemento dentro `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

O objetivo destes elementos é o seguinte:

- Cada `Predicate` elemento define uma verificação de validação da cadeia básico que devolve true ou false.
- O `InputValidations` elemento tem um ou mais `InputValidation` elementos.  Cada `InputValidation` é criado utilizando uma série de `Predicate` elementos. Este elemento permite-lhe efetuar agregações booleanos (semelhante ao `and` e `or`).
- O `ClaimsSchema` define que afirmações está a ser validada.  Em seguida, define qual `InputValidation` regra é utilizada para validar que afirmações.

### <a name="defining-a-predicate-element"></a>Definir um elemento de predicado

Os predicados de têm dois tipos de método: IsLengthRange ou MatchesRegex. Vamos rever um exemplo de cada.  Primeiro temos um exemplo de MatchesRegex, que é utilizado para corresponder a uma expressão regular.  Neste exemplo, corresponde a cadeia que contém números.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Seguinte vamos rever um exemplo de IsLengthRange.  Este método aceita um comprimento da cadeia mínimo e máximo.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Utilize o `HelpText` atributo para fornecer uma mensagem de erro para os utilizadores finais se falhar a verificação.  Esta cadeia pode ser localizada utilizando o [funcionalidade de personalização de idioma](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definir um elemento de InputValidation

Um `InputValidation` é uma agregação de `PredicateReferences`. Cada `PredicateReferences` tem de ser verdadeira para que o `InputValidation` com êxito.  No entanto, no interior do `PredicateReferences` chamado um atributo de utilização de elemento `MatchAtLeast` para especificar quantos `PredicateReference` verificações tem de devolver verdadeiras.  Opcionalmente, defina um `HelpText` atributo para substituir a mensagem de erro definido no `Predicate` elementos que referencia.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Definir um elemento de ClaimsSchema

Os tipos de afirmação `newPassword` e `reenterPassword` são considerados especial, pelo que não altere os nomes.  A IU valida o utilizador reentered corretamente a palavra-passe durante a criação de conta com base nestes `ClaimType` elementos.  Para localizar o mesmo `ClaimType` elementos, procure no TrustFrameworkBase.xml no seu pacote de arranque.  Quais são as novidades neste exemplo é a substituição destes elementos para definir um `InputValidationReference`. O `ID` atributo deste elemento nova está a apontar para o `InputValidation` elemento definido.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Passar todos os em conjunto

Este exemplo mostra como todas as peças encaixam para formar uma política de trabalho.  Para utilizar este exemplo:

1. Siga as instruções no pré-requisito [introdução](active-directory-b2c-get-started-custom.md) para transferir, configurar e carregue TrustFrameworkBase.xml e TrustFrameworkExtensions.xml
1. Crie um ficheiro de SignUporSignIn.xml utilizando o conteúdo de exemplo nesta secção.
1. Atualizar SignUporSignIn.xml substituir `yourtenant` com o nome do seu inquilino do Azure AD B2C.
1. Carregue o ficheiro de política de SignUporSignIn.xml pela última vez.

Este exemplo contém uma validação de palavras-passe do pin e outro para palavras-passe seguras:

- Procure `PINpassword`. Isto `InputValidation` elemento valida um pin de qualquer comprimento.  Não é utilizado neste momento, porque este não está a ser referenciado no `InputValidationReference` elemento dentro `ClaimType`. 
- Procure `PasswordValidation`. Isto `InputValidation` elemento valida uma palavra-passe é de 8 a 16 carateres e contém 3 de 4 de números, letras maiúsculas, minúsculos ou símbolos.  Este é referenciado numa `ClaimType`.  Por conseguinte, esta regra está a ser imposta nesta política.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
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
</TrustFrameworkPolicy>
```
