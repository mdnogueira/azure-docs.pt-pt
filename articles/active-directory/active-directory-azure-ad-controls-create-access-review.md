---
title: "Criar uma revisão do acesso para os membros de um grupo ou os utilizadores com acesso a uma aplicação com o Azure AD | Microsoft Docs"
description: "Saiba como criar uma revisão do acesso para os membros de um grupo ou os utilizadores com acesso a uma aplicação."
services: active-directory
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
ms.openlocfilehash: ae926e8d3a856c9b488d46b481a1c7b5b581d532
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Criar uma revisão do acesso dos membros do grupo ou de acesso de aplicação com o Azure AD

Atribuições de acesso tornar-se "obsoletas" quando os utilizadores têm acesso não precisam de mais. Para reduzir os riscos associados atribuições de acesso obsoletos, os administradores podem utilizar o Azure Active Directory (Azure AD) para criar uma revisão do acesso para os membros do grupo ou os utilizadores atribuídos a uma aplicação. Para obter mais informações sobre estes cenários, consulte [gerir o acesso de utilizador](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) e [gerir o acesso de convidado](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Criar uma revisão do acesso

1. Como administrador global, vá para o [acesso revê página](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)e selecione **programas**.

2. Seleccione o programa que contém o controlo de revisão de acesso que pretende criar. **Programa predefinido** sempre estiver presente, ou pode criar um programa diferente. Por exemplo, pode optar por ter um programa para cada iniciativa de compatibilidade ou o objetivo de negócio.

3. O programa, selecione **controlos**e, em seguida, selecione **adicionar** para adicionar um controlo.

4. Nome de cada revisão do acesso. Opcionalmente, forneça cada revisão uma descrição. O nome é apresentado para os revisores.

5. Defina as datas de início e de fim. Por predefinição, o acesso reveja inicia o mesmo dia, é criado e termina num mês. Pode alterar o início e fim de datas para ter acesso reveja início no futuro e pela última vez no entanto, o número de dias que pretende.

6. Revisões de acesso podem ser membros de um grupo ou para os utilizadores que foram atribuídos a uma aplicação. Pode ainda mais definir o âmbito de acesso revisão para revisão apenas os utilizadores convidados que são membros (ou atribuídos à aplicação), em vez de rever todos os utilizadores que são membros ou que tenham acesso à aplicação.

7. Selecione um ou mais pessoas para rever todos os utilizadores no âmbito. Ou, pode optar por ter membros rever os suas próprias acesso. Se o recurso é um grupo, pode colocar os proprietários de grupo para rever. Também pode exigir que os revisores de fornecer um motivo quando estes aprovar acesso.

8. Por fim, selecione **iniciar**.


## <a name="manage-the-access-review"></a>Gerir a revisão do acesso

Por predefinição, o Azure AD envia um e-mail a revisores imediatamente após a revisão é iniciado. Se optar por não ter o Azure AD enviar o e-mail, lembre-se de que a informar os revisores uma revisão do acesso está a aguardar a sua conclusão. Pode mostrá-los as instruções sobre como [rever o acesso](active-directory-azure-ad-controls-perform-access-review.md). Se estiver a rever para convidados rever os suas próprias acesso, apresentar as instruções sobre como [rever o suas próprias acesso](active-directory-azure-ad-controls-perform-access-review.md).

Se alguns dos revisores tiverem os convidados, os convidados são notificados por e-mail apenas se tiver aceitaram já os respetivos convite.


Pode monitorizar o progresso conforme os revisores concluir as revisões no dashboard do Azure AD no **acesso revê** secção. Sem direitos de acesso são alterados no diretório até [concluída a revisão](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Passos seguintes

Quando uma revisão do acesso é iniciado, o Azure AD envia automaticamente revisores uma mensagem de e-mail que lhe solicita informações para rever o acesso. Se um utilizador não receber uma mensagem de e-mail, pode enviar-lhes instruções sobre como [rever o acesso](active-directory-azure-ad-controls-perform-access-review.md). 

Após o período de revisão do acesso ou o administrador para a revisão do acesso, siga os passos no [concluir uma revisão do acesso](active-directory-azure-ad-controls-complete-access-review.md) para ver e aplicar os resultados.


