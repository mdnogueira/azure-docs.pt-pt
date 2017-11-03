---
title: Como dar acesso ao Privileged Identity Management - Azure | Microsoft Docs
description: "Saiba como adicionar funções a utilizadores com a extensão do Azure Active Directory Privileged Identity Management para que consiga gerir PIM."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d4c53b53-2b37-41e6-813c-96ec08a1c897
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: aeaefb484b29da6e89c2c3c650a79a881b3fa5b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Conceder acesso para gerir o Azure AD Privileged Identity Management
Administrador global que permite o Azure AD Privileged Identity Management (PIM) para uma organização automaticamente obter as atribuições de funções e acesso a PIM. Ninguém mais obtém acesso de escrita por predefinição, no entanto, incluindo outros administradores globais. Outros adminstrators globais, administradores de segurança e leitores de segurança têm acesso só de leitura para o Azure AD PIM. Para conceder acesso a PIM, o primeiro utilizador pode atribuir outras pessoas para o **administrador com função privilegiada** função. Esta atribuição tem de ser efetuada a partir da PIM em si e não pode ser alterada através do PowerShell ou outros portais.

> [!NOTE]
> Gestão do Azure AD PIM requer o MFA do Azure. Uma vez que não é possível registar as contas Microsoft para o MFA do Azure, um utilizador que inicia sessão com uma conta Microsoft não é possível aceder a Azure AD PIM.
> 
> 

Certifique-se existem sempre, pelo menos, dois utilizadores numa função de administrador com função privilegiada, no caso de um utilizador está bloqueado ou a sua conta foi eliminada.

## <a name="give-another-user-access-to-manage-pim"></a>Atribua outro acesso de utilizador para gerir o PIM
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) e selecione o **do Azure AD Privileged Identity Management** aplicação no dashboard.
2. Selecione **gerir funções privilegiadas** > **administrador com função privilegiada** > **adicionar**.
   
    ![Adicionar administradores de com função privilegiada - captura de ecrã][1]
3. No painel adicionar os utilizadores geridos, o passo 1 já está concluído. Selecione passo 2, **selecionar utilizadores** e procure o utilizador que pretende adicionar.
   
    ![Selecione utilizadores - captura de ecrã][2]
4. Selecione o utilizador os resultados da pesquisa e clique em **feito**.
5. Clique em **OK** para guardar a sua seleção. O utilizador selecionou irá aparecer na lista de administradores de com função privilegiada.
   
   * Sempre que atribui uma nova função para alguém, estes são automaticamente configuradas como elegíveis para ativar a função. Se pretende torná-los permanente na função, clique o utilizador na lista. Selecione **tornar perm** no menu de informações de utilizador.
6. Enviar ao utilizador uma hiperligação para [introdução ao Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Remover direitos de acesso de outro utilizador para gerir o PIM
Antes de remover alguém da função de administrador com função privilegiada, sempre Certifique-se irá ainda ser dois utilizadores atribuídos ao mesmo.

1. No dashboard do PIM, clique na função de **administrador com função privilegiada**.  Será apresentada a lista de utilizadores atualmente da função.
2. Clique no utilizador na lista de utilizadores.
3. Clique em **remover**.  É-lhe apresentada uma mensagem de confirmação.
4. Clique em **Sim** para remover o utilizador da função.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
