---
title: "Do Azure AD Connect: A autenticação pass-through - limitações atuais | Microsoft Docs"
description: "Este artigo descreve as limitações atuais da autenticação do Azure Active Directory (Azure AD) pass-through"
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
ms.openlocfilehash: 978ad8f14d70fe60cb220136e87ce4a064672b8a
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Do Azure autenticação do Active Directory pass-through: Limitações atuais

>[!IMPORTANT]
>Autenticação de pass-through do Azure Active Directory (Azure AD) é uma funcionalidade livre e não precisa de quaisquer edições pagas do Azure AD para utilizá-lo. Autenticação pass-through só está disponível na instância em todo o mundo do Azure AD e não no [cloud do Microsoft Azure Datacenters](http://www.microsoft.de/cloud-deutschland) ou [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários suportados

Os cenários seguintes são totalmente suportados:

- Utilizador inícios de sessão para todas as aplicações baseadas no browser do web
- Utilizador inícios de sessão para aplicações de cliente do Office 365 que suportam [autenticação moderna](https://aka.ms/modernauthga)
- Office 2016 ou Office 2013 _com_ autenticação moderna
- Associações de domínio do Azure AD para dispositivos Windows 10
- Suporte do Exchange ActiveSync

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os cenários seguintes são _não_ suportados:

- Utilizador inícios de sessão para aplicações de cliente legados do Office: Office 2010 e Office 2013 _sem_ autenticação moderna. As organizações são encouraged para mudar para a autenticação moderna, se possível. Permite a autenticação moderna para o suporte de autenticação pass-through. Também o ajuda a proteger as contas de utilizador utilizando [acesso condicional](../active-directory-conditional-access-azure-portal.md) funcionalidades, tais como o Azure multi-factor Authentication.
- Utilizador inícios de sessão ao Skype para aplicações de cliente de negócio, incluindo o Skype para empresas 2016.
- Utilizador inícios de sessão para o PowerShell na versão 1.0. Recomendamos que utilize o PowerShell versão 2.0.
- Serviços de domínio do Azure Active Directory.
- Palavras-passe de aplicação para o multi-factor Authentication.
- Deteção de utilizadores com [fuga credenciais](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Como uma solução para cenários não suportados _apenas_, ativar a sincronização de hash de palavra-passe no [funcionalidades opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) página do Assistente do Azure AD Connect. Ativar a sincronização de hash de palavra-passe também dá-lhe a opção de autenticação de ativação pós-falha se a sua infraestrutura no local é interrompida. Esta ativação pós-falha de autenticação pass-through para a sincronização de hash de palavra-passe do Active Directory não é automática. Necessita de ajuda do Support da Microsoft.

## <a name="next-steps"></a>Passos seguintes
- [Início Rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): começar a trabalhar com a autenticação pass-through do Azure AD.
- [Bloqueio do smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Saiba como configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Descrição detalhada da Technical](active-directory-aadconnect-pass-through-authentication-how-it-works.md): compreender como funciona a funcionalidade de autenticação pass-through.
- [Perguntas mais frequentes](active-directory-aadconnect-pass-through-authentication-faq.md): encontre respostas a perguntas mais frequentes sobre a funcionalidade de autenticação pass-through.
- [Resolver problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Descrição detalhada da segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obter particularmente técnicas informações sobre a funcionalidade de autenticação pass-through.
- [Azure SSO totalmente integrada de AD](active-directory-aadconnect-sso.md): saber mais sobre esta funcionalidade complementares.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para novos pedidos de funcionalidade de ficheiros.

