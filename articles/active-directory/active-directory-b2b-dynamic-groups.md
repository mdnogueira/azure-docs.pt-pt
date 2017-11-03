---
title: "Grupos dinâmicos e de colaboração B2B do Azure Active Directory do | Microsoft Docs"
description: "Colaboração do Azure Active Directory B2B pode ser utilizada com grupos dinâmicos do Azure AD"
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
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: sasubram
ms.openlocfilehash: 5818c41610c8c5df89abcb0dcd058bcbe9579ce7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e de colaboração B2B do Azure Active Directory do

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
Configuração dinâmica de associação ao grupo de segurança do Azure Active Directory (Azure AD) está disponível no [portal do Azure](https://portal.azure.com). Os administradores podem definir regras para preencher os grupos que são criados no Azure Active Directory com base nos atributos de utilizador (tais como userType, departamento ou país). Os membros podem ser automaticamente adicionados ou removidos de um grupo de segurança com base nos respetivos atributos. Estes grupos podem fornecer acesso a aplicações ou recursos de nuvem (sites do SharePoint, documentos) e atribuir licenças aos membros. Leia mais informações sobre grupos dinâmicos no [dedicado grupos no Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Apropriados [licenciamento do Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) é necessário para criar e utilizar grupos dinâmicos. Saiba mais no artigo [criar regras baseadas em atributos para filiação dinâmica em grupos no Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Quais são os grupos dinâmicos incorporados?
O **todos os utilizadores** grupo dinâmico permite aos administradores de inquilinos criar um grupo que contém todos os utilizadores no inquilino com um único clique. Por predefinição, o **todos os utilizadores** grupo inclui todos os utilizadores no diretório, incluindo membros e nos convidados.
Dentro do novo portal de administração do Azure Active Directory, pode optar por ativar o **todos os utilizadores** grupo na vista de definições do grupo.

![grupos incorporados](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Proteger a todos os utilizadores de grupo dinâmico
Por predefinição, o **todos os utilizadores** grupo contém, bem como os utilizadores de (convidado) de colaboração do B2B. Pode proteger ainda mais a **todos os utilizadores** grupo utilizando uma regra para remover os utilizadores convidados. A seguinte ilustração mostra a **todos os utilizadores** grupo modificado para excluir os convidados.

![permitir que todos os grupo de utilizadores](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Poderá também achar úteis para criar um novo grupo dinâmico que contém apenas os utilizadores convidados, para que pode aplicar políticas (por exemplo, políticas de acesso condicional do Azure AD) aos mesmos.
Um grupo poderá aspeto:

![excluir os utilizadores convidados](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de utilizador de colaboração B2B](active-directory-b2b-user-properties.md)
* [A adição de um utilizador de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites para colaboração do B2B](active-directory-b2b-delegate-invitations.md)
* [Código de colaboração do B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicações SaaS colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de utilizador de colaboração B2B](active-directory-b2b-user-token.md)
* [Afirmações de utilizador de colaboração B2B mapeamento](active-directory-b2b-claims-mapping.md)
* [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais de colaboração B2B](active-directory-b2b-current-limitations.md)
