---
title: "Ocultar uma aplicação de terceiros da experiência do utilizador no Azure Active Directory | Microsoft Docs"
description: "Como ocultar uma aplicação de terceiros da experiência do utilizador no Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 976cbb1341493186b9996d250ebca8f2f3688fdf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Ocultar uma aplicação de terceiros da experiência do utilizador no Azure Active Directory

Se tiver uma aplicação de terceiros (uma aplicação publicada por outras pessoas que Microsoft) que não pretende ser mostrada no painel de acesso dos utilizadores ou do Office 365 launchers, há uma opção de ocultar este mosaico da aplicação. Por ocultar os utilizadores da aplicação ainda tem permissões para a aplicação, mas não irão vê-las aparecer no respetivos launchers de aplicação. Tem de ter as permissões adequadas para gerir a aplicação da empresa e tem de ser administrador global do diretório.

## <a name="hiding-a-third-party-app-from-a-users-experience"></a>Ocultar uma aplicação de terceiros da experiência do utilizador
Utilize os seguintes passos para ocultar uma aplicação de terceiros do painel de acesso de um utilizador e launchers de aplicação do Office 365

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Como posso ocultar uma aplicação de terceiros do painel de acesso de utilizador e launchers de aplicação do Office 365?

1.  Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2.  Selecione **mais serviços**, introduza **do Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3.  No **Azure Active Directory - *directoryname***  ecrã (ou seja, o Azure AD ecrã para o diretório que está a gerir), selecione **aplicações empresariais**.
![Aplicações da empresa](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  No **aplicações empresariais** ecrã, selecione **todas as aplicações**. É apresentada uma lista das aplicações que pode gerir.
5.  No **aplicações da empresa - todas as aplicações** ecrã, selecione uma aplicação.</br>
![Aplicações da empresa](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  No ***appname*** ecrã (ou seja, ecrã de com o nome da aplicação selecionada no título), selecione propriedades.
7.  No  ***appname* -propriedades** ecrã, selecione **Sim** para **Visible aos utilizadores?**.
![Aplicações da empresa](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Selecione o **guardar** comando.

## <a name="next-steps"></a>Passos seguintes
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Alterar o nome ou logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
