---
title: "Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory | Microsoft Docs"
description: "Como selecionar uma aplicação empresarial para atribuir um utilizador ou grupo ao mesmo no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.openlocfilehash: 8e61044f261033a473241e2de152026bf49c4c70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory
É fácil atribuir um utilizador ou um grupo às suas aplicações da empresa no Azure Active Directory (Azure AD). Tem de ter as permissões adequadas para gerir a aplicação da empresa e tem de ser administrador global do diretório.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Como atribuir acesso de utilizador para uma aplicação da empresa?
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **mais serviços**, introduza Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
3. No **Azure Active Directory - *directoryname***  painel (ou seja, o Azure AD painel para o diretório que está a gerir), selecione **aplicações empresariais**.

    ![Aplicações da empresa ao abrir](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. No **aplicações empresariais** painel, selecione **todas as aplicações**. Verá uma lista de aplicações que pode gerir.
5. No **aplicações da empresa - todas as aplicações** painel, selecione uma aplicação.
6. No ***appname*** painel (ou seja, o painel com o nome da aplicação selecionada no título), selecione **utilizadores e grupos**.

    ![Selecionar o todos os comandos de aplicações](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. No ***appname*** **-atribuição de grupos de & utilizador** painel, selecione o **adicionar** comando.
8. No **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![Atribuir um utilizador ou grupo para a aplicação](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. No **utilizadores e grupos** painel, selecione um ou mais utilizadores ou grupos na lista e, em seguida, selecione o **selecione** na parte inferior do painel.
10. No **adicionar atribuição** painel, selecione **função**. Em seguida, no **selecionar função** painel, selecione uma função para aplicar a utilizadores ou grupos selecionados e, em seguida, selecione o **OK** na parte inferior do painel.
11. No **adicionar atribuição** painel, selecione o **atribuir** na parte inferior do painel. Os utilizadores atribuídos ou grupos terá as permissões definidas pela função selecionada para esta aplicação empresarial.

## <a name="next-steps"></a>Passos seguintes
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Desativar o utilizador inícios de sessão para uma aplicação empresarial](active-directory-coreapps-disable-app-azure-portal.md)
* [Alterar o nome ou logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
