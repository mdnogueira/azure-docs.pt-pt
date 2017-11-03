---
title: "Do Azure AD Connect: A autenticação pass-through - como funciona? | Microsoft Docs"
description: Este artigo descreve como funciona o Azure Active Directory pass-through Authentication.
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
ms.openlocfilehash: c7863e38671349b6424ee08330da8aaa49cb2a70
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Authentication do Azure Active Directory pass-through: Descrição profunda técnica
O seguinte artigo é uma descrição geral de como funciona a autenticação pass-through do Azure AD.  Para avançada técnica e informações de segurança, consulte o [ **detalhada da segurança** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) artigo.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como funciona a autenticação pass-through do Active Directory do Azure?

Quando um utilizador tenta iniciar sessão numa aplicação protegida pelo Azure Active Directory (Azure AD) e se a autenticação pass-through estiver ativada no inquilino, ocorrem os seguintes passos:

1. O utilizador tenta aceder a uma aplicação (por exemplo, o Outlook Web App - https://outlook.office365.com/owa/).
2. Se o utilizador não está já iniciou sessão, o utilizador é redirecionado para a página de início de sessão do Azure AD.
3. O utilizador introduz o respetivo nome de utilizador e palavra-passe para a página de início de sessão do Azure AD e clica no botão "Iniciar sessão".
4. Azure AD, no receber o pedido de início de sessão, coloca o nome de utilizador e palavra-passe (encriptadas com uma chave pública) numa fila.
5. Um agente de autenticação no local obtém o nome de utilizador e palavra-passe encriptada da fila.
6. O agente desencripta a palavra-passe utilizando a respetiva chave privada.
7. O agente, em seguida, valida o nome de utilizador e palavra-passe no Active Directory utilizando APIs padrão do Windows (um mecanismo semelhante para o que é utilizado por serviços de Federação do Active Directory). O nome de utilizador pode ser ambos o nome de utilizador de predefinição de no local (normalmente `userPrincipalName`) ou outro atributo configurado no Azure AD Connect (conhecido como `Alternate ID`).
8. O Active Directory domínio controlador (DC) no local, em seguida, avalia o pedido e devolve a resposta adequada (êxito, falha, palavra-passe expirou ou utilizador bloqueadas) para o agente.
9. O agente de autenticação, por sua vez, devolve esta resposta para o Azure AD.
10. Azure AD avalia a resposta e responde ao utilizador conforme apropriado - por exemplo, inicia o utilizador imediatamente ou pedidos para o multi-factor Authentication (MFA).
11. Se o utilizador inicie sessão com êxito, o utilizador é capaz de aceder à aplicação.

O diagrama seguinte ilustra a todos os componentes e os passos envolvidos.

![Autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Passos seguintes
- [**Limitações atuais** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) -saber quais os cenários que são suportados e aqueles que não são.
- [**Início Rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - obter cópias de segurança e executar a autenticação pass-through do Azure AD.
- [**Bloqueio do smart** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -capacidade de configurar o bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [**Perguntas mais frequentes** ](active-directory-aadconnect-pass-through-authentication-faq.md) -respostas para as perguntas mais frequentes.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**Descrição detalhada da segurança** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -particularmente técnicas obter informações adicionais sobre a funcionalidade.
- [**Azure SSO totalmente integrada de AD** ](active-directory-aadconnect-sso.md) -Saiba mais sobre esta funcionalidade complementares.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.
