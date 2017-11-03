---
title: "Utilização de um grupo para gerir o acesso a aplicações SaaS | Microsoft Docs"
description: "Como utilizar grupos no Azure Active Directory Premium ou Basic atribuir acesso a aplicações SaaS que estão integradas com o Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 818f4b515926c35078b3118978f3accbf3bbb65b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Utilizar um grupo para gerir o acesso a aplicações SaaS
Utilizar o Azure Active Directory (Azure AD) com uma licença do Azure AD Premium ou Basic do Azure AD, pode utilizar grupos para atribuir acesso a uma aplicação SaaS que está integrada com o Azure AD. Por exemplo, se pretender atribuir acesso para o departamento de marketing utilizar cinco diferentes aplicações SaaS, pode criar um grupo que contenha os utilizadores do departamento de marketing e, em seguida, atribuir esse grupo a estas cinco aplicações SaaS que são necessários pelo departamento de marketing. Desta forma, que pode poupar tempo ao gerir a associação do departamento de marketing num único local. Os utilizadores, em seguida, são atribuídos à aplicação quando estes são adicionados como membros do grupo de marketing, e as respetivas atribuições removidas da aplicação quando os mesmos serão removidos do grupo de marketing. Esta capacidade pode ser utilizada com centenas de aplicações que pode adicionar a partir de dentro da Galeria de aplicações do Azure AD.

> [!IMPORTANT]
> Pode utilizar esta funcionalidade depois de iniciar uma versão de avaliação do Azure AD Premium ou adquirir licenças do Azure AD Premium ou Basic do Azure AD.
> Filiações aninhadas em grupos não são suportadas atualmente para uma atribuição a aplicações baseada em grupos.

**Atribuir acesso para um utilizador ou grupo a uma aplicação SaaS**

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **aplicações empresariais**.
2. Selecione uma aplicação que adicionou da Galeria de aplicações para abri-lo.
3. Selecione **utilizadores e grupos**e, em seguida, selecione **adicionar utilizador**.
4. No **adicionar atribuição**, selecione **utilizadores e grupos** para abrir o **utilizadores e grupos** lista de seleção.
6. Selecione o número de grupos ou utilizadores como quiser, em seguida, clique ou toque em **selecione** adicioná-los para o **adicionar atribuição** lista. Também pode atribuir uma função a um utilizador nesta fase.
7. Selecione **atribuir** para atribuir os utilizadores ou grupos à aplicação empresarial selecionado.

### <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
