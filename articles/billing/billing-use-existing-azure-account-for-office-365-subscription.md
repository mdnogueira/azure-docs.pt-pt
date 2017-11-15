---
title: Inscrever-se para o Office 365 com a conta do Azure | Microsoft Docs
description: "Saiba como criar uma subscrição do Office 365, utilizando uma conta do Azure"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: cd6103924571ea6fee0cb1e696884a6106082d7a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Inscreva-se uma subscrição do Office 365 com a sua conta do Azure
Se tiver de subscritor do Azure, pode utilizar a sua conta do Azure para se inscrever para uma subscrição do Office 365. Se a parte de uma organização que tenha uma subscrição do Azure, pode criar subscrições do Office 365 para os utilizadores no seu existente do Azure Active Directory (Azure AD). Inscrever-se ao Office 365, utilizando uma conta que tenha permissões de Administrador Global ou de administrador de faturação no seu inquilino do Azure Active Directory. Para obter mais informações, consulte [verificar as minhas permissões de conta no Azure AD](#RoleInAzureAD) e [atribuir funções de administrador no Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

Se já tiver uma conta do Office 365 e uma subscrição do Azure, pode [associar um inquilino do Office 365 para uma subscrição do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Obtenha uma subscrição do Office 365, utilizando a sua conta do Azure

1. Vá para o [página de produto do Office 365](https://products.office.com/business)e selecione um plano.
2. Clique em **sessão** no canto superior direito da página.

    ![captura de ecrã da página de avaliação do Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Inicie sessão com as credenciais da conta do Azure. Se estiver a criar uma subscrição para a sua organização, utilize uma conta do Azure que seja membro da função de diretório de Administrador Global ou de administrador de faturação no seu inquilino do Azure Active Directory.

    ![Captura de ecrã do Office 365 início de sessão](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Clique em **Experimente agora**.

    ![Captura de ecrã que confirma a encomenda para o Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na página de recibo de ordem, clique em **continuar**.

    ![Captura de ecrã de recibo de ordem do Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Agora está tudo pronto. Se tiver criado a subscrição do Office 365 para a sua organização, utilize os seguintes passos para verificar que os utilizadores do Azure AD estão agora no Office 365.

1. Abra o Centro de administração do Office 365.
2. Expanda **utilizadores**e, em seguida, clique em **utilizadores ativos**.

    ![Captura de ecrã dos utilizadores do Centro de administração do Office 365](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

Depois de se inscrever, é adicionada a subscrição do Office 365 para a mesma instância do Azure Active Directory que pertence a subscrição do Azure. Para obter mais informações, consulte [mais sobre as subscrições do Azure e o Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) e [subscrições do Azure como estão associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Verifique as minhas permissões de conta no Azure AD
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **mais serviços**e, em seguida, procure **do Active Directory**.

    ![Captura de ecrã do Active Directory no portal do Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Clique em **utilizadores e grupos** > **todos os utilizadores**.
4. Selecione o nome de utilizador. 

    ![Captura de ecrã que mostra os utilizadores do Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Clique em **função de diretório**.
  
    ![Captura de ecrã que mostra a função de diretório de portal do Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  A função **Administrador Global** ou **administrador limitado** > **administrador de faturação** é necessário para criar uma subscrição do Office 365 para os utilizadores o existente do Azure Active Directory.

    ![Captura de ecrã que mostra o administrador de faturação de função de diretório de portal do Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente. 
