---
title: "Cancelar a sua subscrição do Azure | Microsoft Docs"
description: "Descreve como cancelar a sua subscrição do Azure, como a subscrição de avaliação gratuita"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 51fe2ab891e86ae4bd7402622231af47f35aa01d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cancel-your-subscription-for-azure"></a>Cancelar a sua subscrição do Azure

Pode cancelar a sua subscrição do Azure como o [administrador da conta](billing-subscription-transfer.md#whoisaa). Após cancelar a subscrição, termina o acesso aos recursos e serviços do Azure.

Antes de cancelar a sua subscrição:

* Fazer uma cópia dos seus dados. Por exemplo, se estiver a armazenar dados no armazenamento do Azure ou SQL Server, transfira uma cópia. Se tiver uma máquina virtual, guarde uma imagem do mesmo localmente.
* Encerre os serviços. Vá para o [recursos página no portal de gestão](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), e **parar** qualquer executar máquinas virtuais, aplicações ou outros serviços.
* Considere migrar os dados. Consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

Se cancelar uma paga [plano de suporte do Azure](https://azure.microsoft.com/support/plans/), ainda é faturadas por mês para o resto do termo de 6 meses.

## <a name="cancel-subscription-using-the-azure-portal"></a>Cancelar a subscrição com o portal do Azure

1. Selecione a sua subscrição do [página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende cancelar e clique em **cancelar a subscrição**.

    ![Captura de ecrã que mostra o botão para cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Siga as instruções e concluir o cancelamento.

## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontece após cancelar a minha subscrição?

Depois de cancelar, a faturação é parada imediatamente. No entanto, pode demorar até 10 minutos para mostrar o cancelamento no portal.

Depois disso, os seus serviços estão desativados. Isto significa que as máquinas virtuais são desalocar, temporários endereços IP são libertados e o armazenamento é só de leitura.

Se cancelar a meio de um período de faturação, enviar-lhe a fatura final a data da fatura típico após terminar o período. 

Aguardaremos 90 dias antes de eliminar permanentemente os seus dados caso precisa de aceder ao mesmo ou mudar de ideias. Iremos não cobram de retenção de dados. Para obter mais informações, consulte [Microsoft Trust Center - como podemos gerir os seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar subscrição

Se cancelar a sua subscrição pay as you go acidentalmente, pode [reativá-lo no Centro de contas](billing-subscription-become-disable.md).

Se a sua subscrição não está pay as you go, contacte o suporte dentro de 90 dias do cancelamento para reativar a sua subscrição.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda tiver questões, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
