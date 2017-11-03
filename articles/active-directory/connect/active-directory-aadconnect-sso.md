---
title: 'Azure AD Connect: Totalmente integrada Single Sign-On | Microsoft Docs'
description: "Este tópico descreve o Azure Active Directory (Azure AD) totalmente integrada Single Sign-On e como permite-lhe fornecer verdadeiro-início de sessão único para os utilizadores empresariais de ambiente de trabalho dentro da sua rede empresarial."
services: active-directory
keywords: "o que é o Azure AD Connect, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: billmath
ms.openlocfilehash: dd619ddf62b079ecc06bdf3699e3e32c35313284
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory totalmente integrada Single Sign-On

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é o Azure Active Directory totalmente integrada Single Sign-On?

Azure Active Directory totalmente integrada Single Sign-On (Azure AD totalmente integrada SSO) inicia automaticamente os utilizadores quando estão nos respetivos dispositivos de empresa ligados à sua rede empresarial. Quando ativada, os utilizadores não precisam de escrever as palavras-passe para iniciar sessão Azure AD e, normalmente, mesmo escreva os nomes de utilizador. Esta funcionalidade fornece aos utilizadores o facilitar o acesso às suas aplicações baseado na nuvem sem necessidade de quaisquer componentes adicionais no local.

<iframe width="560" height="315" src="https://www.youtube.com/embed/PyeAC85Gm7w" frameborder="0" allowfullscreen></iframe>

SSO totalmente integrada pode ser combinado com uma o [sincronização de Hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) ou [autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md) métodos de início de sessão.

![Totalmente integrado Single Sign-On](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>SSO totalmente integrada é _não_ aplicável para serviços de Federação do Active Directory (ADFS).

## <a name="key-benefits"></a>Principais vantagens

- *Experiência de utilizador excelente*
  - Os utilizadores são automaticamente iniciados no local e aplicações baseadas na nuvem.
  - Os utilizadores não têm de introduzir repetidamente as palavras-passe.
- *Fácil de implementar e administrar*
  - Não existem componentes adicionais necessários no local para isto funcionar.
  - Funciona com qualquer método de autenticação em nuvem - [sincronização de Hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) ou [autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md).
  - Pode ser implementado para alguns ou todos os utilizadores através da política de grupo.
  - Registe dispositivos não Windows 10 com o Azure AD sem a necessidade de qualquer infraestrutura de AD FS. Esta capacidade tem de utilizar a versão 2.1 ou posterior do [cliente associação à área de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Destaques de funcionalidade

- Início de sessão de nome de utilizador pode ser ambos o nome de utilizador de predefinição de no local (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (`Alternate ID`). Utilizam ambos trabalho casos porque utiliza o SSO totalmente integrado a `securityIdentifier` afirmação na permissão Kerberos para procurar o objeto de utilizador correspondente no Azure AD.
- SSO totalmente integrada é uma funcionalidade oportunistas medida planeada. Se falhar por alguma razão, a experiência de início de sessão do utilizador fica novamente para o respetivo comportamento normal - revertidos, o utilizador tem de introduzir a palavra-passe na página de início de sessão.
- Se uma aplicação reencaminha uma `domain_hint` (OpenID Connect) ou `whr` parâmetro (SAML) - identificar o seu inquilino, ou `login_hint` parâmetro - identificar o utilizador, no seu Azure AD início de sessão pedido, os utilizadores automaticamente iniciados sem-las introduzir os nomes de utilizador ou palavras-passe.
- Pode ser ativado através do Azure AD Connect.
- É uma funcionalidade livre e não precisa de quaisquer edições pagas do Azure AD para utilizá-lo.
- É suportada em clientes de baseada no browser da web e os clientes do Office que suportam [autenticação moderna](https://aka.ms/modernauthga) em plataformas e browsers com capacidade de autenticação Kerberos:

| OS\Browser |Internet Explorer|Limite|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim|Não|Sim|Sim\*|N/D
|Windows 8.1|Sim|N/D|Sim|Sim\*|N/D
|Windows 8|Sim|N/D|Sim|Sim\*|N/D
|Windows 7|Sim|N/D|Sim|Sim\*|N/D
|Mac OS X|N/D|N/D|Sim\*|Sim\*|Sim\*

\*Requer [configuração adicional](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Para o Windows 10, a recomendação é utilizar [associação do Azure AD](../active-directory-azureadjoin-overview.md) para a único início de sessão uma experiência ideal com o Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) - obter cópias de segurança e executar o SSO totalmente integrada de AD do Azure.
- [**Descrição detalhada da Technical** ](active-directory-aadconnect-sso-how-it-works.md) -compreender como funciona esta funcionalidade.
- [**Perguntas mais frequentes** ](active-directory-aadconnect-sso-faq.md) -respostas para as perguntas mais frequentes.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-sso.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.
