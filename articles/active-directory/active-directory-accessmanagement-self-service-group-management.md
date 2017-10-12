---
title: "Configurar a gestão personalizada de acessos a aplicações no Azure Active Directory | Microsoft Docs"
description: "Criar e gerir grupos de segurança ou grupos do Office 365 no Azure Active Directory e pedir adesões ao grupo de segurança ou ao grupo do Office 365"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 7a7370eb076ba8602a58a260a14bb863c55bc803
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Configurar o Azure Active Directory para gestão de grupos personalizada
Os seus utilizadores podem criar e gerir os próprios grupos de segurança ou do Office 365 no Azure Active Directory (Azure AD). Os utilizadores também podem pedir um grupo de segurança ou as associações de grupos do Office 365 e, em seguida, o proprietário do grupo pode aceitar ou recusar a associação. O controlo diário da adesão ao grupo pode ser delegado a quem entenda o contexto empresarial dessa adesão. As funcionalidades de gestão de grupo personalizada estão disponíveis apenas para grupos de segurança e grupos do Office 365, mas não para grupos de segurança com capacidade de e-mail correio ou listas de distribuição.

A gestão de grupo personalizada destina-se atualmente a dois cenários essenciais: gestão de grupo delegada e gestão de grupo personalizada.

* **Gestão de grupo delegada** Um exemplo é um administrador que está a gerir o acesso a uma aplicação SaaS que a sua empresa está a utilizar. A gestão destes direitos de acesso está a tornar-se complexa, por isso este administrador pede ao proprietário da empresa que crie um novo grupo. O administrador atribui acesso à aplicação para o novo grupo e adiciona ao grupo todas as pessoas que estão a aceder à aplicação. O proprietário da empresa pode então adicionar mais utilizadores e estes são aprovisionados automaticamente para a aplicação. O proprietário da empresa não tem de aguardar que o administrador faça a gestão do acesso dos utilizadores. Se o administrador conceder a mesma permissão a um gestor num grupo empresarial diferente, essa pessoa também pode gerir o acesso para os respetivos utilizadores. O proprietário empresarial e o gestor não podem ver ou gerir os utilizadores dos outros. O administrador ainda pode ver todos os utilizadores que têm acesso à aplicação e bloquear os direitos de acesso, se for necessário.
* **Gestão de grupo personalizada** Um exemplo deste cenário são dois utilizadores com sites do SharePoint Online que configuraram independentemente. Pretendem conceder acesso às equipas uns dos outros com os respetivos sites. Para o conseguirem, podem criar um grupo no Azure AD e no SharePoint Online cada um deles seleciona esse grupo para fornecer acesso aos respetivos sites. Quando alguém quiser ter acesso, pode solicitá-lo no Painel de Acesso e, após a aprovação, obtém automaticamente acesso a ambos os sites do SharePoint Online. Posteriormente, um deles decide que todas as pessoas que acedem ao site devem também ter acesso a uma determinada aplicação SaaS. O administrador da aplicação SaaS pode adicionar direitos de acesso da aplicação ao site do SharePoint Online. A partir daí, quaisquer pedidos que ele aprove concedem acesso aos dois sites SharePoint Online e também a esta aplicação SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilizar um grupo para personalização pelo utilizador
1. Inicie sessão no [Centro de administradores do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
2. Selecione **Utilizadores e Grupos** e **Definições do grupo**.
3. Defina **Gestão de grupos personalizada ativada** como **Sim**.
4. Defina **Os utilizadores podem criar grupos de segurança** ou **Os utilizadores podem criar grupos do Office 365** como **Sim**.
  * Se estas definições estiverem ativada, todos os utilizadores do seu diretório estão autorizados a criar novos grupos de segurança e a adicionar membros aos mesmos. Estes novos grupos apareceriam também no Painel de Acesso para todos os outros utilizadores. Se a definição de política do grupo o permitir, outros utilizadores podem criar pedidos de adesão para estes grupos. 
  * Se estas definições estiverem desativadas, os utilizadores não podem criar grupos nem alterar os grupos existentes dos quais são proprietários. No entanto, podem continuar gerir os membros desses grupos e aprovar pedidos de outros utilizadores para pertencer aos respetivos grupos.

Também pode utilizar **Utilizadores que podem gerir grupos de segurança** e **Utilizadores que podem gerir grupos do Office 365** para obter um controlo de acesso mais refinado sobre a gestão de grupos personalizada para os seus utilizadores. Se **Os utilizadores podem criar grupos** estiver ativado, todos os utilizadores do seu inquilino estão autorizados a criar novos grupos e a adicionar membros aos mesmos. Ao defini-los como **Alguns**, está a restringir a gestão de grupos a apenas um grupo de utilizadores limitado. Quando este parâmetro estiver definido como **Alguns**, tem de adicionar utilizadores ao grupo SSGMSecurityGroupsUsers antes de eles poderem criar grupos novos e adicionar membros aos mesmos. Se definir **Utilizadores que podem utilizar a personalização em grupos de segurança** e **Utilizadores que podem gerir grupos do Office 365** como **Todos**, permitirá que todos os utilizadores do seu inquilino criem grupos novos.

Também pode utilizar **Grupo que pode gerir grupos de segurança** ou **Grupo que pode gerir grupos do Office 365** para especificar um grupo individual cujos membros podem utilizar a personalização.

## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Manage access to resources with Azure Active Directory groups](active-directory-manage-groups.md) (Gerir o acesso aos recursos com grupos do Azure Active Directory)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar as identidades no local no Azure Active Directory](active-directory-aadconnect.md)
