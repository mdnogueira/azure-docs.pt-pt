---
title: "Gerir o SSO para Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Saiba mais sobre as noções básicas do início de sessão com o Proxy da aplicação"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1deb3d91049d45fe26791783e13bd23e0a7d9f95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Como Proxy de aplicações do Azure AD fornece o início de sessão único?

O início de sessão único é um elemento-chave do Proxy de aplicações do Azure AD.  Fornece a melhor experiência de utilizador porque os seus utilizadores só têm de iniciar sessão no Azure Active Directory na nuvem. Depois de se autenticar-se ao Azure Active Directory, o conector do Proxy de aplicação processa a autenticação para a aplicação no local. A aplicação de back-end não pode informar a diferença entre um início de sessão através do Proxy de aplicações e uma utilização normal num dispositivo associado a um domínio de utilizador remoto. 

Para utilizar o Azure Active Directory para início de sessão às suas aplicações, tem de selecionar **do Azure Active Directory** como método de pré-autenticação. Se selecionar **Passthrough** , em seguida, os utilizadores não autenticar para o Azure Active Directory de todo, mas são direcionados diretamente para a aplicação. Pode configurar esta definição quando primeiro publicar uma aplicação, ou navegue até à sua aplicação no portal do Azure e editar as definições de Proxy de aplicações. 

Para ver as opções de início de sessão único, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações**.
3. Selecione a aplicação cujas único início de sessão opções que pretende gerir.
4. Selecione **de sessão único-**.

   ![Menu de lista pendente SSO](./media/application-proxy-sso-overview/single-sign-on-mode.png)

O menu pendente mostra as opções de cinco para início de sessão à sua aplicação:

* Azure AD-início de sessão único desativada
* Baseado em palavra-passe de início de sessão
* O início de sessão ligado
* Autenticação integrada do Windows
* Com base no cabeçalho de início de sessão

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD-início de sessão único desativada

Se não quiser utilizar a integração do Azure Active Directory para início de sessão à sua aplicação, escolha **do Azure AD-início de sessão único desativada**. Com esta opção selecionada, os utilizadores podem autenticar-se duas vezes. Em primeiro lugar, autenticar-se ao Azure Active Directory e, em seguida, iniciar sessão para a própria aplicação. 

Esta opção é uma boa opção se a sua aplicação no local não requer que os utilizadores autentiquem, mas que pretende adicionar o Azure Active Directory como uma camada de segurança de acesso remoto. 

## <a name="password-based-sign-on"></a>Baseado em palavra-passe de início de sessão

Se pretender utilizar o Azure Active Directory como um cofre de palavra-passe para as suas aplicações no local, escolha **baseada em palavra-passe de início de sessão**. Esta opção é uma boa opção se a aplicação se autentica com uma combinação de nome de utilizador/palavra-passe em vez de tokens de acesso ou nos cabeçalhos. Com baseado em palavra-passe de início de sessão, os utilizadores tem de iniciar sessão para o tempo de aplicação primeiro acederem ao mesmo. Depois disso, o Azure Active Directory fornece o nome de utilizador e palavra-passe em nome do utilizador. 

Para obter informações sobre a configuração baseada em palavra-passe de início de sessão, consulte [vaulting para início de sessão com o Proxy da aplicação de palavra-passe](application-proxy-sso-azure-portal.md).

## <a name="linked-sign-on"></a>O início de sessão ligado

Se já tiver uma único início de sessão solução estar configurada para as identidades no local, escolha **ligado início de sessão**. Esta opção permite que o Azure Active Directory para tirar partido das soluções SSO existentes, mas ainda fornece aos utilizadores acesso remoto para a aplicação. 

Para obter informações sobre ligado início de sessão (formally conhecido como existente-início de sessão único), consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Autenticação integrada do Windows

Se as suas aplicações no local utilizar Authentication(IWA) integrada do Windows ou se quiser utilizar a delegação restrita de Kerberos (KCD) para início de sessão, escolha **autenticação integrada do Windows**. Com esta opção, os utilizadores só precisam para autenticar para o Azure Active Directory e, em seguida, o conector do Proxy de aplicações representa o utilizador tem de obter um token de Kerberos e iniciar sessão para a aplicação. 

Para obter informações sobre como configurar a autenticação integrada do Windows, consulte [delegação restrita de Kerberos para o início de sessão único com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md).

## <a name="header-based-sign-on"></a>Com base no cabeçalho de início de sessão 

Se as suas aplicações utilizam cabeçalhos para autenticação, escolha **com base no cabeçalho de início de sessão**. Com esta opção, os utilizadores só precisam para autenticação o Azure Active Directory. Parceiros da Microsoft com um serviço de autenticação de terceiros chamado PingAccess, o qual o token de acesso do Azure Active Directory convertido de um cabeçalho de formato para a aplicação. 

Para obter informações sobre como configurar a autenticação baseada no cabeçalho, consulte [autenticação baseada no cabeçalho para início de sessão com o Proxy da aplicação](application-proxy-ping-access.md).

## <a name="next-steps"></a>Passos seguintes

- [Palavra-passe vaulting para início de sessão com o Proxy da aplicação](application-proxy-sso-azure-portal.md)
- [Delegação restrita de Kerberos para o início de sessão único com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)
- [Autenticação baseada no cabeçalho para início de sessão com o Proxy da aplicação](application-proxy-ping-access.md) 
