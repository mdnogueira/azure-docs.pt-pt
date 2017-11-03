---
title: "Passos para utilizar grupos de gestão de acesso | Microsoft Docs"
description: "Avançadas como-para gerir grupos de segurança e como utilizar estes grupos para gerir o acesso a um recurso."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: e1b88f7d43ba29589e5f1f4a8036c716ae984ae4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-owners-for-a-group"></a>Gerir proprietários de um grupo
Assim que um proprietário de recursos foi atribuído o acesso a um recurso a um grupo do Azure AD, a associação do grupo é gerida pelo proprietário do grupo. O proprietário do recurso delega efetivamente a permissão para atribuir aos utilizadores para o recurso para o proprietário do grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar um proprietário para um grupo

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **utilizadores e grupos**.
2. Selecione **todos os grupos**e, em seguida, abra o grupo que pretende adicionar os proprietários.
3. Selecione **adicionar proprietários**.
4. No **adicionar proprietários** página, selecione o utilizador que pretende adicionar como o proprietário deste grupo e certificar-se de que este nome é adicionado para o **selecionados** painel.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **utilizadores e grupos**.
2. Selecione **todos os grupos**e, em seguida, abra o grupo a partir do qual pretende remover os proprietários.
3. Selecione o **proprietários** separador.
4. Selecione o proprietário de que pretende remover deste grupo e, em seguida, selecione **remover**.

## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
