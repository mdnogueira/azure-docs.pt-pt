---
title: "Conclua uma revisão do acesso dos membros de um grupo ou o acesso dos utilizadores a uma aplicação com o Azure AD | Microsoft Docs"
description: "Saiba como realizar uma revisão do acesso para os membros de um grupo ou os utilizadores com acesso a uma aplicação no Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b301ff06c01d51c02f7d7393801b35cd8965403c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Conclua uma revisão do acesso dos membros de um grupo ou o acesso dos utilizadores a uma aplicação no Azure AD

Os administradores podem utilizar o Azure Active Directory (Azure AD) para [criar uma revisão do acesso](active-directory-azure-ad-controls-create-access-review.md) para membros do grupo ou os utilizadores atribuídos a uma aplicação. Azure AD envia automaticamente revisores uma mensagem de e-mail que lhe solicita informações para rever o acesso. Se um utilizador não receber uma mensagem de e-mail, pode enviar-lhes as instruções [rever o seu acesso](active-directory-azure-ad-controls-perform-access-review.md). O período de revisão do acesso ou se um administrador deixa a revisão do acesso, siga os passos neste artigo para ver e aplicar os resultados.

## <a name="view-an-access-review-in-the-azure-portal"></a>Ver uma revisão do acesso no portal do Azure

1. Vá para o [acesso revê página](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), selecione **programas**e selecione o programa que contém o controlo de revisão do acesso.

2. Selecione **gerir**e selecione o controlo de revisão do acesso. Se existirem vários controlos no programa, pode filtrar para controlos de um tipo específico e ordenação pelo respetivo estado. Também pode procurar por nome de revisão de controlo de acesso ou o nome a apresentar do proprietário que o criou. 

## <a name="stop-a-review-that-hasnt-finished"></a>Parar uma revisão de que não foi concluída

Se a revisão ainda não atingiu a data de fim agendada, um administrador pode selecionar **parar** para terminar a revisão antecipadamente. Depois de interromper a revisão, os utilizadores já não podem ser revistos. Não é possível reiniciar uma revisão depois está parado.

## <a name="apply-the-changes"></a>Aplicar as alterações 

Depois de concluída a revisão do acesso, porque atingiu a data de fim ou um administrador que o impediu manualmente, selecione **aplicar**. O resultado de revisão é implementado atualizando o grupo ou aplicação. Se acesso um utilizador foi negado na revisão, quando um administrador seleciona esta opção, o Azure AD remove a atribuição de associação ou aplicação. 

Selecionar **aplicar** não tem um efeito de um grupo que origina um diretório no local ou um grupo dinâmico. Se pretender alterar um grupo que tem origem no local, transfira os resultados e aplicar essas alterações para a representação do grupo no diretório.

## <a name="download-the-results-of-the-review"></a>Transferir os resultados de revisão

Para obter os resultados de revisão, selecione **aprovações** e, em seguida, selecione **transferir**. O ficheiro CSV resultante pode ser visualizado no Excel ou em outros programas que abrem ficheiros CSV.

## <a name="optional-delete-a-review"></a>Opcional: Eliminar uma revisão
Se já não estiver interessado em de revisão, podem eliminá-lo. Selecione **eliminar** para remover a revisão do Azure AD.

> [!IMPORTANT]
> Não há nenhum aviso antes de ocorre a eliminação, por isso, lembre-se de que pretende eliminar a revisão.
> 
> 

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Gerir o acesso de convidado com as revisões de acesso do Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Gerir programas e controlos de revisões de acesso do Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Criar uma revisão de acesso para os membros de um grupo ou o acesso a uma aplicação](active-directory-azure-ad-controls-create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
