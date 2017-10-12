---
title: "Gerir o acesso de utilizador com as revisões de acesso do Azure AD | Microsoft Docs"
description: "Saiba como gerir o acesso de utilizador, como a associação a um grupo ou a atribuição a uma aplicação, com as revisões de acesso do Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gerir o acesso de utilizador com as revisões de acesso do Azure AD

Com o Azure Active Directory, é fácil garantir que os utilizadores têm o acesso adequado, basta pedir aos próprios utilizadores ou a um decisor que participe numa revisão de acesso e volte a certificar (ou "confirme") o acesso do utilizador.  Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Uma vez concluída uma revisão de acesso, pode introduzir as alterações necessárias e remover o acesso dos utilizadores que já não precisam dele.

> [!NOTE]
> Se quiser rever apenas o acesso dos utilizadores convidados, sem abranger a revisão de acesso de todos os tipos de utilizador, consulte [gerir o acesso de utilizadores convidados com revisões de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).  Além disso, se quiser rever a associação do utilizador a funções administrativas como a de Administrador Global, consulte [Como iniciar uma revisão de acesso no Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

As revisões de acesso estão disponíveis com a edição Premium P2 do Azure Active Directory, incluída no EMS E5. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).  Cada utilizador que interage com esta funcionalidade, para criar uma revisão, rever o acesso ou aplicar uma revisão, precisa de uma licença.


## <a name="creating-and-performing-an-access-review"></a>Criar e realizar uma revisão de acesso

Uma revisão de acesso pode ter um ou mais utilizadores como revisores.  

1. Selecione um grupo no Azure Active Directory que tenha um ou mais membros, ou uma aplicação ligada ao Azure Active Directory com um ou mais utilizadores atribuídos a ela. 
2. Decida se quer que cada utilizador faça a revisão do seu próprio acesso ou se quer um ou mais utilizadores incumbidos de rever o acesso de todos os utilizadores.
3. Ative as revisões de acesso de modo a serem apresentadas nos painéis de acesso do revisor.  Como administrador global, aceda à [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Inicie a revisão de acesso. Leia mais sobre [como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
5. Peça aos revisores para darem o seu parecer. Por predefinição, cada um deles irá receber uma mensagem de e-mail do Azure AD com uma ligação para o painel de acesso, onde irão [realizar a revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).
6. Se os revisores não tiverem dado o seu parecer, pode pedir ao Azure AD para lhes enviar um lembrete.  Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.
7. Depois de receber o parecer dos revisores, pare a revisão de acesso e aplique as alterações. Leia mais sobre [como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso para os membros de um grupo ou o acesso a uma aplicação](active-directory-azure-ad-controls-create-access-review.md)




