---
title: "O Azure Active Directory B2C: Noções sobre políticas personalizadas do pacote de arranque | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 9847bcfcc139a769847678c1cca6a8b9c3a30e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Compreender as políticas personalizadas do pacote de arranque de política personalizada do Azure AD B2C

Esta secção lista todos os elementos principais da política B2C_1A_base que vem com o **Starter pacote** e que é utilizado para criar as suas políticas através de herança do *B2C_1A_base_extensions política*.

Como tal, é mais particularmente aborda os tipos de afirmação já definidos, transformações de afirmações, as definições de conteúdo, fornecedores de afirmações com os seus perfis técnica e percursos de utilizador principal.

> [!IMPORTANT]
> Microsoft faz com que não oferece nenhuma garantia, expressa ou implícita, relativamente a informações fornecidas hereafter. Alterações podem ser introduzidas em qualquer altura, antes da hora de DG, momento GA ou depois.

As suas políticas e a política de B2C_1A_base_extensions podem substituir estas definições e alargar esta política de principal, fornecendo adicionais conforme necessário.

Os elementos de core do *B2C_1A_base política* são tipos de afirmação, transformações de afirmações e as definições de conteúdo. Estes elementos podem suscetível seja referenciado nas suas políticas, bem como no *B2C_1A_base_extensions política*.

## <a name="claims-schemas"></a>Esquemas de afirmações

Isto afirmações esquemas está dividida nas três secções:

1.  Primeira secção que lista as mínimo afirmações que são necessárias para os percursos de utilizador funcionar corretamente.
2.  Uma segunda secção apresenta as afirmações necessários para os parâmetros de cadeia de consulta e outros parâmetros especiais para outros fornecedores de afirmações, especialmente login.microsoftonline.com para autenticação. **Não modifique estas afirmações**.
3.  E, eventualmente, uma secção terceira apresenta uma lista de quaisquer afirmações adicionais, opcionais que podem ser recolhidas do utilizador, armazenado no diretório e enviados em tokens durante o início de sessão. É possível adicionar o novo tipo de afirmações recolhidos a partir de utilizador e/ou enviados no token nesta secção.

> [!IMPORTANT]
> O esquema de afirmações contém restrições para determinadas afirmações, tais como palavras-passe e nomes de utilizador. A política de confiança Framework (TF) trata do Azure AD como qualquer outro fornecedor de afirmações e todas as respetivas restrições são modelled na política de premium. Foi possível modificar uma política para adicionar mais restrições ou utilize outro fornecedor de afirmações para armazenamento de credenciais que têm a sua própria restrições.

Os tipos de afirmação disponíveis são listados abaixo.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Afirmações que são necessárias para os percursos de utilizador

As seguintes afirmações são necessárias para percursos de utilizador funcionar corretamente:

| Tipo de afirmações | Descrição |
|-------------|-------------|
| *ID de utilizador* | Nome de utilizador |
| *signInName* | Inicie sessão no nome |
| *tenantId* | Identificador de inquilino (ID) do objeto de utilizador no Azure AD B2C Premium |
| *objectId* | Identificador de objeto (ID) do objeto de utilizador no Azure AD B2C Premium |
| *palavra-passe* | Palavra-passe |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Políticas de palavra-passe utilizadas pelo Premium do Azure AD B2C para determinar a força da palavra-passe, expiração, etc. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Número de telefone do utilizador |
| *Verified.strongAuthenticationPhoneNumber* | |
| *correio eletrónico* | Endereço de correio eletrónico que pode ser utilizado para contactar o utilizador |
| *signInNamesInfo.emailAddress* | Endereço de e-mail que o utilizador pode utilizar para iniciar sessão |
| *otherMails* | Endereços de e-mail que podem ser utilizados para contactar o utilizador |
| *userPrincipalName* | Nome de utilizador conforme armazenado no Premium do Azure AD B2C |
| *upnUserName* | Nome de utilizador para a criação de nome principal de utilizador |
| *mailNickName* | Nome de nick de correio do utilizador conforme armazenado no Premium do Azure AD B2C |
| *newUser* | |
| *foi executado-SelfAsserted-entrada* | Afirmação que especifica se os atributos foram recolhidos do utilizador |
| *foi executado-PhoneFactor-entrada* | Afirmação que especifica se um novo número de telefone foi recolhido do utilizador |
| *authenticationSource* | Especifica se o utilizador foi autenticado no fornecedor de identidade sociais, login.microsoftonline.com ou conta local |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Afirmações necessárias para os parâmetros de cadeia de consulta e outros parâmetros especiais

As seguintes afirmações têm de passar em parâmetros especiais (incluindo alguns parâmetros de cadeia de consulta) para outros fornecedores de afirmações:

| Tipo de afirmações | Descrição |
|-------------|-------------|
| *Nux* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *NCA* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *linha de comandos* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *mkt* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *LC* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *grant_type* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *âmbito* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *client_id* | Parâmetro especial transmitido para a autenticação da conta local para login.microsoftonline.com |
| *objectIdFromSession* | Parâmetro fornecido pelo fornecedor de gestão de sessão predefinida para indicar que o id de objeto foi obtido uma sessão SSO |
| *isActiveMFASession* | Parâmetro fornecido pela gestão de sessão a MFA para indicar que o utilizador tem uma sessão ativa de MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Afirmações (opcionais) adicionais que podem ser recolhidas

As seguintes afirmações são afirmações adicionais que podem ser recolhidas a partir dos utilizadores, armazenadas no diretório e enviadas no token. Conforme realçado antes, afirmações adicionais podem ser adicionadas a esta lista.

| Tipo de afirmações | Descrição |
|-------------|-------------|
| *givenName* | Nome fornecido (também conhecido como nome próprio) do utilizador |
| *Apelido* | Apelido do utilizador (também conhecido como o nome de família ou o apelido) |
| *Extension_picture* | Imagem do utilizador do social |

## <a name="claim-transformations"></a>Transformações de afirmação

As transformações de afirmação disponíveis são listadas abaixo.

| Transformação de afirmações | Descrição |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definições de conteúdo

Esta secção descreve as definições de conteúdo já foi declaradas no *B2C_1A_base* política. Estas definições de conteúdo são suscetíveis a ser referenciada, substituídas, e/ou expandido conforme necessário na suas própria políticas, bem como no *B2C_1A_base_extensions* política.

| Fornecedor de afirmações | Descrição |
|-----------------|-------------|
| *Facebook* | |
| *Início de sessão de conta local* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Gestão personalizada permitido* | |
| *Conta local* | |
| *Gestão de sessão* | |
| *Motor de política de Trustframework* | |
| *TechnicalProfiles* | |
| *Emissor de token* | |

## <a name="technical-profiles"></a>Perfis técnicas

Esta secção ilustra os perfis técnicos já foi declarados por fornecedor de afirmações o *B2C_1A_base* política. Estes perfis técnicas são suscetíveis a ser mais referenciado, substituídas, e/ou expandido conforme necessário na suas própria políticas, bem como no *B2C_1A_base_extensions* política.

### <a name="technical-profiles-for-facebook"></a>Perfis técnicas para o Facebook

| Perfil técnica | Descrição |
|-------------------|-------------|
| *Facebook OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Perfis técnicas para início de sessão de conta Local

| Perfil técnica | Descrição |
|-------------------|-------------|
| *Início de sessão NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Perfis técnicas para Phone Factor

| Perfil técnica | Descrição |
|-------------------|-------------|
| *Entrada do PhoneFactor* | |
| *PhoneFactor InputOrVerify* | |
| *Certifique-se de um PhoneFactor* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Perfis técnicas para o Azure Active Directory

| Perfil técnica | Descrição |
|-------------------|-------------|
| *AAD comuns* | Perfil técnico incluído por outros perfis técnicos AAD xxx |
| *AAD UserWriteUsingAlternativeSecurityId* | Perfil técnica para inícios de sessão de redes sociais |
| *AAD UserReadUsingAlternativeSecurityId* | Perfil técnica para inícios de sessão de redes sociais |
| *UserReadUsingAlternativeSecurityId-AAD-NoError* | Perfil técnica para inícios de sessão de redes sociais |
| *AAD UserWritePasswordUsingLogonEmail* | Perfil técnica para contas locais |
| *AAD UserReadUsingEmailAddress* | Perfil técnica para contas locais |
| *AAD UserWriteProfileUsingObjectId* | Perfil técnica para a atualização do registo de utilizador utilizando o objectId |
| *AAD UserWritePhoneNumberUsingObjectId* | Perfil técnica para a atualização do registo de utilizador utilizando o objectId |
| *AAD UserWritePasswordUsingObjectId* | Perfil técnica para a atualização do registo de utilizador utilizando o objectId |
| *AAD UserReadUsingObjectId* | Perfil técnica é utilizada para ler os dados depois do utilizador é autenticado |

### <a name="technical-profiles-for-self-asserted"></a>Perfis técnicas para Self permitido

| Perfil técnica | Descrição |
|-------------------|-------------|
| *SelfAsserted Social* | |
| *SelfAsserted ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Perfis técnicas para a conta Local

| Perfil técnica | Descrição |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Perfis técnicas para gestão de sessão

| Perfil técnica | Descrição |
|-------------------|-------------|
| *SM Noop* | |
| *SM AAD* | |
| *SM SocialSignup* | Nome do perfil está a ser utilizado para ambiguidade sessão AAD entre o início de sessão de cópia de segurança e iniciar sessão |
| *SM SocialLogin* | |
| *MFA DE SM* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Perfis técnicas para TechnicalProfiles de motor de política de Trustframework

Atualmente, não existem perfis técnicas são definidas para o **TechnicalProfiles de motor de política de Trustframework** fornecedor de afirmações.

### <a name="technical-profiles-for-token-issuer"></a>Perfis técnicas para o emissor de Token

| Perfil técnica | Descrição |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Percursos de utilizador

Esta secção ilustra os percursos de utilizador já foi declarados no *B2C_1A_base* política. Estes percursos de utilizador são suscetíveis a ser mais referenciado, substituídas, e/ou expandido conforme necessário na suas própria políticas, bem como no *B2C_1A_base_extensions* política.

| Journey de utilizador | Descrição |
|--------------|-------------|
| *Inscrição* | |
| *Início de sessão* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
