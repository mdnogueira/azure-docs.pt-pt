---
title: "Gerir o acesso de convidado com o Azure AD aceder revisões | Microsoft Docs"
description: "Gerir os utilizadores convidados como membros de um grupo ou atribuído a uma aplicação com revisões de acesso do Azure Active Directory"
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
ms.openlocfilehash: d67b12eaac17e278724ddf8670e65afba235f099
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerir o acesso de convidado com o Azure AD revisões de acesso


Com o Azure Active Directory (Azure AD), pode ativar facilmente colaboração entre limites organizacionais através de [funcionalidade do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md). Os utilizadores convidados de outros inquilinos podem ser [convidou por administradores](active-directory-b2b-admin-add-users.md) ou pelo [outros utilizadores](active-directory-b2b-how-it-works.md). Esta capacidade também se aplica a identidades sociais, tais como contas Microsoft.

Também pode facilmente garantir que os utilizadores convidados têm acesso adequado. Pode colocar os convidados próprios ou um decisor participar uma revisão do acesso e recertify (ou atestem) para o acesso dos convidados. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando uma revisão do acesso estiver concluída, em seguida, pode efetuar alterações e remover o acesso para os convidados que já não precisam dele.

> [!NOTE]
> Este documento centra-se na revisão do acesso dos utilizadores convidados. Se pretender rever o acesso de todos os utilizadores, não apenas convidados, consulte [gerir o acesso de utilizador com acesso revisões](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Se pretender rever a associação dos utilizadores funções administrativas, tal como administrador global, consulte [iniciar uma revisão do acesso no Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

Revisões de acesso estão disponíveis com a edição Premium P2 do Azure AD, que está incluído no Microsoft Enterprise Mobility + segurança, E5. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md). É necessária uma licença para cada utilizador que interage com esta funcionalidade para criar uma revisão, aceder a uma revisão ou aplicar uma revisão.

Se planeia peça aos utilizadores de convidado para rever os suas próprias acesso, leia sobre o licenciamento de utilizador convidado. Para obter mais informações, consulte [licenciamento de colaboração B2B do Azure AD](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e executar uma revisão do acesso para os convidados

Em primeiro lugar, ative revisões de acesso a aparecer no painel de acesso de um revisor. Como administrador global, aceda à [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD permite vários cenários para rever os utilizadores convidados.

Selecione um dos seguintes:

 - Um grupo no Azure AD que tenha um ou mais convidados como membros.
 - Uma aplicação ligada para o Azure AD que tenha um ou mais utilizadores convidados atribuídos. 

Em seguida, pode decidir se pretende colocar cada convidado para rever os suas próprias acesso ou para pedir um ou mais utilizadores para rever o acesso de todos os convidados.

 Estes cenários são abordados nas secções seguintes.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Peça convidados para rever as suas próprias associação num grupo

Pode utilizar o acesso revisões para garantir que os utilizadores que foram convidados e adicionados a um grupo continuam a ter acesso. Pode facilmente pedir convidados para rever as seus próprios associação a esse grupo.

1. Para iniciar uma revisão do acesso para o grupo, selecione de revisão para incluir apenas membros de utilizador de convidado e membros reveja próprios. Para obter mais informações, consulte [criar uma revisão do acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Peça cada convidado para rever as suas próprias associação. Por predefinição, cada convidado que aceite um convite recebe uma mensagem de e-mail do Azure AD com uma hiperligação para a revisão do acesso. Azure AD tem instruções para os convidados sobre como [rever o respetivo acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores dar entrada, pare a revisão do acesso e aplicar as alterações. Para obter mais informações, consulte [concluir uma revisão do acesso](active-directory-azure-ad-controls-complete-access-review.md).

4. Para além dos utilizadores que negado as seus próprios necessidade de acesso contínuo, pode também remover utilizadores que não respondem. Resposta de não utilizadores potencialmente já não recebem correio eletrónico.

5. Se o grupo não é utilizado para gestão de acesso, também pode remover utilizadores que não foram selecionados para participar na revisão porque estes não aceitam os respetivos convite. Não aceitar poderá indicar que o endereço de correio eletrónico do utilizador convidados tinha um erro de digitação. Se um grupo é utilizado como uma lista de distribuição, talvez alguns utilizadores convidados não foram selecionados para participar porque estão contidos objetos de contactos.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Peça a um patrocinador para rever a associação do convidado num grupo

Pode colocar um patrocinador, tais como o proprietário de um grupo, para rever a necessidade de um convidado de contínua associação num grupo.

1. Para iniciar uma revisão do acesso para o grupo, selecione de revisão para incluir apenas membros de utilizador de convidado. Em seguida, especifique um ou mais revisores. Para obter mais informações, consulte [criar uma revisão do acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por predefinição, cada recebem uma mensagem de e-mail do Azure AD com uma hiperligação para o painel de acesso, onde são [efetue a respetiva revisão do acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores dar entrada, pare a revisão do acesso e aplicar as alterações. Para obter mais informações, consulte [concluir uma revisão do acesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Peça convidados para rever os suas próprias acesso a uma aplicação

Pode utilizar o acesso revisões para garantir que os utilizadores que foram convidados para uma aplicação específica continuam a ter acesso. Pode facilmente pedir ao próprios convidados para rever as suas próprias necessidade de acesso.

1. Para iniciar uma revisão do acesso da aplicação, selecione de revisão para incluir apenas convidados e que os utilizadores, reveja os suas próprias acesso. Para obter mais informações, consulte [criar uma revisão do acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Peça cada convidado para rever os suas próprias acesso à aplicação. Por predefinição, cada convidado que aceite um convite recebe uma mensagem de e-mail do Azure AD com uma hiperligação para a revisão do acesso no painel de acesso da sua organização. Azure AD tem instruções para os convidados sobre como [rever o respetivo acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores dar entrada, pare a revisão do acesso e aplicar as alterações. Para obter mais informações, consulte [concluir uma revisão do acesso](active-directory-azure-ad-controls-complete-access-review.md).

4. Para além dos utilizadores que negado os seus próprios necessita de acesso contínuo, também pode remover os utilizadores convidados que não respondem. Resposta de não utilizadores potencialmente já não recebem correio eletrónico. Também pode remover os utilizadores convidados que não foram selecionados para participar, especialmente se estes não foram recentemente convidados. Os utilizadores não aceitam que os respetivos convite e por isso não tem acesso à aplicação. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Peça a um patrocinador para rever o acesso do convidado para uma aplicação

Pode colocar um patrocinador, tais como o proprietário de uma aplicação, reveja a necessidade de um convidado de acesso contínuo para a aplicação.

1. Para iniciar uma revisão do acesso da aplicação, selecione de revisão para incluir apenas convidados. Em seguida, especifique um ou mais utilizadores como revisores. Para obter mais informações, consulte [criar uma revisão do acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Peça aos revisores para darem o seu parecer. Por predefinição, cada recebem uma mensagem de e-mail do Azure AD com uma hiperligação para o painel de acesso, onde são [efetue a respetiva revisão do acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores dar entrada, pare a revisão do acesso e aplicar as alterações. Para obter mais informações, consulte [concluir uma revisão do acesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Peça convidados para rever a respetiva necessidade de acesso, em geral

Em algumas organizações, os convidados podem não estar em consideração as respetivas associações a grupos.

> [!NOTE]
> Versões anteriores do portal do Azure não permitem o acesso administrativo pelos utilizadores com o UserType do convidado. Em alguns casos, um administrador no seu diretório poderá foram alterados valor de UserType do convidado para o membro com o PowerShell. Se esta alteração ocorreu anteriormente no seu diretório, a consulta anterior pode não incluir todos os utilizadores de convidado que tinham Historicamente, os direitos de acesso administrativo. Neste caso, terá de alterar UserType o convidado ou incluir manualmente o convidado na associação ao grupo.

1. Crie um grupo de segurança no Azure AD com os convidados como membros, se ainda não existir um grupo adequado. Por exemplo, pode criar um grupo com uma adesão manualmente manter de convidados. Em alternativa, pode criar um grupo dinâmico com um nome, tais como "Convidados da Contoso" para os utilizadores no inquilino Contoso com o valor do atributo UserType do convidado.

2. Para iniciar uma revisão do acesso para esse grupo, selecione de revisores para serem membros próprios. Para obter mais informações, consulte [criar uma revisão do acesso](active-directory-azure-ad-controls-create-access-review.md).

3. Peça cada convidado para rever as suas próprias associação. Por predefinição, cada convidado que aceite um convite recebe uma mensagem de e-mail do Azure AD com uma hiperligação para a revisão do acesso no painel de acesso da sua organização. Azure AD tem instruções para os convidados sobre como [rever o respetivo acesso](active-directory-azure-ad-controls-perform-access-review.md).

4. Depois dos revisores dar entrada, pare a revisão do acesso. Para obter mais informações, consulte [concluir uma revisão do acesso](active-directory-azure-ad-controls-complete-access-review.md).

5. Remova o acesso de convidado para convidados que foram negados, não concluiu a revisão ou anteriormente não aceitam o respetivo convite. Se alguns dos convidados são contactos que foram selecionados para participar na revisão porque estes não aceitam anteriormente um convite, pode desativar as contas utilizando o portal do Azure ou o PowerShell. Se o convidado já não precisa de acesso e não é um contacto, pode remover o respetivo objeto de utilizador do diretório através do portal do Azure ou do PowerShell.

## <a name="next-steps"></a>Passos seguintes

[Criar uma revisão de acesso para os membros de um grupo ou o acesso a uma aplicação](active-directory-azure-ad-controls-create-access-review.md)







