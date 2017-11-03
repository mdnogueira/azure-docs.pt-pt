---
title: "Gestão de sessão SSO através de políticas personalizadas - Azure AD B2C | Microsoft Docs"
description: "Saiba como gerir sessões SSO utilizando as políticas personalizadas no Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 809f6000-2e52-43e4-995d-089d85747e1f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: parja
ms.openlocfilehash: ff767993eaf0305168176d0ad6e15c068b8c85eb
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>O Azure AD B2C: Início de sessão (SSO) sessão gestão único

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

B2C do Azure AD permite aos administradores controlar como o Azure AD B2C interage com um utilizador depois do utilizador já foi autenticado. Isto é feito através da gestão de sessão do SSO. Por exemplo, o administrador pode controlar se a seleção de fornecedores de identidade é apresentada, ou se detalhes da conta local tem de ser introduzidos novamente. Este artigo descreve como configurar as definições de SSO para o Azure AD B2C.

## <a name="overview"></a>Descrição geral

Gestão de sessão SSO tem duas partes. O primeiro lida com interações do utilizador diretamente com o Azure AD B2C e as outras oportunidades com interações do utilizador com entidades externas, como o Facebook. O Azure AD B2C não substituir ou ignorar sessões SSO que podem ser contidas por entidades externas. Em vez de encaminhar através do Azure AD B2C para obter a entidade externa é "memorizada", evitando a necessidade de reprompt ao utilizador selecionar o seu fornecedor de identidade social ou enterprise. A decisão de SSO ultimate permanece com a entidade externa.

## <a name="how-does-it-work"></a>Como funciona?

Gestão de sessão SSO utiliza a mesma semântica como qualquer outro perfil técnico políticas personalizadas. Quando um passo orchestration é executado, o perfil técnico associado com o passo está a ser consultado para um `UseTechnicalProfileForSessionManagement` referência. Se existir, o fornecedor de sessão SSO referenciado, em seguida, é verificado para ver se o utilizador é um participante à sessão. Se o fornecedor de SSO de sessão com a, por isso, é utilizado para repovoar a sessão. Da mesma forma, quando a execução de um passo de orquestração estiver concluída, o fornecedor é utilizado para armazenar as informações na sessão se tiver sido especificado um fornecedor de sessão do SSO.

O Azure AD B2C definiu um número de fornecedores de sessão SSO que podem ser utilizadas:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Classes de SSO de gestão são especificadas utilizando o `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` elemento de um perfil técnico.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome dita, este fornecedor não produz qualquer efeito. Este fornecedor pode ser utilizado para suprimir o comportamento SSO para um perfil de técnico específico.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Este fornecedor pode ser utilizado para armazenar afirmações numa sessão. Este fornecedor, normalmente, é referenciado num perfil técnico utilizado para gerir contas locais. 

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Para adicionar afirmações na sessão, utilize o `<PersistedClaims>` elemento do perfil técnico. Quando o fornecedor é utilizado para repovoar a sessão, o persistente afirmações são adicionadas para a matriz de afirmações. `<OutputClaims>`é utilizado para obter afirmações da sessão.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Este fornecedor é utilizado para suprimir o ecrã "choose fornecedor de identidade". Normalmente, é referenciada num perfil técnico configurado para um fornecedor de identidade externas, como o Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Este fornecedor é utilizado para gerir as sessões SAML do Azure AD B2C entre as aplicações, bem como fornecedores de identidade externas SAML.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Existem dois itens de metadados no perfil de técnico:

| Item | Valor predefinido | Valores Possíveis | Descrição
| --- | --- | --- | --- |
| IncludeSessionIndex | VERDADEIRO | Verdadeiro/Falso | Indica ao fornecedor de que o índice de sessão deve ser armazenado. |
| RegisterServiceProviders | VERDADEIRO | Verdadeiro/Falso | Indica que o fornecedor deve registar todos os fornecedores de serviços SAML que foram emitidos uma asserção. |

Ao utilizar o fornecedor para armazenar uma sessão de fornecedor de identidade SAML, os itens acima deverão ser falsos. Ao utilizar o fornecedor para armazenar a sessão de SAML do B2C, os itens acima devem ser VERDADEIRO ou omitido as predefinições são verdadeiras.

>[!NOTE]
> Fim de sessão SAML requer o `SessionIndex` e `NameID` para concluir.

## <a name="next-steps"></a>Passos seguintes

Iremos adoram comentários e sugestões! Se tiver dificuldades em causa com este tópico, publique no Stack Overflow utilizando a tag ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Para pedidos de funcionalidades, votar na nossa [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

