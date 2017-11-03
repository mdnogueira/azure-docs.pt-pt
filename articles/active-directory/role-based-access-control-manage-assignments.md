---
title: "Ver as atribuições de acesso de recursos do Azure | Microsoft Docs"
description: "Ver e gerir todas as atribuições de controlo de acesso baseado em funções para qualquer utilizador ou grupo no portal do Azure"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: andredm
ms.openlocfilehash: 72c695d08bdf5de003d51ffb0768184e1e4d00ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Vista atribuições de acesso para utilizadores e grupos no portal do Azure
> [!div class="op_single_selector"]
> * [Gerir o acesso por utilizador ou grupo](role-based-access-control-manage-assignments.md)
> * [Gerir o acesso por recurso](role-based-access-control-configure.md)

Com o controlo de acesso baseado em funções (RBAC) no Azure Active Directory (Azure AD), pode gerir o acesso aos seus recursos do Azure. 

Acesso atribuído com RBAC é detalhado porque pode restringir as permissões de duas formas:

* **Âmbito:** atribuições de funções RBAC estão confinadas a uma subscrição específica, o grupo de recursos ou o recurso. Um utilizador dado acesso a um único recurso não é possível aceder a quaisquer outros recursos na mesma subscrição.
* **Função:** no âmbito da atribuição da, o acesso é narrowed ainda mais ao atribuir uma função. Funções podem ser alto nível, como o proprietário ou específico, como o leitor de máquina virtual.

Só podem ser atribuídas a funções de dentro da subscrição, o grupo de recursos ou o recurso que o âmbito de atribuição. Mas pode ver todas as atribuições de acesso para um grupo ou utilizador especificado num único local. Pode ter até 2000 atribuições de funções em cada subscrição. 

Obter mais informações sobre como [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Atribuições de acesso de vista
Para procurar as atribuições de acesso de um único utilizador ou grupo, iniciar no Azure Active Directory no [portal do Azure](http://portal.azure.com).

1. Selecione **do Azure Active Directory**. Se esta opção não estiver visível na sua lista de navegação, selecione **mais serviços** e, em seguida, desloque para baixo para localizar **do Azure Active Directory**.
2. Selecione **utilizadores e grupos**e, em seguida, **todos os utilizadores** ou **todos os grupos**. Neste exemplo, iremos focar-se os utilizadores individuais.
    ![Gerir utilizadores e grupos no Azure Active Directory - captura de ecrã](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Procure o utilizador por nome ou nome de utilizador.
4. Selecione **Recursos do Azure** no painel do utilizador. São apresentadas todas as atribuições de acesso desse utilizador.

### <a name="read-permissions-to-view-assignments"></a>Permissões de leitura para ver a atribuições
Esta página apenas apresenta as atribuições de acesso que terá permissão para ler. Por exemplo, pode ter acesso de leitura para a subscrição A e aceda ao painel de recursos do Azure para verificar as atribuições de um utilizador. Pode ver o respetiva atribuições de acesso para a subscrição A, mas não pode ver que ela também tem atribuições de acesso na subscrição B.

## <a name="delete-access-assignments"></a>Eliminar atribuições de acesso
A partir deste painel, pode eliminar as atribuições de acesso que foram atribuídas diretamente a um utilizador ou grupo. Se a atribuição de acesso foi herdada de um grupo principal, terá de ir para o recurso ou na subscrição e gerir a atribuição não existe.

1. Na lista de todas as atribuições de acesso para um utilizador ou grupo, selecione aquele que pretende eliminar.
2. Selecione **remover** e, em seguida, **Sim** para confirmar.
    ![Remover atribuição de acesso - captura de ecrã](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>Passos seguintes

* Introdução ao controlo de acesso baseado em funções para [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](role-based-access-control-configure.md)
* Consulte as [Funções incorporadas do RBAC](role-based-access-built-in-roles.md)

