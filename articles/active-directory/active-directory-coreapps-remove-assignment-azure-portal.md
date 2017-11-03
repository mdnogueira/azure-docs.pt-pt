---
title: "Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory | Microsoft Docs"
description: "Como remover a atribuição de acesso de um utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 99cbc54b4daa988dbf741275ce92d1c863af6720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory
É fácil remover um utilizador ou um grupo de que está a ser atribuído o acesso a uma das suas aplicações da empresa no Azure Active Directory (Azure AD). Tem de ter as permissões adequadas para gerir a aplicação da empresa e tem de ser administrador global do diretório.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Como remover um utilizador ou a atribuição de grupo?
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **mais serviços**, introduza **do Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3. No **Azure Active Directory - *directoryname***  painel (ou seja, o Azure AD painel para o diretório que está a gerir), selecione **aplicações empresariais**.

    ![Aplicações da empresa ao abrir](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. No **aplicações empresariais** painel, selecione **todas as aplicações**. Verá uma lista de aplicações que pode gerir.
5. No **aplicações da empresa - todas as aplicações** painel, selecione uma aplicação.
6. No ***appname*** painel (ou seja, o painel com o nome da aplicação selecionada no título), selecione **utilizadores e grupos**.

    ![Selecionar utilizadores ou grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. No ***appname*** **-atribuição de grupos de & utilizador** painel, selecione uma das mais utilizadores ou grupos e, em seguida, selecione o **remover** comando. Confirme a sua decisão de linha.

    ![Selecionar o comando Remove](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Passos seguintes
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Desativar o utilizador inícios de sessão para uma aplicação empresarial](active-directory-coreapps-disable-app-azure-portal.md)
* [Alterar o nome ou logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
