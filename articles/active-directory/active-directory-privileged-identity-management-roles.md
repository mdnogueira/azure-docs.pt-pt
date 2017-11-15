---
title: "Funções no Azure AD Privileged Identity Management | Microsoft Docs"
description: "Saiba que funções são utilizadas para as identidades privilegiadas com a extensão do Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e3f67b978ff66cbb71709f2f8d66986a33149ae6
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Função administrativa diferentes no Azure Active Directory PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Pode atribuir utilizadores na sua organização para diferentes funções administrativas no Azure AD. Estes atribuições de funções controlam as tarefas, tais como adicionar ou remover utilizadores ou alterar as definições do serviço, os utilizadores são capazes de efetuar no Azure AD, Office 365 e outros serviços Online da Microsoft e aplicações ligadas.  

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo.

Um administrador global pode atualizar-se que os utilizadores são **permanentemente** atribuídos a funções no Azure AD, utilizando cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`, ou através do portal clássico, conforme descrito em [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

O Azure AD Privileged Identity Management (PIM) gere as políticas de acesso privilegiado para os utilizadores no Azure AD. PIM atribui os utilizadores a uma ou mais funções no Azure AD e pode atribuir a alguém ser permanentemente a função, ou elegíveis para a função. Quando um utilizador é permanentemente atribuído a uma função ou ativa uma atribuição de função elegíveis, em seguida, pode gerir do Azure Active Directory, Office 365 e outras aplicações com as permissões atribuídas às respetivas funções.

Não há qualquer diferença no acesso aos alguém com um permanente versus uma atribuição de função elegível. A única diferença é que algumas pessoas não precisam de que o acesso o tempo. Estes são efetuados elegíveis para a função e podem ativá-la e desativar sempre que precisam.

## <a name="roles-managed-in-pim"></a>Funções geridas no PIM
Privileged Identity Management permite-lhe atribuir utilizadores a funções de administrador comuns, incluindo:

* **Administrador global** (também conhecido como administrador de empresa) tem acesso a todas as funcionalidades administrativas. Pode ter mais do que um administrador global da sua organização. A pessoa que se inscreve para comprar o Office 365 automaticamente torna-se um administrador global.
* **Administrador com função privilegiada** gere o Azure AD PIM e atualiza as atribuições de funções para outros utilizadores.  
* **Administrador de faturação** efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o estado de funcionamento do serviço.
* **Administrador de palavras-passe** repõe palavras-passe, gere pedidos de serviço e monitoriza o estado de funcionamento do serviço. Administradores de palavras-passe estão limitadas a repor palavras-passe para os utilizadores.
* **Administrador de serviço** gere pedidos de serviço e monitoriza o estado de funcionamento do serviço.
  
  > [!NOTE]
  > Se estiver a utilizar o Office 365, em seguida, antes de atribuir a função de administrador de serviço a um utilizador, primeiro atribua ao utilizador permissões administrativas para um serviço, como o Exchange Online.
  > 
  > 
* **Administrador de gestão de utilizador** repõe palavras-passe, monitoriza o estado de funcionamento do serviço e gere contas de utilizador, grupos de utilizadores e pedidos de serviço. O administrador de gestão de utilizador não é possível eliminar um administrador global, crie outras funções de administrador ou repor palavras-passe para faturação, globais e administradores de serviço.
* **Administrador do Exchange** tenha acesso administrativo para o Exchange Online através do Centro de administração do Exchange (CA) e pode realizar quase qualquer tarefa no Exchange Online.
* **Administrador do SharePoint** tenha acesso administrativo ao SharePoint Online através do Centro de administração do SharePoint Online e pode realizar quase qualquer tarefa no SharePoint Online.
* **Skype para o administrador da empresa** tenha acesso administrativo ao Skype para empresas através do Skype para o Centro de administração de negócio e pode realizar quase todas as tarefas no Skype para empresas Online.

Ler estes artigos para obter mais detalhes sobre [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles-azure-portal.md) e [atribuir funções de administrador no Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Do PIM, pode [atribuir estas funções a um utilizador](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que o utilizador pode [ativar a função quando for necessário](active-directory-privileged-identity-management-how-to-activate-role.md).

Se pretende conceder acesso de utilizadores de outra para gerir no PIM em si, as funções que PIM exige que o utilizador ter são descritas mais no [como dar acesso ao PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Funções não são geridas no PIM
Funções no Exchange Online ou o SharePoint Online, com exceção dos mencionado acima, não estão representadas no Azure AD e, por isso, não são visíveis no PIM. Para obter mais informações sobre como alterar as atribuições de funções de detalhado nestes serviços do Office 365, consulte [permissões no Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

As subscrições do Azure e grupos de recursos também não são representados no Azure AD. Para gerir as subscrições do Azure, consulte [como adicionar ou alterar funções de administrador do Azure](../billing/billing-add-change-azure-subscription-administrator.md) e para obter mais informações, consulte Azure RBAC [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Funções de utilizador e o início de sessão
Para alguns serviços e aplicações Microsoft, a atribuição de um utilizador a uma função não pode ser suficiente para permitir que o utilizador seja um administrador.

Acesso ao portal clássico do Azure requer que o utilizador ser um administrador de serviço ou coadministrador de uma subscrição do Azure, mesmo que o utilizador não precisa de gerir as subscrições do Azure.  Por exemplo, para gerir as definições de configuração para o Azure AD no portal clássico, um utilizador tem de ser um administrador global no Azure AD e um coadministrador da subscrição numa subscrição do Azure.  Para saber como adicionar utilizadores a subscrições do Azure, consulte [como adicionar ou alterar funções de administrador do Azure](../billing/billing-add-change-azure-subscription-administrator.md).

Acesso a Serviços Online da Microsoft pode exigir que o utilizador também ser atribuída uma licença antes de poderem abrir o portal do serviço ou efetuar tarefas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Atribuir uma licença a um utilizador no Azure AD
1. Iniciar sessão para o [portal clássico do Azure](http://manage.windowsazure.com) com uma conta de administrador global ou de uma conta de administrador conjunta.
2. Selecione **todos os itens** no menu principal.
3. Selecione o diretório que pretende trabalhar e que tenha licenças associadas à mesma.
4. Selecione **licenças**. Será apresentada a lista de licenças disponíveis.
5. Selecione o plano de licença que contém as licenças que pretende distribuir.
6. Selecione **atribuir utilizadores**.
7. Selecione o utilizador que pretende atribuir uma licença.
8. Clique em de **atribuir** botão.  O utilizador pode agora iniciar sessão no Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

