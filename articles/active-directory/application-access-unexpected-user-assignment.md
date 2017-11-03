---
title: "Como atribuir utilizadores a aplicações | Microsoft Docs"
description: "Compreender a forma como os utilizadores são atribuídos a uma aplicação no seu inquilino"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 916238ba402a2555bac620d7f08e02799d981ae0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-assign-users-to-applications"></a>Como atribuir utilizadores a aplicações

Este artigo ajuda-o a compreender a forma como os utilizadores são atribuídos a uma aplicação no seu inquilino.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como os utilizadores são atribuídos a uma aplicação no Azure AD?

Para um utilizador aceder a uma aplicação, tem primeiro de atribuir ao mesmo de alguma forma. Atribuição pode ser efetuada por um administrador, um delegado de negócio ou, por vezes, o utilizador o próprios. Abaixo descreve as formas como podem obter atribuído aos utilizadores para aplicações:

1.  Um administrador [atribui um utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) diretamente da aplicação

2.  Um administrador [atribui um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) que o utilizador é membro da aplicação, incluindo:

  * Um grupo que foi sincronizado no local

  * Um grupo de segurança estático criado na nuvem

  * A [grupo de segurança dinâmica](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criados na nuvem

  * Um grupo do Office 365 criado na nuvem

  * O [todos os utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) grupo

3.  Um administrador ativa [Self-Service de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicionar uma aplicação utilizando o [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Adicionar aplicação** funcionalidade **sem a aprovação de negócio**

4.  Um administrador ativa [Self-Service de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um utilizador adicionar uma aplicação utilizando o [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Adicionar aplicação** funcionalidade, mas apenas w**ith aprovação anterior de um conjunto selecionado de aprovadores de negócio**

5.  Um administrador ativa [Self-Service de grupo de gestão](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um utilizador aderir a um grupo que uma aplicação é atribuída ao **sem a aprovação de negócio**

6.  Um administrador ativa [Self-Service de grupo de gestão](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um utilizador aderir a um grupo que uma aplicação é atribuída a, mas apenas **com aprovação anterior de um conjunto selecionado de aprovadores de negócio**

7.  Um administrador atribui uma licença a um utilizador diretamente para uma primeira aplicação de terceiros, como [Microsoft Office 365](http://products.office.com/)

8.  Um administrador atribui uma licença a um grupo que o utilizador é um membro a uma primeira aplicação de terceiros, como [Microsoft Office 365](http://products.office.com/)

9.  Um [administrador permitir a uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) a serem utilizados por todos os utilizadores e, em seguida, um utilizador inicia sessão na aplicação

10. Um utilizador [permitir a uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) próprios pelo início de sessão na aplicação

## <a name="next-steps"></a>Passos seguintes
[Gestão de aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md)
