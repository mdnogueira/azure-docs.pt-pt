---
title: "Do Azure AD Connect: Autenticação de pass-through | Microsoft Docs"
description: "Este artigo descreve a autenticação de pass-through do Azure Active Directory (Azure AD) e como permite inícios de sessão do Azure AD através da validação as palavras-passe dos utilizadores contra do Active Directory no local."
services: active-directory
keywords: "o que é o Azure AD Connect pass-through a autenticação, instalar o Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 96a33547329931903d264d5ec4ea8da76e36a0a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Utilizador inicie sessão com a autenticação pass-through do Active Directory do Azure

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é o Azure Active Directory pass-through Authentication?

Autenticação de pass-through do Azure Active Directory (Azure AD) permite aos utilizadores iniciar sessão no local e de aplicações baseado na nuvem utilizando as mesmas palavras-passe. Esta funcionalidade fornece aos utilizadores uma melhor experiência - uma menor palavra-passe de lembrar e reduz os custos de suporte técnico de TI, uma vez que os utilizadores são menos provável que se esqueça como iniciar sessão. Quando os utilizadores iniciam sessão com o Azure AD, esta funcionalidade valida palavras-passe dos utilizadores diretamente no seu Active Directory no local.

<iframe width="560" height="315" src="https://www.youtube.com/embed/PyeAC85Gm7w" frameborder="0" allowfullscreen></iframe>

Esta funcionalidade é uma alternativa à [sincronização de Hash de palavra-passe do Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), que oferece o benefício mesmo de autenticação em nuvem para as organizações. No entanto, as políticas de segurança e conformidade em algumas organizações não permitem estas organizações para enviar palavras-passe dos utilizadores mesmo num formulário com hash, fora os respetivos limites internos. A autenticação pass-through é a solução certa para essas organizações.

![Autenticação do Azure AD pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Pode combinar autenticação pass-through com o [totalmente integrada Single Sign-On](active-directory-aadconnect-sso.md) funcionalidade. Desta forma, quando os utilizadores estão a aceder a aplicações no respetivas máquinas empresarias no interior da rede empresarial, não precisam de escreva as palavras-passe para iniciar sessão.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Principais vantagens da utilização de autenticação pass-through do Azure AD

- *Experiência de utilizador excelente*
  - Os utilizadores utilizam as mesmas palavras-passe para iniciar sessão em aplicações baseadas na nuvem e no local.
  - Os utilizadores gastam menos tempo falar com os problemas de relacionadas com a palavra-passe resolução de TI suporte técnico.
  - Os utilizadores podem concluir [gestão de palavra-passe self-service](../active-directory-passwords-overview.md) tarefas na nuvem.
- *Fácil de implementar e administrar*
  - Não é necessário para implementações de complexa no local ou de configuração de rede.
  - Tem apenas um agente de simples para ser instalado no local.
  - Não existem custos de gestão. O agente recebe automaticamente melhoramentos e correções de erros.
- *Proteger*
  - Palavras-passe no local nunca são armazenadas na nuvem em qualquer outra forma.
  - O agente só permite ligações de saída a partir de dentro da sua rede. Por conseguinte, é necessário para instalar o agente numa rede de perímetro, também conhecida como DMZ.
  - Protege as contas de utilizador ao trabalhar de forma totalmente integrada com [políticas de acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md), incluindo o multi-factor Authentication (MFA) e pelo [filtrar ataques de palavra-passe de força bruta](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Elevada disponibilidade*
  - Agentes adicionais podem ser instalados em vários servidores no local para fornecer elevada disponibilidade dos pedidos de início de sessão.

## <a name="feature-highlights"></a>Destaques de funcionalidade

- Suporta a sessão do utilizador em todas as aplicações baseadas no browser do web e para as aplicações de cliente do Microsoft Office que utilizam [autenticação moderna](https://aka.ms/modernauthga).
- Início de sessão de nomes de utilizador podem ser ambos o nome de utilizador de predefinição de no local (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (conhecido como `Alternate ID`).
- A funcionalidade funciona na perfeição com [acesso condicional](../active-directory-conditional-access.md) funcionalidades como o multi-factor Authentication (MFA) para ajudar a proteger os seus utilizadores.
- Integrado com baseado na nuvem [gestão de palavra-passe self-service](../active-directory-passwords-overview.md), incluindo a repetição de escrita de palavras-passe para no local do Active Directory e a proteção de palavra-passe por banning palavras-passe utilizadas frequentemente.
- Ambientes de várias florestas são suportadas se existirem fidedignidades de floresta entre as florestas do AD e se o encaminhamento de sufixo de nome se encontra corretamente configurado.
- É uma funcionalidade livre e não precisa de quaisquer edições pagas do Azure AD para utilizá-lo.
- Pode ser ativado através de [do Azure AD Connect](active-directory-aadconnect.md).
- Utiliza um agente de simples no local que escuta e responde a pedidos de validação da palavra-passe.
- Instalar agentes de vários fornece elevada disponibilidade dos pedidos de início de sessão.
- - [Protege](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) as contas no local contra força forçar ataques de palavra-passe na nuvem.

## <a name="next-steps"></a>Passos seguintes

- [**Início Rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - obter cópias de segurança e executar a autenticação pass-through do Azure AD.
- [**Bloqueio do smart** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -capacidade de configurar o bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [**Limitações atuais** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) -saber quais os cenários que são suportados e aqueles que não são.
- [**Descrição detalhada da Technical** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -compreender como funciona esta funcionalidade.
- [**Perguntas mais frequentes** ](active-directory-aadconnect-pass-through-authentication-faq.md) -respostas para as perguntas mais frequentes.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**Descrição detalhada da segurança** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -particularmente técnicas obter informações adicionais sobre a funcionalidade.
- [**Azure SSO totalmente integrada de AD** ](active-directory-aadconnect-sso.md) -Saiba mais sobre esta funcionalidade complementares.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.
