---
title: "Segurança de palavra-passe de camadas do Azure AD | Microsoft Docs"
description: "Explica a forma como os do Azure AD impõe palavras-passe seguras e protege as palavras-passe de utilizadores de criminals informático,"
services: active-directory
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 50b24466bcbd399de19934f7ec5ed096e308eaf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Uma abordagem de várias camadas de segurança de palavra-passe do Azure AD

Este artigo aborda algumas melhores práticas que pode seguir como um utilizador ou como um administrador para proteger o seu Azure Active Directory (Azure AD) ou Account Microsoft.

 > [!NOTE]
 > **Está aqui porque está a ter problemas em iniciar sessão?** Se assim for, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
 >
 > Os administradores do AD do Azure podem repor palavras-passe do utilizador com as orientações no artigo [repor a palavra-passe de um utilizador no Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Requisitos de palavra-passe

O Azure AD incorpora as seguintes abordagens comuns relativamente à proteção de palavras-passe:

* Requisitos de comprimento de palavras-passe
* Requisitos de complexidade de palavra-passe
* Expiração de palavras-passe regular e periódica

Para obter informações sobre a reposição no Azure Active Directory palavra-passe, consulte o tópico [passe self-service do Azure AD ' Repor para profissionais de TI](active-directory-passwords.md).

## <a name="azure-ad-password-protections"></a>Proteções de palavra-passe do Azure AD

Azure AD e o sistema de contas do Microsoft utilizar da indústria comprovada abordagens para garantir a proteção segura de utilizador e administrador de palavras-passe, incluindo:

* Palavras-passe banidas dinamicamente
* Smart Password Lockout

Para obter informações sobre a gestão de palavra-passe com base na investigação atual, consulte o documento técnico [orientações de palavra-passe](http://aka.ms/passwordguidance).

### <a name="dynamically-banned-passwords"></a>Palavras-passe banidas dinamicamente

Azure AD e Accounts Microsoft salvaguardar proteção de palavra-passe por dinamicamente banning palavras-passe utilizadas frequentemente. A equipa do Azure AD Identity Protection analisa regularmente listas banned palavra-passe, impedir que os utilizadores a seleção de palavras-passe utilizadas frequentemente. Este serviço está disponível para clientes do Azure AD e do Serviço de Conta Microsoft.

Ao criar palavras-passe, é importante para os administradores a encorajar os utilizadores para escolher as expressões de palavra-passe que incluam uma combinação de letras, números, carateres ou palavras exclusivos. Esta abordagem ajuda a tornar as palavras-passe do utilizador praticamente impossíveis ser mais fácil para os utilizadores memorizem mas comprometido.

#### <a name="password-breaches"></a>Falhas de palavra-passe

Microsoft sempre está a trabalhar para se manter um passo à frente das criminals informático.

A equipa do Azure AD Identity Protection analisa continuamente as palavras-passe que são utilizadas mais vezes. Os cibercriminosos também utilizam estratégias semelhantes para informar os respetivos ataques, que podem incluir a criação de [rainbow tables](https://en.wikipedia.org/wiki/Rainbow_table), para descodificar hashes de palavras-passe.

A Microsoft analisa continuamente [violações a dados](https://www.privacyrights.org/data-breaches), para manter uma lista de palavras-passe banidas dinamicamente atualizada, assegurando que as palavras-passe vulneráveis são banidas antes de se tornarem uma ameaça real aos clientes do Azure AD. Para obter mais informações sobre os nossos esforços de segurança atuais, veja [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx) (Relatório de Informações de Segurança da Microsoft).

### <a name="smart-password-lockout"></a>Smart Password Lockout

Quando o Azure AD deteta que um potencial cibercriminoso está a tentar piratear uma palavra-passe de utilizador, bloqueamos essa conta com o Smart Password Lockout. O Azure AD foi concebido para determinar o risco associado a sessões de início de sessão específicas. Em seguida, utilizar os dados de segurança mais atualizados, iremos aplicar semântica de bloqueio para parar ameaças informático.

Se um utilizador está bloqueado fora do Azure AD, as respetivas ecrã semelhante ao seguinte:

  ![Bloqueado do Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Para outras contas Microsoft, os respetivos ecrã semelhante ao seguinte:

  ![Bloqueado de uma conta Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Para obter informações sobre a reposição no Azure Active Directory palavra-passe, consulte o tópico [passe self-service do Azure AD ' Repor para profissionais de TI](active-directory-passwords.md).

  >[!NOTE]
  >Se for administrador do Azure AD, poderá ser útil utilizar o [Windows Hello](https://www.microsoft.com/windows/windows-hello) para impedir que os seus utilizadores criem palavras-passe tradicionais.
  >

## <a name="next-steps"></a>Passos seguintes

* [Como atualizar a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md)
* [The fundamentals of Azure identity management](fundamentals-identity.md) (As noções básicas da gestão de identidades do Azure)
* [Atividade de reposição de relatório na palavra-passe](active-directory-passwords-reporting.md)
