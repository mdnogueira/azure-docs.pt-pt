---
title: "No Azure Active Directory de reposição de palavra-passe | Microsoft Docs"
description: "Administrador iniciada reposição palavra-passe de um utilizador no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Repor a palavra-passe de um utilizador no Azure Active Directory

Os administradores poderão ter de repor a palavra-passe de um utilizador em casos onde esqueceu, foram bloqueado fora ou outros cenários. Os passos que se seguem descrevem o repor palavra-passe de um utilizador.

## <a name="how-to-reset-the-password-for-a-user"></a>Como repor a palavra-passe para um utilizador

1. Iniciar sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que tenha permissões de diretório para repor palavras-passe do utilizador.
2. Selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione o utilizador que pretende repor a palavra-passe.
2. Para o utilizador selecionado, selecione **Repor palavra-passe**.

    ![Repor uma palavra-passe para um utilizador do perfil de utilizador no Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. No **Repor palavra-passe**, selecione **Repor palavra-passe**.
7. Uma palavra-passe temporária é apresentada a que, em seguida, pode fornecer ao utilizador. O utilizador será pedido para alterar a palavra-passe da próxima vez que iniciar sessão. 

   > [!NOTE]
   > Esta palavra-passe temporária não tem uma hora de expiração para que esta será válida até que iniciar sessão e que são, em seguida, são forçadas para alterá-la. 

## <a name="next-steps"></a>Passos seguintes
* [Adicionar um utilizador](active-directory-users-create-azure-portal.md)
* [Atribuir funções de administrador a um utilizador](active-directory-users-assign-role-azure-portal.md)
* [Gerir perfis de utilizador](active-directory-users-profile-azure-portal.md)
* [Eliminar um utilizador no Azure AD](active-directory-users-delete-user-azure-portal.md)
