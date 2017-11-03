---
title: "Do Azure AD Connect: A autenticação pass-through - limitações atuais | Microsoft Docs"
description: "Este artigo descreve as limitações atuais da autenticação do Azure Active Directory (Azure AD) pass-through."
services: active-directory
keywords: "Authentication do Azure AD Connect pass-through, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 42a6d7156a28aaeadea96f9134b0becc004816ba
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Do Azure autenticação do Active Directory pass-through: Limitações atuais

>[!IMPORTANT]
>A autenticação pass-through do AD do Azure é uma funcionalidade livre e não precisa de quaisquer edições pagas do Azure AD para utilizá-lo. A autenticação pass-through só está disponível na instância em todo o mundo do Azure AD e não no [Datacenters da Microsoft Cloud](http://www.microsoft.de/cloud-deutschland) e [na nuvem do Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários suportados

Os cenários seguintes são totalmente suportados:

- Utilizador inícios de sessão em todas as aplicações baseadas no browser do web.
- Utilizador inícios de sessão em aplicações de cliente do Office 365 que suportam [autenticação moderna](https://aka.ms/modernauthga) -Office 2016 ou Office 2013 _com_ autenticação moderna.
- Associação de AD do Azure para dispositivos Windows 10.
- Suporte Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os cenários seguintes são _não_ suportados:

- Utilizador inícios de sessão no Office cliente as aplicações legadas - Office 2010 e Office 2013 _sem_ autenticação moderna). As organizações são encouraged para mudar para a autenticação moderna, se possível. Permite a autenticação moderna para o suporte de autenticação pass-through, mas também ajuda a proteger os seus utilizadores contas com [acesso condicional](../active-directory-conditional-access.md) funcionalidades, tais como o multi-factor Authentication (MFA).
- Utilizador inícios de sessão para o Skype para aplicações de cliente de negócio, incluindo o Skype para empresas 2016.
- Utilizador inícios de sessão para o PowerShell v 1.0. É recomendado que utilize v 2.0 do PowerShell.
- Serviços de domínio do Azure AD.
- Palavras-passe de aplicação para a MFA.
- Deteção de utilizadores com [fuga credenciais](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Como uma solução para cenários não suportados _apenas_, ativar a sincronização de Hash de palavra-passe no [funcionalidades opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) página do Assistente do Azure AD Connect. Ativar a sincronização de Hash de palavra-passe também dá-lhe a opção de autenticação de ativação pós-falha se a sua infraestrutura no local completamente é interrompida. Esta ativação pós-falha de autenticação pass-through para a sincronização de Hash de palavra-passe não é automática, mas para efetuou com a ajuda do Support da Microsoft.

## <a name="next-steps"></a>Passos seguintes
- [**Início Rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - obter cópias de segurança e executar a autenticação pass-through do Azure AD.
- [**Bloqueio do smart** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -capacidade de configurar o bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [**Descrição detalhada da Technical** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -compreender como funciona esta funcionalidade.
- [**Perguntas mais frequentes** ](active-directory-aadconnect-pass-through-authentication-faq.md) -respostas para as perguntas mais frequentes.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**Descrição detalhada da segurança** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -particularmente técnicas obter informações adicionais sobre a funcionalidade.
- [**Azure SSO totalmente integrada de AD** ](active-directory-aadconnect-sso.md) -Saiba mais sobre esta funcionalidade complementares.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.
