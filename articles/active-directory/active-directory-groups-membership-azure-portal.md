---
title: Gira os grupos que o grupo pertence no Azure Active Directory | Microsoft Docs
description: "Os grupos podem conter outros grupos no Azure Active Directory. Eis como gerir essas associações."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 44911ec2c278f7af7b8ec4bd971bc97b342a8bf6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Gerir a que grupos de um grupo pertence no seu inquilino do Azure Active Directory
Os grupos podem conter outros grupos no Azure Active Directory. Eis como gerir essas associações.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Como posso encontrar os grupos dos quais o meu grupo é um membro?
1. Inicie sessão no [Centro de administradores do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
2. Selecione **utilizadores e grupos**.

   ![Utilizadores de abertura e de imagem de grupos](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Selecione **todos os grupos**.

   ![Selecionar a imagem dos grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Selecione um grupo.
2. Selecione **associações de grupo**.

   ![Imagem de membros do grupo de abertura](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Para adicionar o grupo como membro de outro grupo, o **grupo - associações de grupo** painel, selecione o **adicionar** comando.
2. Selecione um grupo do **selecionar grupo** painel e, em seguida, selecione o **selecione** na parte inferior do painel. Pode adicionar o grupo para apenas um grupo de cada vez. O **utilizador** caixa filtra a apresentação com base na correspondência a entrada de qualquer parte de um nome de utilizador ou dispositivo. Não existem carateres universais são aceites na caixa de.

   ![Adicionar uma associação de grupo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Para remover o grupo como membro de outro grupo, no **grupo - associações de grupo** painel, selecione um grupo.
9. Selecione o **remover** comando e confirme a sua escolha na linha de.

   ![Remova o comando de associação](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Quando terminar de alterar as associações de grupo para o grupo, selecione **guardar**.

## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Consulte os grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](active-directory-groups-create-azure-portal.md)
* [Gerir as definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir os membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerir regras dinâmicas para os utilizadores de um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
