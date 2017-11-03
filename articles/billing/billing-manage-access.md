---
title: "Gerir o acesso ao Azure através de funções de faturação | Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Gerir o acesso a informações de faturação do Azure utilizando o controlo de acesso baseado em funções

Pode conceder acesso para as informações de faturação do Azure para os membros da sua equipa atribuindo uma das seguintes funções de utilizador à sua subscrição: conta de administrador, administrador de serviço, coadministrador, proprietário, Contribuidor, leitor e faturação leitor. Seria têm acesso a informações de faturação no [portal do Azure](https://portal.azure.com/), e podem utilizar o [APIs faturação](billing-usage-rate-card-overview.md) programaticamente obter faturas (uma vez optado por participar) e detalhes de utilização. Para obter mais informações sobre quem pode conceder funções e que funções podem fazer com que, consulte [funções no Azure RBAC](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a>Permitir que os utilizadores adicionais acesso faturas

O administrador da conta tem ativamente por participar no utilizando o [portal do Azure](https://portal.azure.com/) permitir o acesso ao faturas para outros utilizadores e através de API.

1. Como o administrador da conta, selecione a sua subscrição do [painel subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

1. Selecione **faturas** e, em seguida, **acesso ao faturas**.

    ![Captura de ecrã mostra como delegar o acesso às faturas](./media/billing-manage-access/AA-optin.png)

1. Ativar **no** funções para transferir a fatura no âmbito de acesso seguido de guardar as alterações, para permitir que os utilizadores na subscrição.

    ![Captura de ecrã mostra no convocaremos para delegar o acesso a fatura](./media/billing-manage-access/AA-optinAllow.png)

Aceitar permite que o administrador de serviço, coadministrador, proprietário, Contribuidor, leitor e faturação leitor na subscrição para transferir faturas PDF no portal do Azure. No entanto, faturas anteriores Dezembro de 2016 estão disponíveis apenas para o administrador da conta por agora.

O administrador da conta também pode configurar ter faturas enviadas por e-mail. Para obter mais informações, consulte [obter da sua fatura no e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Adicionar utilizadores à função de leitor de faturação

A função de leitor de Faturação tem acesso só de leitura às informações de faturação da subscrição no portal do Azure e sem acesso a serviços como VMs e as contas de armazenamento. Atribua a função de leitor de faturação a alguém que precisa de aceder as informações de faturação da subscrição, mas não a capacidade de gerir os serviços do Azure. Esta função é adequada para os utilizadores numa organização que efetuar apenas financeiro e de custo de gestão de subscrições do Azure.

1. Selecione a sua subscrição do [painel subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

1. Selecione **(IAM) do controlo de acesso** e, em seguida, clique em **adicionar**.

    ![Captura de ecrã mostra IAM no painel de subscrição](./media/billing-manage-access/select-iam.PNG)

1. Escolha **faturação leitor** no **selecionar uma função** página.

    ![Captura de ecrã mostra o leitor de faturação na vista de pop-up](./media/billing-manage-access/select-roles.PNG)

1. Escreva o e-mail para o utilizador pretende convidar, em seguida, clique em **OK** para enviar o convite.

    ![Captura de ecrã que mostra a introdução de e-mail para convidar alguém](./media/billing-manage-access/add-user.PNG)

1. Siga as instruções no e-mail de convite para iniciar sessão como um leitor de faturação.

    ![Captura de ecrã que mostra que o leitor de faturação podem ver no portal do Azure](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> A funcionalidade de leitor de faturação está em pré-visualização e ainda não suporta subscrições de empresa (EA) ou de nuvens não global.

## <a name="adding-users-to-other-roles"></a>Adicionar utilizadores a outras funções

Os utilizadores nas outras funções, tais como proprietário ou contribuinte, podem aceder a informações de faturação não apenas, mas, bem como os serviços do Azure. Para gerir estas funções, consulte [adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou serviços](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Quem pode aceder a [Centro de contas](https://account.windowsazure.com)?

Apenas o administrador da conta pode iniciar sessão para o Centro de contas. O administrador da conta é o proprietário legal da subscrição. Por predefinição, a pessoa que inscreveu no ou comprou a subscrição do Azure é o administrador da conta, a menos que o [propriedade de subscrição foi transferida](billing-subscription-transfer.md) para outra pessoa. O administrador da conta pode criar subscrições, cancelar subscrições, alterar o endereço para faturação para uma subscrição e gerir políticas de acesso para a subscrição.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda tiver mais perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
