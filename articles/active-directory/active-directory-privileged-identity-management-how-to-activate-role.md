---
title: "Como ativar ou desativar uma função | Microsoft Docs"
description: "Aprenda a ativar as funções de identidades privilegiadas com a aplicação Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a143fd78eae52fda0cbadb7e74fd8209f24629a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Como ativar ou desativar as funções no Azure AD Privileged Identity Management
O Azure Active Directory (AD) Privileged Identity Management simplifica como às empresas gerem o acesso privilegiado para recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se tiver sido efetuado elegível para uma função administrativa, o que significa que pode ativar essa função quando é necessário efetuar ações privilegiadas. Por exemplo, se gerir ocasionalmente funcionalidades do Office 365, os administradores de com função privilegiada da sua organização poderão não fazer com que um Administrador Global permanentes, uma vez que essa função tem impacto nos outros serviços, demasiado. Em vez disso, podem fazer com que elegível para funções do Azure AD, como o administrador do Exchange Online. Pode pedir para ativar essa função quando tem os privilégios e, em seguida, terá de controlo de administração para um período de tempo predefinido.

Este artigo destina-se a administradores que precisam de ativar a respetiva função no Azure AD Privileged Identity Management (PIM). -Explica os passos para ativar uma função quando necessita das permissões e desativar a função quando tiver terminado. Além disso, os administradores de com função privilegiada podem exigir a aprovação para ativar uma função (pré-visualização). Saiba mais sobre [fluxos de trabalho de aprovação de PIM](./privileged-identity-management/azure-ad-pim-approval-workflow.md) aqui.

## <a name="add-the-privileged-identity-management-application"></a>Adicionar a aplicação Privileged Identity Management
Utilizar a aplicação Azure AD Privileged Identity Management no [portal do Azure](https://portal.azure.com/) para pedir a ativação de uma função, mesmo que vai operar noutro portal ou PowerShell. Se não tiver a aplicação Azure AD Privileged Identity Management no portal do Azure, siga estes passos para começar a utilizar.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione o seu nome de utilizador no canto superior direito do portal do Azure e selecione o diretório onde irá a estar a funcionar.
3. Selecione **Mais serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação Privileged Identity Management abre.

## <a name="activate-a-role"></a>Ativar uma função
Quando precisar de desempenhar uma função, pode pedir a ativação, selecionando o **funções My** opção de navegação na aplicação Azure AD Privileged Identity Management da esquerda a coluna de navegação.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) e selecione o mosaico do Azure AD Privileged Identity Management.
2. Selecione **as minhas funções**. Uma lista das suas funções elegíveis atribuídas são apresentados no agrupamento na parte superior da página.
3. Selecione uma função que pretende ativar.
4. Selecione **ativar**. O **pedir a ativação de função** é apresentado o painel.
5. Algumas funções requerem multi-factor Authentication (MFA) antes de pode ativar a função. Só tem de autenticar uma vez por sessão.
   
    ![Certifique-se com a MFA antes da ativação da função - captura de ecrã][2]
6. Introduza a razão para o pedido de ativação no campo de texto.  Algumas funções requerem que forneça um número de permissão de problemas.
7. Selecione **OK**.  Se a função não necessita de aprovação, está agora ativado e a função é apresentada na lista de funções do Active Directory (diretamente abaixo da lista de atribuições de função elegíveis). Se o [função requer aprovação](./privileged-identity-management/azure-ad-pim-approval-workflow.md) para ativar, uma notificação de alerta brevemente aparecerá no canto superior direito do seu browser informando-o pedido está a aguardar aprovação.

    ![Pedidos pendentes notificação - captura de ecrã][3]

## <a name="deactivate-a-role"></a>Desativar uma função
Depois de uma função tiver sido ativada, automaticamente desativa quando for atingido o limite de tempo (duração elegível).

Se concluir as tarefas administrativas numa fase inicial, também pode desativar uma função manualmente na aplicação Azure AD Privileged Identity Management.  Selecione **funções My**, escolha a função estiver pronto através do **atribuições de funções do Active Directory** agrupamento e selecione **desativar**.  

## <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente
No caso de não necessitar de ativação de uma função que requeira a aprovação, pode cancelar um pedido pendente em qualquer altura. Selecione simplesmente o **funções My** opção de navegação na aplicação Azure AD Privileged Identity Management da esquerda a coluna de navegação.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) e selecione o mosaico do Azure AD Privileged Identity Management.
2. Selecione **as minhas funções**. Uma lista das suas funções elegíveis atribuídas são apresentados no agrupamento na parte superior da página.
3. Selecione uma função.
4. Selecione o **ativação está com aprovação pendente** faixa no painel de detalhes de ativação de função.
5. Selecione **Cancelar** na parte superior do **com aprovação pendente** painel.

   ![Cancelar a captura de ecrã do pedido pendente][4]

## <a name="next-steps"></a>Passos seguintes
Se estiver interessado em aprender mais sobre o Azure AD Privileged Identity Management, as hiperligações seguintes tem mais informações.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
