---
title: "Gerir o acesso de utilizador com as revisões de acesso do Azure AD | Microsoft Docs"
description: "Saiba como gerir o acesso dos utilizadores, como a associação a um grupo ou a atribuição a uma aplicação, com as revisões de acesso do Azure Active Directory"
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
ms.openlocfilehash: c4ea5d797613b214be569c6405a0fc672cae83a0
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gerir o acesso de utilizador com as revisões de acesso do Azure AD

Com o Azure Active Directory (Azure AD), pode facilmente garantir que os utilizadores têm acesso adequado. Pode pedir aos utilizadores ou a um decisor para participar na revisão de acesso e voltar a certificar (ou confirmar) o acesso dos utilizadores. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando a revisão de acesso estiver concluída, pode introduzir as alterações necessárias e remover o acesso dos utilizadores que já não precisam dele.

> [!NOTE]
> Se quiser rever apenas o acesso dos utilizadores convidados, sem abranger a revisão de acesso de todos os tipos de utilizador, veja [Gerir o acesso de utilizadores convidados com revisões de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). Se quiser rever a associação do utilizador a funções administrativas como a de administrador global, veja [Iniciar uma revisão de acesso no Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

As revisões de acesso estão disponíveis com a edição Premium P2 do Azure AD, que está incluída no Microsoft Enterprise Mobility + Security, E5. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md). Cada utilizador que interage com esta funcionalidade para criar uma revisão, aceder a uma revisão ou aplicar uma revisão precisa de uma licença.


## <a name="create-and-perform-an-access-review"></a>Criar e realizar uma revisão de acesso

Uma revisão de acesso pode ter um ou mais utilizadores como revisores.  

1. Selecione um grupo no Azure AD que tenha um ou mais membros. Ou selecione uma aplicação ligada ao Azure AD que tenha um ou mais utilizadores atribuídos à mesma. 

2. Decida se quer que cada utilizador faça a revisão do seu próprio acesso ou se quer ter um ou mais utilizadores incumbidos de rever o acesso de todos os utilizadores.

3. Ative as revisões de acesso de modo a serem apresentadas nos painéis de acesso do revisor. Como administrador global, aceda à [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Inicie a revisão de acesso. Para obter mais informações, veja [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

5. Peça aos revisores para darem o seu parecer. Por predefinição, cada um deles recebe um e-mail do Azure AD com uma ligação para o painel de acesso, onde [realizam a revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).

6. Se os revisores não tiverem dado o seu parecer, pode pedir ao Azure AD para lhes enviar um lembrete. Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.

7. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, veja [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Passos seguintes

[Criar uma revisão de acesso para os membros de um grupo ou o acesso a uma aplicação](active-directory-azure-ad-controls-create-access-review.md)




