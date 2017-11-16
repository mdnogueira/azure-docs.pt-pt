---
title: Criar um grupo de utilizadores no Azure Active Directory | Microsoft Docs
description: Como criar um grupo no Azure Active Directory e adicionar membros ao grupo
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c36ffd0a567b665d6ea0d02d2aa0171965d1b8b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Crie um grupo e adicionar membros no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Este artigo explica como criar e preencher um novo grupo no Azure Active Directory. Utilize um grupo para efetuar tarefas de gestão, tais como atribuir licenças ou permissões para um número de utilizadores ou dispositivos em simultâneo.

## <a name="how-do-i-create-a-group"></a>Como crio um grupo?
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **mais serviços**, introduza **grupos de utilizadores e** na caixa de texto e, em seguida, selecione **Enter**.

   ![Gestão de utilizadores de abertura](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. No **utilizadores e grupos** painel, selecione **todos os grupos**.

   ![Abrir o painel de grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. No **utilizadores e grupos - todos os grupos** painel, selecione o **adicionar** comando.

   ![Selecionar o comando Adicionar](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. No **grupo** painel, adicionar um nome e descrição para o grupo.
6. Para selecionar membros para adicionar ao grupo, selecione **atribuído** no **tipo de associação** caixa e, em seguida, selecione **membros**. Para obter mais informações sobre como gerir dinamicamente a filiação de um grupo, consulte [utilizar atributos para criar regras avançadas para a associação de grupo](active-directory-groups-dynamic-membership-azure-portal.md).

   ![Selecionar Membros para adicionar](./media/active-directory-groups-create-azure-portal/select-members.png)
7. No **membros** painel, selecione um ou mais utilizadores ou dispositivos a adicionar ao grupo e selecione o **selecione** na parte inferior do painel para adicioná-los ao grupo. O **utilizador** caixa filtra a apresentação com base na correspondência a entrada de qualquer parte de um nome de utilizador ou dispositivo. Não existem carateres universais são aceites na caixa de.
8. Quando acabar de adicionar membros ao grupo, selecione **criar** no **grupo** painel.    

   ![Criar a confirmação de grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Consulte os grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerir as definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir os membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas para os utilizadores de um grupo](active-directory-groups-dynamic-membership-azure-portal.md)
