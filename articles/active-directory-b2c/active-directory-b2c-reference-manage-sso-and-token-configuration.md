---
title: "O Azure Active Directory B2C: Gerir o SSO e personalização token com as políticas personalizadas | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/02/2017
ms.author: sama
ms.openlocfilehash: 8f5703d15766f221517cd89352d41685652d32d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>O Azure Active Directory B2C: Gerir o SSO e personalização token com as políticas personalizadas
Utilizar políticas personalizadas fornece o mesmo controlo sobre o token, a sessão e o único configurações de início de sessão (SSO) como através de políticas incorporadas.  Para saber o que faz cada definição, consulte a documentação [aqui](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Configuração de afirmações e durações token
Para alterar as definições no seu token durações, terá de adicionar um `<ClaimsProviders>` elemento no ficheiro de terceiros de entidade confiadora da política que pretende afetar.  O `<ClaimsProviders>` elemento é um elemento subordinado do `<TrustFrameworkPolicy>`.  No interior tem de colocar as informações que afeta as durações token.  O XML tem o seguinte aspeto:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Durações de token de acesso** a duração do token de acesso pode ser alterada modificando o valor de `<Item>` com a chave = "token_lifetime_secs" em segundos.  O valor predefinido na incorporado é 3600 segundos (60 minutos).

**Duração do token ID** duração do token o ID pode ser alterada modificando o valor de `<Item>` com a chave = "id_token_lifetime_secs" em segundos.  O valor predefinido na incorporado é 3600 segundos (60 minutos).

**Atualize a duração do token** a duração do token de atualização pode ser alterada modificando o valor de `<Item>` com a chave = "refresh_token_lifetime_secs" em segundos.  O valor predefinido na incorporado é 1209600 segundos (14 dias).

**Atualizar o token duração de janela deslizante** se pretende definir uma duração de janela deslizante para o seu token de atualização, modifique o valor `<Item>` com a chave = "rolling_refresh_token_lifetime_secs" em segundos.  O valor predefinido na incorporado é 7776000 (90 dias).  Se não quiser enfore a deslizante uma duração da janela, substitua a linha com:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Emissor (iss) afirmação** se pretender alterar a afirmação de emissor (iss), modifique o valor dentro do `<Item>` com a chave = "IssuanceClaimPattern".  Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.

**Definição que representa o ID de política de afirmações** as opções para a definição deste valor são TFP (confiança da política de framework) e ACR (referência de contexto de autenticação).  
Recomendamos que definir este como TFP, para efetuar este procedimento, certifique-se a `<Item>` com a chave = "AuthenticationContextReferenceClaimPattern" existe e o valor é `None`.
No seu `<OutputClaims>` item, adicione este elemento:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Para o ACR, remova o `<Item>` com a chave = "AuthenticationContextReferenceClaimPattern".

**O requerente (sub) afirmação** esta opção é predefinida para ObjectID, se pretender que esta opção para alternar `Not Supported`, efetue o seguinte procedimento:

Substitua esta linha 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
com esta linha:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Comportamento de sessão e SSO
Para alterar o comportamento de sessão e configurações de SSO, tem de adicionar um `<UserJourneyBehaviors>` elemento dentro do `<RelyingParty>` elemento.  O `<UserJourneyBehaviors>` elemento tem de seguir de imediato o `<DefaultUserJourney>`.  Dentro do seu `<UserJourneyBehavors>` elemento deve ter o seguinte aspeto:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Configuração início de sessão único (SSO)** para alterar a configuração de início de sessão único, terá de modificar o valor de `<SingleSignOn>`.  Os valores aplicáveis são `Tenant`, `Application`, `Policy` e `Disabled`. 

**Aplicação Web duração de sessão (minutos)** para alterar a aplicação web duração de sessão, terá de modificar o valor da `<SessionExpiryInSeconds>` elemento.  O valor predefinido na políticas incorporadas é segundos 86400 (1440 minutos).

**Tempo limite de sessão de aplicação Web** para alterar o limite de tempo de sessão de aplicação web, tem de modificar o valor de `<SessionExpiryType>`.  Os valores aplicáveis são `Absolute` e `Rolling`.
