---
title: "Adicionar utilizadores de colaboração B2B do Azure Active Directory sem um convite | Microsoft Docs"
description: "Pode permitir que um utilizador convidado adicionar outros utilizadores convidados ao seu Azure AD sem resgatar um convite na colaboração B2B do Azure Active Directory do."
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
ms.openlocfilehash: 91b9477cdb679851e7d8d2942c06999a05f64e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Adicionar utilizadores de convidado de colaboração B2B sem um convite

Pode permitir que um utilizador, tal como um representante de parceiro, para adicionar utilizadores do parceiro à sua organização sem necessitar de convites para ser resgatadas. Tudo o que deve fazer é conceder privilégios de enumeração no diretório que está a utilizar para o org. de parceiro a que o utilizador 

Conceda estes privilégios quando:

1. Um utilizador na organização do anfitrião (por exemplo, o WoodGrove) invites um utilizador da organização de parceiro (por exemplo, Sam@litware.com) como convidado.
2. O administrador na organização do anfitrião configura políticas que lhe permitem Sam identificar e adicionar outros utilizadores da organização de parceiro (Litware).
3. Agora Sam pode adicionar outros utilizadores de Litware para o diretório de WoodGrove, grupos ou aplicações sem ser necessário convites para ser resgatadas. Se Sam tem os privilégios adequados enumeração Litware, ocorre automaticamente.

### <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como é que os administradores de Azure Active Directory adicionar utilizadores de colaboração do B2B?](active-directory-b2b-admin-add-users.md)
* [Como é que os infotrabalhadores adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
* [Os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento e colaboração do Azure AD B2B](active-directory-b2b-licensing.md)
* [Resolução de problemas de colaboração B2B do Azure Active Directory do](active-directory-b2b-troubleshooting.md)
* [Colaboração do Azure Active Directory B2B perguntas mais frequentes (FAQ)](active-directory-b2b-faq.md)
* [Colaboração B2B do Active Directory Azure API e personalização](active-directory-b2b-api.md)
* [Autenticação multifator para os utilizadores da colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)