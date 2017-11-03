---
title: "Mapeamento no Azure Active Directory de afirmações de utilizador de colaboração do B2B | Microsoft Docs"
description: "afirmações de mapeamento de referência para colaboração B2B do Azure Active Directory do"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Afirmações de utilizador de colaboração do B2B mapeamento no Azure Active Directory

Personalizar afirmações emitidas no token SAML para os utilizadores de colaboração B2B do Azure suporta o Active Directory (Azure AD). Quando um utilizador efetua a autenticação para a aplicação, o Azure AD emite um token SAML para a aplicação que contém informações (ou afirmações) sobre o utilizador que identifica exclusivamente. Por predefinição, isto inclui o nome de utilizador, endereço de correio eletrónico, nome próprio e apelido do utilizador. Pode ver ou editar as afirmações enviadas no token SAML para a aplicação no separador atributos.

Existem duas razões possíveis por que razão poderá ser necessário editar as afirmações emitidas no SAML token.

1. A aplicação foi escrita para solicitar a um conjunto diferente de afirmação URIs ou valores de afirmação

2. A aplicação requer a afirmação NameIdentifier ser algo que o nome principal de utilizador armazenado no Azure Active Directory.

  ![Vista de afirmações num SAML token](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Para obter informações sobre como adicionar e editar afirmações, consulte este artigo sobre a personalização de afirmações, [personalizar afirmações emitidas no token SAML para aplicações previamente integradas no Azure Active Directory](develop/active-directory-saml-claims-customization.md). Para colaboração B2B utilizadores, mapeamento entre-inquilinos NameID e UPN são impedidos por motivos de segurança.


## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de utilizador de colaboração B2B](active-directory-b2b-user-properties.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar B2bB convites de colaboração](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e de colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração do B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicações SaaS colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Limitações atuais de colaboração B2B](active-directory-b2b-current-limitations.md)
