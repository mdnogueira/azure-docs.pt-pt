---
title: Inscrever-se no Azure com a conta do Office 365 | Microsoft Docs
description: "Saiba como criar uma subscrição do Azure, utilizando uma conta do Office 365"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: c81d7fa793388612ec4d76d79a2f30f209b9cf42
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Inscreva-se uma subscrição do Azure com a sua conta Office 365
Se tiver uma subscrição do Office 365, pode utilizar a sua conta Office 365 para criar uma subscrição do Azure. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) utilizando o seu nome de utilizador do Office 365 e a palavra-passe. Se pretender configurar máquinas virtuais ou utilizar outros serviços do Azure, deve inscrever-se para uma subscrição do Azure. Pode partilhar a sua subscrição do Azure com outras pessoas e [utilizar o controlo de acesso baseado em funções para gerir o acesso à sua subscrição do Azure e recursos](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)

Se já tiver uma conta do Office 365 e uma subscrição do Azure, consulte o artigo [associar um inquilino do Office 365 para uma subscrição do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obter uma subscrição do Azure utilizando a sua conta Office 365

Poupar tempo e evitar a proliferação de conta ao inscrever-se no Azure utilizando o seu nome de utilizador do Office 365 e a palavra-passe. 

1. Inscrever-se no [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Inicie sessão com o nome de utilizador do Office 365 e a palavra-passe. A conta utilizada não tem permissões de administrador. Se tiver mais do que uma conta do Office 365, certifique-se de que utiliza as credenciais da conta do Office 365 que pretende associar a sua subscrição do Azure. 

   ![Captura de ecrã que mostra a página de início de sessão.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Introduza as informações necessárias e concluir o processo de inscrição. Algumas informações podem não ser necessárias se já tiver uma conta do Office 365.

    ![Captura de ecrã que mostra o formulário de inscrição.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se precisar de adicionar outras pessoas na sua organização para a subscrição do Azure, consulte [introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md). 

## <a id="more-about-subs">Mais informações sobre as subscrições do Azure e o Office 365</a>
Office 365 e o Azure utilizam o serviço do Azure AD para gerir utilizadores e as subscrições. O diretório do Azure é como um contentor no qual pode agrupar os utilizadores e as subscrições. Para utilizar as mesmas contas de utilizador para as suas subscrições do Azure e o Office 365, tem de certificar-se de que as subscrições do Azure são criadas no mesmo diretório que as subscrições do Office 365. Tenha em consideração os seguintes pontos:

* É criada uma subscrição de um diretório
* Os utilizadores pertencem a diretórios
* Uma subscrição seja colocado no sítio o diretório do utilizador que cria a subscrição. Por isso, a subscrição do Office 365 está associada para a mesma conta que a sua subscrição do Azure.
* As subscrições do Azure são pertencentes a utilizadores individuais no diretório
* Subscrições do Office 365 são proprietário do diretório si próprio. Os utilizadores com as permissões corretas no diretório podem gerir estas subscrições.

![Captura de ecrã que mostra a relação do diretório, os utilizadores e subscrições.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para obter mais informações, consulte [subscrições do Azure como estão associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente. 
