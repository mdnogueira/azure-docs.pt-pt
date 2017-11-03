---
title: "Compreender os encargos de serviço externos do Azure | Microsoft Docs"
description: "Saiba mais sobre faturação de serviços externos, anteriormente conhecido como Marketplace, os encargos no Azure."
services: 
documentationcenter: 
author: adpick
manager: tonguyen
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64bfd8581141001aa6c11ca17ec1af681054f490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Compreender a faturação do Azure para externos encargos de serviço
Serviços externos são publicados por fornecedores de software de terceiros no Azure marketplace. Por exemplo, ClearDB e SendGrid são serviços externos que pode comprar no Azure, mas não são publicadas pela Microsoft.

Quando aprovisionar um novo serviço externo ou um recurso, é apresentado um aviso:

![Aviso de compra do Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Serviços externos estão publicados por empresas não Microsoft, mas por vezes, os produtos da Microsoft também são categorizados como serviços externos.
> 
> 

## <a name="how-external-services-are-billed"></a>Como são faturados serviços externos
- Serviços externos são cobrados separadamente. São tratadas como as ordens individuais dentro da sua subscrição do Azure. O período de faturação para cada serviço é definido ao adquirir o serviço. Não deve ser confundido com o período de faturação da subscrição na qual a comprou. Também receber faturas separadas e o seu cartão de crédito é cobrado separadamente.
- Cada serviço externo tem um modelo de faturação diferente. Alguns serviços são faturados de forma pay as you go, enquanto outras pessoas utilizam um modelo de pagamento com base mensal. Precisa de um cartão de crédito para serviços externos do Azure, não é possível comprar serviços externos com o pagamento da fatura.
- Não é possível utilizar os créditos mensais gratuitos para serviços externos. Se estiver a utilizar uma subscrição do Azure que inclua [livre créditos](https://azure.microsoft.com/pricing/spending-limits/), não é possível aplicar a faturas serviço externo. Utilize um cartão de crédito para comprar serviços externos.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Ver os gastos com serviço externo e o histórico no portal do Azure
Pode ver uma lista dos serviços externos que estão em cada subscrição dentro de [portal do Azure](https://portal.azure.com/): 

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/) como o administrador da conta.
2. No Hub menu, selecione **subscrições**.
   
    ![Selecione as subscrições no Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. No **subscrições** painel, selecione a subscrição que pretende visualizar e, em seguida, selecione **serviços externos**.
   
    ![Selecionar uma subscrição no painel de faturação](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Deverá ver cada uma das ordens de serviço externo, o nome do publicador, comprou de camada de serviço, um nome que deu o recurso e o estado atual da ordem. Para ver passado faturas, selecione um serviço externo.
   
    ![Selecione um serviço externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Aqui, pode ver passado quantidades fatura, incluindo a repartição dedução dos impostos.
   
    ![Vista de serviços externos histórico de faturação](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Vista de serviço externo gastos para clientes do Enterprise Agreement (EA)
Os clientes EA podem ver a dedicar muito do seu serviço externo e transferir relatórios no portal de EA. Consulte [Azure Marketplace para clientes de EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar a utilizar.

## <a name="manage-payment-methods-for-external-service-orders"></a>Gerir os métodos de pagamento para as ordens de serviço externo
Atualizar os métodos de pagamento de encomendas de serviço externo a partir de [Centro de contas](https://account.windowsazure.com/).

> [!NOTE]
> Se comprou a sua subscrição com uma conta escolar ou profissional, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para efetuar alterações ao seu método de pagamento.
> 
> 

1. Iniciar sessão para o [Centro de contas](https://account.windowsazure.com/) e [navegue para o **marketplace** separador](https://account.windowsazure.com/Store)
   
    ![Selecione o marketplace no Centro de contas do](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Selecione o serviço externo que pretende gerir
   
    ![Selecione o serviço externo que pretende gerir](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Clique em **alterar método de pagamento** no lado direito da página. Esta ligação proporciona um portal diferente para gerir o seu método de pagamento.
   
    ![Resumo de ordem](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Clique em **editar informações** e siga as instruções para atualizar as suas informações de pagamento.
   
    ![Selecione editar informações](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Cancelar um pedido de serviço externo
Se pretender cancelar a sua encomenda serviço externo, eliminar o recurso a [portal do Azure](https://portal.azure.com).

![Eliminar recurso](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda tiver questões, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.

