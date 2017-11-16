---
title: "Como adicionar ou remover uma função de utilizador | Microsoft Docs"
description: "Saiba como adicionar funções ao identidades privilegiadas com a aplicação do Azure Active Directory Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim;oldportal;it-pro;
ms.openlocfilehash: af1ed4fab00d5304a25d28149e2ce9adcc3168fd
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Privileged Identity Management do Azure AD: como adicionar ou remover uma função de utilizador
Com o Azure Active Directory (AD), um administrador global (ou o administrador da empresa) pode atualizar que os utilizadores são **permanentemente** atribuídos a funções no Azure AD. Isto é feito com os cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`. Ou, podem utilizar o portal clássico do Azure conforme descrito em [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

A aplicação Azure AD Privileged Identity Management permite aos administradores de com função privilegiada tornar atribuições de funções permanente, bem. Além disso, os administradores de com função privilegiada podem tornar utilizadores **elegível** para funções de administrador. Um administrador elegível pode ativar a função quando precisarem e, em seguida, as respetivas permissões expirarem assim que estiver pronto.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gerir funções com PIM no portal do Azure
Na sua organização, pode atribuir utilizadores para diferentes funções administrativas no Azure AD, Office 365 e a outros serviços e aplicações Microsoft.  Obter mais detalhes sobre as funções disponíveis podem ser encontrados em [funções no Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Para adicionar ou remover um utilizador numa função utilizando o Privileged Identity Management, abra o dashboard do PIM. Em seguida, clique o **utilizadores nas funções de administrador** botão ou selecione uma função específica (por exemplo, o Administrador Global) da tabela de funções.

> [!NOTE]
> Se ainda não ativou PIM no portal do Azure, aceda a [introdução ao Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) para obter mais detalhes.

Se pretende conceder acesso de outro utilizador para PIM em si, as funções que PIM exige que o utilizador ter são descritas mais no [como dar acesso ao PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Adicionar um utilizador a uma função
1. No [portal do Azure](https://portal.azure.com/), selecione o **do Azure AD Privileged Identity Management** mosaico no dashboard.
2. Selecione **gerir funções privilegiadas**.
3. No **resumo de função** tabela, selecione a função que pretende gerir.
4. No painel da função, selecione **adicionar**.
5. Clique em **selecionar utilizadores** e procure o utilizador no **selecionar utilizadores** painel.  
6. Selecione o utilizador na lista de resultados de pesquisa e clique em **feito**.
7. Clique em **OK** para guardar a sua seleção. O utilizador selecionou irá aparecer na lista como elegível para a função.

> [!NOTE]
> Os novos utilizadores numa função só são elegíveis para a função por predefinição. Se pretender que a função permanente, clique o utilizador na lista. As informações do utilizador serão apresentado um novo painel. Selecione **disponibilizar perm** no menu de informações de utilizador.  
> Se um utilizador não é possível registar o para o Azure multi-factor Authentication (MFA), ou está a utilizar uma conta Microsoft (normalmente @outlook.com), tem de torná-los permanente em todas as respetivas funções. Administradores elegíveis-lhe pedidos para se registar na MFA durante a ativação.

Agora que o utilizador é elegível para uma função, informar de que estes podem ativá-lo, de acordo com as instruções em [como ativar ou desativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Remover um utilizador a partir de uma função
Pode remover utilizadores da atribuições de funções elegíveis, mas certifique-se de que existe sempre, pelo menos, um utilizador que seja um administrador global permanente.

Siga estes passos para remover um utilizador específico a partir de uma função:

1. Navegue para a função na lista de função ou selecionando uma função no dashboard do Azure AD PIM ao clicar no **utilizadores nas funções de administrador** botão.
2. Clique no utilizador na lista de utilizadores.
3. Clique em **remover**. Uma mensagem irá pedir-lhe para confirmar.
4. Clique em **Sim** para remover a função de utilizador.

Se não tiver a certeza de que os utilizadores terão as respetivas atribuições de funções, em seguida, pode [iniciar uma revisão do acesso para a função](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

