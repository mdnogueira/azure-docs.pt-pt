---
title: "Como iniciar uma revisão do acesso | Microsoft Docs"
description: "Saiba como criar uma revisão do acesso para identidades privilegiadas com a aplicação Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 3e52b731-55f4-4c8a-ba87-9fd34033f52f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2b516e2f05aa883c5e37f5864e5ee8a2b37d3a46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Como iniciar uma revisão do acesso no Azure AD Privileged Identity Management
Atribuições de função ficam "obsoletas" quando os utilizadores com acesso privilegiado que não precisam de já. Para reduzir os riscos associados estes atribuições de funções obsoletos, os administradores de com função privilegiada regularmente devem rever as funções que atribuiu utilizadores. Este documento aborda os passos para iniciar uma revisão do acesso no Azure AD Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Iniciar uma revisão do acesso
> [!NOTE]
> Se ainda não adicionou a aplicação do PIM ao dashboard no portal do Azure, consulte os passos em [introdução ao Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)
> 
> 

Na página principal da aplicação do PIM, existem três formas de iniciar uma revisão do acesso:

* **Aceder revisões** > **adicionar**
* **Funções** > **revisão** botão
* Selecione a função específica a rever a lista de funções > **revisão** botão

Quando clica no **rever** botão, o **iniciar uma revisão do acesso** é apresentado o painel. Neste painel, vai configurar a revisão com um nome e o limite de tempo, escolha uma função para rever e decidir que executará a revisão.

![Iniciar uma revisão do acesso - captura de ecrã][1]

### <a name="configure-the-review"></a>Configurar a revisão
Para criar uma revisão do acesso, terá de nome e defina uma data de início e de fim.

![Configurar revisão - captura de ecrã][2]

Se o comprimento da revisão tempo suficiente para que os utilizadores concluí-la. Se concluir antes da data de fim, pode sempre parar a revisão antecipadamente.

### <a name="choose-a-role-to-review"></a>Escolher uma função para rever
Cada revisão centra-se apenas uma função. Exceto se tiver iniciado a revisão do acesso a partir do painel de uma função específica, terá de escolher uma função agora.

1. Navegue para **rever membro da função**
   
    ![Reveja o membro da função - captura de ecrã][3]
2. Selecione uma função na lista.

### <a name="decide-who-will-perform-the-review"></a>Decidir que executará a revisão
Existem três opções para executar uma revisão. Pode atribuir a revisão para outra pessoa para concluir, pode fazê-lo por si ou pode fazer com que cada utilizador, reveja os suas próprias acesso.

1. Navegue para **selecione revisores**
   
    ![Selecione os revisores - captura de ecrã][4]
2. Escolha uma das opções:
   
   * **Selecione revisor**: Utilize esta opção se não souber quem tem acesso. Com esta opção, pode atribuir a revisão para um proprietário de recursos ou o Gestor de grupo para concluir.
   * **Enviar-me**: útil se pretender pré-visualizar como acesso revê o trabalho ou pretende rever em nome de pessoas que não é possível.
   * **Membros reveja próprios**: Utilize esta opção para que os utilizadores, reveja as suas próprias atribuições de funções.

### <a name="start-the-review"></a>Iniciar a revisão
Por fim, tem a opção para exigir que os utilizadores forneçam um motivo se estes aprovar o acesso. Adicione uma descrição da revisão se assim o desejar e selecione **iniciar**.

Certifique-se de que permite aos utilizadores saber que existe uma revisão do acesso a aguardar para os mesmos e apresentar [como executar uma revisão do acesso](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerir a revisão do acesso
Pode monitorizar o progresso conforme os revisores concluir as revisões no dashboard do Azure AD PIM, na secção de revisões de acesso. Sem direitos de acesso serão alterados no diretório até [conclui a revisão](active-directory-privileged-identity-management-how-to-complete-review.md).

Até que o período de avaliação está acima, pode notificar os utilizadores para concluir a respetiva revisão ou pare a revisão numa fase inicial da secção de revisões de acesso.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>Tabela PIM de conteúdo
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
