---
title: "Desativar o utilizador inícios de sessão para uma aplicação empresarial no Azure Active Directory | Microsoft Docs"
description: "Como desativar uma aplicação empresarial para que nenhum utilizador pode iniciar sessão no-lo no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 4f587eb96aaa41ba2034eb9eaeb904f72c501abe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desativar o utilizador inícios de sessão para uma aplicação empresarial no Azure Active Directory
É fácil desativar uma aplicação empresarial para que nenhum utilizador pode iniciar sessão no-lo no Azure Active Directory (Azure AD). Tem de ter as permissões adequadas para gerir a aplicação da empresa e tem de ser administrador global do diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desativar inícios de sessão do utilizador?
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **mais serviços**, introduza **do Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3. No **do Azure Active Directory** -  ***directoryname*** painel (ou seja, o Azure AD painel para o diretório que está a gerir), selecione **aplicações empresariais**.

    ![Aplicações da empresa ao abrir](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. No **aplicações empresariais** painel, selecione **todas as aplicações**. É apresentada uma lista das aplicações que pode gerir.
5. No **aplicações da empresa - todas as aplicações** painel, selecione uma aplicação.
6. No ***appname*** painel (ou seja, o painel com o nome da aplicação selecionada no título), selecione **propriedades**.

    ![Selecionar o todos os comandos de aplicações](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. No ***appname*** - **propriedades** painel, selecione **não** para **ativado para os utilizadores iniciar sessão?**.
8. Selecione o **guardar** comando.

## <a name="next-steps"></a>Passos seguintes
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Alterar o nome ou logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
