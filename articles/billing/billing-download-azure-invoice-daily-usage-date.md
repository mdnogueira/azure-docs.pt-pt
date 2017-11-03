---
title: "Transferir uma fatura e diariamente dados de utilização de faturação do Azure | Microsoft Docs"
description: "Descreve como transferir ou visualizar o seu Azure faturação uma fatura e diariamente dados de utilização."
keywords: "faturação da fatura, transferência fatura, fatura do azure, a utilização do azure"
services: 
documentationcenter: 
author: genlin
manager: tonguyen
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/26/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eea55735d0e17de4fe543847d0d521b0e8c0c3f7
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Transferir ou visualizar a fatura de faturação do Azure e a utilização de dados diária
Pode transferir a fatura do [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou tiver enviado por correio eletrónico. Para transferir a sua utilização diária, vá para o [Centro de contas do Azure](https://account.azure.com/Subscriptions). Apenas determinadas funções tem permissão para obter a fatura de faturação e informações de utilização, como o administrador da conta. Para obter mais informações sobre como obter acesso a informações de faturação, consulte [gerir o acesso ao Azure através de funções de faturação](billing-manage-access.md).

>[!NOTE]
>Este artigo não se aplica aos clientes do Enterprise Agreement (EA). Se tiver um cliente EA, sua faturas são enviadas diretamente para os administradores de inscrição.

## <a name="get-your-invoice-in-email-pdf"></a>Obter a sua fatura por correio eletrónico (. pdf)
Pode optar ativamente por participar no e configurar os destinatários adicionais para receber o seu Azure fatura num e-mail. Esta funcionalidade pode não estar disponível para determinados subscrições, tais como ofertas de suporte, contratos Enterprise ou do Azure no Open.

1. Selecione a sua subscrição do [página de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Optar ativamente por participar para cada subscrição que é proprietário. Clique em **faturas** , em seguida, **correio eletrónico da minha fatura**. 

    ![Captura de ecrã que mostra o fluxo de recusa](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Clique em **optar ativamente por participar no** e aceite os termos.

    ![Captura de ecrã que mostra o fluxo de recusa](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Assim que tiver aceite o contrato, pode configurar os destinatários adicionais.

    ![Captura de ecrã que mostra o fluxo de recusa](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Se não obtiver uma mensagem de e-mail depois de seguir os passos, certifique-se de que o seu endereço de correio eletrónico está correto no [preferências de comunicação no seu perfil](https://account.windowsazure.com/profile).

## <a name="download-invoice-from-azure-portal-pdf"></a>Transferir a fatura do portal do Azure (. pdf)

1. Selecione a sua subscrição do [página de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure como [um utilizador com acesso ao faturas](billing-manage-access.md).

2. Selecione **faturas**. 

    ![Captura de ecrã que mostra a opção de faturação e de utilização](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Clique em **transferir fatura** para ver uma cópia da sua fatura PDF. Se diz **não está disponível**, consulte [por que motivo não vejo uma fatura para o último período de faturação?](#noinvoice)

    ![Captura de ecrã que mostra períodos de faturação, a opção de transferência e os encargos totais para cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Também pode ver a utilização diária clicando o período de faturação. 

Para obter mais informações sobre a sua fatura, consulte [compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para ajudar a gerir os custos, consulte [evitar custos inesperados com faturação do Azure e custos de gestão](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Transferir a utilização do Centro de contas (. csv)

1. Inicie sessão no [Centro de contas do Azure](https://account.windowsazure.com/subscriptions) como o administrador da conta.

2. Selecione a subscrição para o qual pretende que as informações da fatura e utilização.

3. Selecione **histórico de FATURAÇÃO**. 

    ![Captura de ecrã que mostra a opção de histórico de faturação](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Pode ver as instruções para os últimos seis períodos de faturação e o período de unbilled atual. 

    ![Captura de ecrã que mostra períodos de faturação, as opções a transferir uma fatura e utilização diária e os encargos totais para cada período de faturação](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **ver declaração atual** para ver uma estimativa do seu encargos no momento a estimativa foi gerada. Estas informações só são atualizadas diariamente e não podem incluir todos os seus utilização. A fatura mensal pode ser diferentes desta estimativa.

    ![Captura de ecrã que mostra a opção da instrução atual da vista](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de ecrã que mostra a estimativa de encargos atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **transferir utilização** para transferir os dados de utilização diária como um ficheiro CSV. Se vir duas versões disponíveis, transfira a versão 2.

    ![Captura de ecrã que mostra a opção de transferir utilização](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Apenas o administrador da conta podem aceder Centro de contas do Azure. Outros administradores de faturação como, por exemplo, um proprietário, podem obter a utilização de informações de utilização a [APIs faturação](billing-usage-rate-card-overview.md).

Para obter mais informações sobre a utilização diária, consulte [compreender a fatura do Microsoft Azure](billing-understand-your-bill.md). Para ajudar a gerir os custos, consulte [evitar custos inesperados com faturação do Azure e custos de gestão](billing-getting-started.md).

## <a name="noinvoice"></a>Por que motivo não vejo uma fatura para o último período de faturação?

Podem existir várias razões, se não vir uma fatura:

- Tem um montante mensal de crédito com a sua subscrição que não exceda ou tem uma versão de avaliação gratuita. Uma fatura só é gerada quando honorários dinheiro.

- É menos de 30 dias a contar do dia que subscrito no Azure.

- Não é gerada a fatura. Aguarde até ao fim do período de faturação.

- Se não for administrador de conta, faturas mais antigas poderão não estar disponíveis para si.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda tiver mais perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.

