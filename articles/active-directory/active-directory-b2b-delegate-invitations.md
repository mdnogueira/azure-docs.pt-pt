---
title: "Delegar convites para colaboração B2B do Azure Active Directory do | Microsoft Docs"
description: "Propriedades de utilizador de colaboração B2B do Active Directory do Azure são configuráveis"
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
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 78613cc978b585a98d235245194c02371f7f3849
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegar convites para colaboração B2B do Azure Active Directory do

Com a colaboração de empresa-empresa (B2B) do Azure Active Directory (Azure AD), tem de ser um administrador global para enviar convites para. Em vez disso, pode utilizar as políticas e delegar convites aos utilizadores cujas funções de lhes enviar convites para permitam. Uma nova forma de importante para delegar convites de utilizador convidado é através da função de convidado Inviter.

## <a name="guest-inviter-role"></a>Função de convidado Inviter
Iremos pode atribuir o utilizador à função de convidado Inviter enviar convites para. Não tem de ser membro da função de administrador global para enviar convites para. Por predefinição, os utilizadores regulares podem também invocar a API de convite a menos que um administrador global desativado convites para os utilizadores regulares. Um utilizador pode também invocar a API utilizando o portal do Azure ou o PowerShell.

Eis um exemplo que mostra como utilizar o PowerShell para adicionar um utilizador à função de convidado Inviter:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Controlar quem pode convidar

![Controlar como convidar](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Com a colaboração B2B do Azure AD, um administrador de inquilino pode definir as políticas de convite seguintes:

- Desativar convites para
- Apenas administradores e utilizadores na função de convidado Inviter podem convidar
- Administradores, a função de convidado Inviter e membros podem convidar
- Todos os utilizadores, incluindo nos convidados, podem convidar

Por predefinição, os inquilinos estão definidos como #4. (Todos os utilizadores, incluindo nos convidados, podem convidar os utilizadores de B2B).

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de utilizador de colaboração B2B](active-directory-b2b-user-properties.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Grupos dinâmicos e de colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração do B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicações SaaS colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Afirmações de utilizador de colaboração B2B mapeamento](active-directory-b2b-claims-mapping.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais de colaboração B2B](active-directory-b2b-current-limitations.md)
