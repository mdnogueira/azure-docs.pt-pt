---
title: "Configurar alertas de crédito ou de faturação para as subscrições do Azure | Microsoft Docs"
description: "Descreve como pode configurar alertas na fatura do Azure, para evitar a faturação surpresas."
keywords: "alerta de crédito, alerta de faturação"
services: 
documentationcenter: 
author: vikdesai
manager: tonguyen
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc0cb2b036e835450ee0fc404cce12439fabc77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Configurar alertas de crédito ou de faturação para as subscrições do Microsoft Azure
Se tiver o administrador de conta para uma subscrição do Azure, pode utilizar o serviço do Azure de faturação alerta criar personalizado faturação alertas que o ajudam a monitorizar e gerir atividade faturação para as contas do Azure.

Este serviço está em pré-visualização, por isso terá de ativar primeiro na página de funcionalidades de pré-visualização.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Definir os destinatários de e-mail e de limiar de alerta
1. Visite [a página de funcionalidades de pré-visualização](https://account.windowsazure.com/PreviewFeatures) e ativar **faturação alerta serviço**.

1. Depois de receber a confirmação de e-mail que o serviço de faturação é ativado para a sua subscrição, visite [página de subscrições](https://account.windowsazure.com/Subscriptions) no portal de contas. Clique na subscrição que pretende monitorizar e, em seguida, clique em **alertas**.

    ![Captura de ecrã da vista de subscrições do Centro de contas do Azure, com alertas realçado][Image1]

2. Em seguida, clique em **Adicionar alerta** para criar o seu primeiro um. Pode configurar um total de cinco alertas faturação por subscrição, com um limiar diferentes e até duas destinatários de e-mail para cada alerta.

    ![Captura de ecrã da vista de alertas, onde pode adicionar o alerta][Image2]

3. Quando adiciona um alerta, atribua um nome exclusivo, escolha um limite de gastos e, escolha os endereços de e-mail, onde são enviados os alertas. Quando configurar o limiar, pode escolher uma um **faturação Total** ou um **crédito monetário** do **alerta para** lista. Para um total de faturação, é enviado um alerta quando gastos da subscrição excede o limiar. Para um crédito monetário, é enviado um alerta quando créditos monetários drop abaixo do limite. Créditos monetários aplicam-se normalmente às subscrições de avaliação gratuita e o Visual Studio.

    ![Captura de ecrã da vista de alerta de adição, onde pode configurar os destinatários][Image3]

Azure suporta qualquer endereço de e-mail, mas não Certifique-se de que funciona de endereço de e-mail, verifique para erros de digitação.

## <a name="check-on-your-alerts"></a>Verificar os alertas
Depois de configurar alertas, o Centro de contas do apresenta-os e mostra quantos mais que pode configurar. Para cada alerta, ver a data e hora que foi enviada, se se trata de um alerta de faturação Total ou crédito monetário e o limite que configura. O formato de data e hora é de 24 horas Hora Universal Coordenada (UTC) e a data é o formato aaaa-mm-dd. Clique no sinal de um alerta na lista para editar ou clique em lixo-pode eliminá-la.

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Alertas de faturação para clientes do Enterprise Agreement (EA)
Os clientes EA podem obter alertas para cada departamento sob uma inscrição por definição gastos quotas. Consulte [departamento gastos Quotas](https://ea.azure.com/helpdocs/departmentSpendingQuotas) no portal do EA para começar a utilizar.

## <a name="learn-more-about-azure-cost-management"></a>Saiba mais sobre a gestão de custo do Azure
- Os custos de estimativa utilizando o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/), [custo total da Calculadora de propriedade](https://aka.ms/azure-tco-calculator), e ao adicionar um serviço.
- [Reveja a utilização e os custos regularmente no portal do Azure](billing-getting-started.md#costs).
- Ativar [Azure Advisor custo recomendações](../advisor/advisor-cost-recommendations.md).

Para obter mais informações, consulte [obter orientações sobre a gestão de custos de Azure](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
