---
title: "Alterar o seu cartão de crédito do Azure | Microsoft Docs"
description: "Descreve como como alterar o cartão de crédito utilizado pagar para uma subscrição do Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 03764377b3ea0e17d4a192a7e05bb495ec56f331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito ou de débito do Azure

No Centro de conta, pode adicionar um novo cartão de crédito, atualizar um cartão de crédito existente ou eliminar um cartão de crédito não utilizar. Tem de ser [administrador da conta](billing-subscription-transfer.md#whoisaa) para efetuar estas alterações.

**Pretende mudar pagar por fatura?** Consulte [pagar para as subscrições do Azure por fatura](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Adicionar um novo cartão de crédito ou de débito

1. Iniciar sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions) como o administrador da conta.
1. Selecione uma subscrição.
1. No lado direito da página, selecione **Gerir métodos de pagamento**.

    ![Captura de ecrã que mostra a opção de métodos de pagamento de gerir selecionada.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Selecione "+" para adicionar um cartão.

    ![Captura de ecrã que mostra a opção de editar junto o método de pagamento.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Introduza o crédito ou débito os detalhes do cartão.
1. Selecione **Guardar**. 

Se obtiver um erro depois de adicionar o cartão de crédito, consulte [recusado de cartão de crédito no Azure inscrição](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Atualizar crédito existente ou de débito

Se o seu cartão de crédito obtém renovado e o número permanece o mesmo, atualize os detalhes de cartão de crédito existente, como a data de expiração. Se as alterações de número de cartão de crédito porque o cartão é perdido, roubados ou expirado, siga os passos a [adicionar um cartão de crédito como um método de pagamento](#addcard) secção. Não precisa de atualizar o CVV.

1. Iniciar sessão para o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como o administrador da conta.
1. Selecione a subscrição que está ligada para o cartão.
1. Selecione **gerir métodos de pagamento**.
1. Selecione **editar** junto a placa de que pretende atualizar.
1. Atualize os detalhes de cartão de crédito ou de débito.
1. Selecione **Guardar**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Utilizar um cartão de crédito diferente para a subscrição do Azure

1. Iniciar sessão para o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como o administrador da conta.
1. Selecione a subscrição que está ligada para o cartão.
1. No lado direito da página, selecione **Gerir métodos de pagamento**.
1. Clique em **utilizar em vez disso,** junto a placa de que pretende utilizar. Isto também atualiza quaisquer outras subscrições atualmente associadas este cartão. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Remover um cartão de crédito ou de débito da conta

1. Iniciar sessão para o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como o administrador da conta.
1. Selecione a subscrição que está ligada para o cartão.
3. No lado direito da página, selecione **Gerir métodos de pagamento**.
4. Clique em **eliminar** para o cartão de crédito que pretende eliminar.

Se o seu cartão de crédito é associado a outras subscrições do Microsoft Active Directory, não é possível removê-lo da sua conta do Azure. Remova o cartão de crédito de todas as subscrições de Active Directory com a Microsoft e tente novamente.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>A minha subscrição está desativada. Por que motivo não é possível remover o meu cartão de crédito agora?

Após a sua subscrição está desativada ou cancelada, aguardaremos 90 dias antes de eliminar permanentemente a sua subscrição. Vamos manter o seu método de pagamento no ficheiro durante o período de retenção caso pretende reativar a subscrição. Depois disso, a subscrição é totalmente eliminada.

Se precisar de remover o seu cartão de crédito ou de débito antes de terminar o período de retenção de 90 dias, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que motivo posso manter obter "a sua sessão de início de sessão expirou. Clique aqui para voltar a iniciar sessão"?

Se manter a obter esta mensagem de erro, mesmo se já tiver terminar sessão e iniciá-la no, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como utilizar um cartão diferente para cada subscrição que tiver?

Infelizmente, se as suas subscrições já estiver a utilizar a mesma placa, não é possível separar-lhe utilizar diferentes cartões. No entanto, quando se inscreve para uma nova subscrição, pode optar por utilizar um novo método de pagamento para essa subscrição.

### <a name="how-do-i-make-payments"></a>Como efetuar pagamentos?

Se configurou um cartão de crédito ou de um débito como método de pagamento, iremos cobram automaticamente seu cartão após cada período de faturação. Não precisa de fazer nada.

Se estiver [pagar por fatura](billing-how-to-pay-by-invoice.md), envie o pagamento para a localização indicado na parte inferior da sua fatura.

### <a name="how-do-i-make-a-one-time-payment"></a>Como efetuar um única pagamento?

Infelizmente, Azure atualmente não suporta uma única pagamentos para cartões de crédito ou de débito. 

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
