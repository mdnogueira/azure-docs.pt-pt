---
title: Licenciar os utilizadores no Azure Active Directory | Microsoft Docs
description: Saiba mais sobre como licenciar si e aos utilizadores no Azure Active Directory.
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: c4509cdb003687083d0456c1957b19cf35ee056a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Início rápido: Licenciar utilizadores no Azure Active Directory
Baseada no licenciamento do Azure AD serviços trabalho por ativar uma subscrição do Azure Active Directory (Azure AD) no seu inquilino do Azure. Depois da subscrição está ativa, capacidades de serviço são geridas por administradores do Azure AD e utilizadas pelos utilizadores licenciados. Ao adquirir o Enterprise Mobility + de segurança, Azure AD Premium ou Basic do Azure AD, o inquilino é atualizado com a subscrição, incluindo o respetivo período de validade e licenças pré-pago. Informações da sua subscrição, incluindo o número de licenças atribuídas ou disponíveis, estão disponíveis através do portal do Azure em **do Azure Active Directory** abrindo o **licenças** mosaico. O **licenças** painel também é o melhor local para gerir a suas atribuições de licenças.

Apesar de obter uma subscrição é tudo o que precisa para configurar funcionalidades pagas, ainda tem de atribuir licenças de utilizador para o Azure AD pago e paga funcionalidades. Qualquer utilizador que devem ter acesso ao ou que é gerido através de um Azure AD paga funcionalidade tem de ser atribuída uma licença. Atribuição de licenças é um mapeamento entre um utilizador e um serviço adquirido, como o Azure AD Premium, Basic ou Enterprise Mobility + Security.

Pode utilizar [atribuição com base no grupo de licenças](active-directory-licensing-whatis-azure-portal.md) para configurar regras como o seguinte:
* Todos os utilizadores no seu diretório obtém automaticamente uma licença
* Todos os utilizadores com o título de trabalho adequada obtém uma licença
* Pode delegar a decisão de outros gestores na organização (utilizando [grupos self-service](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Para ver um debate detalhado da atribuição de licença aos grupos, incluindo avançadas cenários e o Office 365 licenciamento cenários, consulte o artigo [atribuir licenças aos utilizadores através da associação a grupos no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Atribuir licenças aos utilizadores e grupos
Utilizar uma subscrição ativa, deve primeiro atribuir uma licença a próprio e atualize o browser para garantir que todas as funcionalidades esperadas incluídas na sua subscrição. O passo seguinte é atribuir licenças aos utilizadores que necessitam de aceder a paga funcionalidades do Azure AD. É uma forma fácil para atribuir licenças para atribuir licenças para grupos de utilizadores, em vez de para indivíduos. Ao atribuir licenças a um grupo, todos os membros do grupo recebem uma licença. Se os utilizadores são adicionados ou removidos do grupo, a licença adequada é automaticamente atribuída ou removida. 

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar o **localização de utilização** propriedade para o utilizador. Pode definir esta propriedade em **utilizador** &gt; **perfil** &gt; **definições** no portal do Azure. Quando utilizar a atribuição do grupo de licenças, qualquer utilizador cuja localização de utilização não está especificada herda a localização do diretório.

Para atribuir uma licença, em **do Azure Active Directory** &gt; **licenças** &gt; **todos os produtos**, selecione um ou mais produtos e, em seguida, selecione **atribuir** na barra de comandos.

![Selecione uma licença para atribuir](media/license-users-groups/select-license-to-assign.png)

Pode utilizar o **utilizadores e grupos** painel escolher vários utilizadores ou grupos ou desativar os planos de serviço no produto. Utilize a caixa de pesquisa na parte superior para procurar os nomes de utilizador e grupo.

![Selecione um utilizador ou grupo para atribuição de licenças](media/license-users-groups/select-user-for-license-assignment.png)

Ao atribuir licenças a um grupo, pode demorar algum tempo antes de todos os utilizadores herdam a licença, consoante o tamanho do grupo. Pode verificar o estado de processamento no **grupo** painel, sob o **licenças** mosaico.

![Estado de atribuição de licença](media/license-users-groups/license-assignment-status.png)

Erros de atribuição podem ocorrer durante a atribuição de licença do Azure AD, mas são raros relativamente ao gerir do Azure AD e Enterprise Mobility + produtos de segurança. Potenciais erros de atribuição estão limitados a:
- Conflito de atribuição: quando um utilizador anteriormente foi atribuído uma licença que é incompatível com a licença atual. Neste caso, a atribuir a licença nova requer a remoção dos atual.
- Foi excedido licenças disponíveis: quando o número de utilizadores nos grupos atribuídos excede as licenças disponíveis, o estado de atribuição de um utilizador reflete uma falha ao atribuir devido a falta de licenças.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Licenciamento e colaboração do Azure AD B2B

Colaboração B2B permite-lhe convidar utilizadores convidados no inquilino do Azure AD para fornecer acesso aos serviços do Azure AD e quaisquer recursos do Azure lhe disponibiliza.  

Não há sem encargos durante inviting B2B utilizadores e atribuir-lhes a uma aplicação no Azure AD. Até 10 aplicações por utilizador convidado e 3 relatórios básicos também são livres para os utilizadores de colaboração B2B. Se o utilizador convidado tem as licenças adequadas atribuídas no inquilino do Azure AD o parceiro, estes irá licenciadas, bem como na sua.

Não é necessário, mas se pretender fornecer acesso a paga funcionalidades do Azure AD, os utilizadores de convidado B2B têm de estar licenciados com adequado licenças do Azure AD. Um inquilino convidando com um Azure AD, uma licença paga pode atribuir a colaboração B2B direitos de utilizador a um utilizadores cinco convidados adicionais convidado para o inquilino. Para cenários e as informações, consulte [colaboração B2B licenciamento orientações](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Licenças de vista atribuída

Uma vista de resumo de licenças atribuídas e disponíveis é apresentada em **do Azure Active Directory** &gt; **licenças** &gt; **todos os produtos**.

![Licença de vista Resumo](media/license-users-groups/view-license-summary.png)

Uma lista detalhada dos atribuído utilizadores e grupos que está disponível quando selecionar um produto específico. O **utilizadores licenciados** lista mostra todos os utilizadores atualmente consumir uma licença e indica se a licença foi atribuída diretamente para o utilizador ou se tiver sido herdado de um grupo.

![Ver detalhes de licença](media/license-users-groups/view-license-detail.png)

Da mesma forma, o **licenciado grupos** lista mostra todos os grupos aos quais foi atribuídas licenças. Selecione um utilizador ou grupo para abrir o **licenças** painel, que mostra todas as licenças atribuídas para esse objeto.

## <a name="remove-a-license"></a>Remover uma licença

Para remover uma licença, vá para o utilizador ou grupo e abra o **licenças** mosaico. Selecione a licença e clique em **remover**.

![Remover uma licença](media/license-users-groups/remove-license.png)

Não não possível remover o herdadas pelo utilizador de um grupo de licenças diretamente. Em vez disso, remova o utilizador o partir da qual estes são herdar a licença de grupo.


## <a name="next-steps"></a>Passos seguintes
Este guia de introdução, aprendeu como atribuir licenças a utilizadores e grupos no diretório do Azure AD. 

Pode utilizar a hiperligação seguinte para configurar as atribuições de licenças de subscrição no Azure AD a partir do portal do Azure.

> [!div class="nextstepaction"]
> [Atribuir licenças do Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 