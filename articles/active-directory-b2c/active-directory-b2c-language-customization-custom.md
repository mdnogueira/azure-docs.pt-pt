---
title: "Azure Active Directory B2C:Language personalização no políticas personalizadas | Microsoft Docs"
description: "Saiba como utilizar localizar conteúdo políticas personalizadas para vários idiomas"
services: active-directory-b2c
documentationcenter: 
author: sammak
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 11/13/2017
ms.author: sama
ms.openlocfilehash: 4ed9791d6590e3982a1bc79b96f8592995bc315c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
#<a name="language-customization-in-custom-policies"></a>Personalização de idioma no políticas personalizadas

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> 

As políticas personalizadas, a personalização de idioma funciona iguais às políticas incorporadas.  Consulte incorporada [documentação](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) que descreve o comportamento de como um idioma é escolhido com base nos parâmetros e definições do browser.

##<a name="enable-supported-languages"></a>Ativar suportado idiomas
Se não especificou as regiões de IU e o browser do utilizador pede-lhe uma destas linguagens, idiomas suportados são apresentados ao utilizador.  

Idiomas suportados são definidos no `<BuildingBlocks>` no seguinte formato:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

Idioma predefinido e as linguagens, comportam-se da mesma forma tal como nas políticas incorporadas.

##<a name="enable-custom-language-strings"></a>Ativar cadeias de idioma personalizado

A criação de cadeias de idioma personalizado requer dois passos:
1. Editar o `<ContentDefinition>` para a página especificar um ID de recurso para os idiomas pretendidos
2. Criar o `<LocalizedResources>` com IDs correspondentes na sua`<BuildingBlocks>`

Tenha em atenção que pode colocar um `<ContentDefinition>` e `<BuildingBlock>` no seu ficheiro de extensão ou o ficheiro de política de entidade confiadora, dependendo se pretende que as alterações para estar em todas as políticas inheriting ou não.

###<a name="edit-the-contentdefinition-for-the-page"></a>Editar ContentDefinition para a página

Para cada página que pretende localizar, pode especificar no `<ContentDefinition>` que recursos do idioma serve para cada código de idioma.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

Neste exemplo, francês (fr) e cadeias personalizadas inglês (en) são adicionadas para a página de inscrição ou início de sessão unificada.  O `LocalizedResourcesReferenceId` para cada `LocalizedResourcesReference` é o mesmo que os respetivos região, mas pode utilizar qualquer cadeia como ID.  Para cada combinação de página e de idioma, tem de criar um correspondente `<LocalizedResources>` mostrado a seguir.


###<a name="create-the-localizedresources"></a>Criar o LocalizedResources

As substituições são contidas no seus `<BuildingBlocks>` e não existe um `<LocalizedResources>` para cada página e o idioma que especificou no `<ContentDefinition>` para cada página.  Cada substituição está especificada como um `<LocalizedString>` como o exemplo seguinte:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Existem quatro tipos de elementos de cadeia na página:

**ClaimsProvider** -as etiquetas para os fornecedores de identidade (Facebook, Google, do Azure AD etc.) **ClaimType** -as etiquetas para os atributos e as respetivas correspondente texto de ajuda ou erros de validação de campo **UxElement** - outros elementos na página que existem por predefinição, como botões, ligações ou texto decadeia**ErrorMessage** -formam mensagens de erro de validação

Certifique-se de que o `StringId`s corresponder para a página que está a utilizar estas substituições, caso contrário, é bloqueado pela validação de política no carregamento.  