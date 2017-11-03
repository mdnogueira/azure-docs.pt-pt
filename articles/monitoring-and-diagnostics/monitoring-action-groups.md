---
title: "Criar e gerir grupos de ação no portal do Azure | Microsoft Docs"
description: "Saiba como criar e gerir grupos de ação no portal do Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: ancav
ms.openlocfilehash: 7347be8520e643cd166851d3f525a9a0726b40c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerir grupos de ação no portal do Azure
## <a name="overview"></a>Descrição geral ##
Este artigo mostra como criar e gerir grupos de ação no portal do Azure.

Pode configurar uma lista de ações com grupos de ação. Estes grupos, em seguida, podem ser utilizados quando definir alertas do registo de atividade. Estes grupos, em seguida, podem ser reutilizados por cada alerta de registo de atividade que definir, garantindo que as mesmas ações efetuadas sempre que é acionado o alerta de registo de atividade.

Um grupo de ação pode ter até 10 de cada tipo de ação. Cada ação é constituída pelas seguintes propriedades:

* **Nome**: um identificador exclusivo dentro do grupo de ação.  
* **Tipo de ação**: enviar um SMS, envie um e-mail, chamar um webhook ou enviar dados para uma ferramenta ITSM.
* **Detalhes**: correspondente phone número, endereço de correio eletrónico, webhook URI ou ITSM detalhes de ligação.

Para obter informações sobre como utilizar os modelos Azure Resource Manager para configurar grupos de ação, consulte [modelos de Gestor de recursos do grupo de ação](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Criar um grupo de ação, utilizando o portal do Azure ##
1. No [portal](https://portal.azure.com), selecione **Monitor**. O **Monitor** painel consolida todas as suas monitorização definições e dados de uma vista.

    ![O serviço de "Monitor de"](./media/monitoring-action-groups/home-monitor.png)
2. No **registo de atividade** secção, selecione **grupos ação**.

    ![No separador "Grupos de ação"](./media/monitoring-action-groups/action-groups-blade.png)
3. Selecione **adicionar grupo de ação**e preencha os campos.

    ![O comando "Adicionar grupo de ação"](./media/monitoring-action-groups/add-action-group.png)
4. Introduza um nome no **nome do grupo de ação** caixa e introduza um nome no **nome abreviado** caixa. O nome abreviado é utilizado em vez de um nome de grupo ação completa, quando as notificações são enviadas através deste grupo.

      ![Caixa de diálogo de grupo de ação adicionar"](./media/monitoring-action-groups/action-group-define.png)

5. O **subscrição** caixa autofills com a sua subscrição atual. Esta subscrição é o que o grupo de ação é guardado.

6. Selecione o **grupo de recursos** no qual o grupo de ação é guardado.

7. Defina uma lista de ações, fornecendo cada ação:

    a. **Nome**: introduza um identificador exclusivo para esta ação.

    b. **Tipo de ação**: selecione SMS, o e-mail, o webhook ou ITSM.

    c. **Detalhes**: com base no tipo de ação, introduza um número de telefone, endereço de correio eletrónico, webhook URI ou detalhes de ligação de ITSM. Além disso especificar para a ação de ITSM, **Item de trabalho** e requer a sua ferramenta ITSM outros campos. 

> [!NOTE]
> Ação de ITSM requer uma ligação de ITSM. Saiba como criar um [ITSM ligação](../log-analytics/log-analytics-itsmc-overview.md). Ação de ITSM funciona atualmente apenas para os alertas de registo de atividade. Para outros tipos de alerta, esta ação é atualmente uma sem operações.
>
>

8. Selecione **OK** ao criar o grupo de ação.

## <a name="manage-your-action-groups"></a>Gerir os grupos de ação ##
Depois de criar um grupo de ação, fica visível no **grupos ação** secção o **Monitor** painel. Selecione o grupo de ação que pretende gerir:

* Adicionar, editar ou remover as ações.
* Elimine o grupo de ação.

## <a name="next-steps"></a>Passos seguintes ##
* Saiba mais sobre [SMS alerta comportamento](monitoring-sms-alert-behavior.md).  
* Obter um [compreensão sobre o esquema de webhook alerta de registo de atividade](monitoring-activity-log-alerts-webhook.md).  
* Saiba mais sobre [ITSM conector](../log-analytics/log-analytics-itsmc-overview.md)
* Saiba mais sobre [limitação de taxa](monitoring-alerts-rate-limiting.md) em alertas. 
* Obter um [descrição geral dos alertas de registo de atividade](monitoring-overview-alerts.md)e saber como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de estado de funcionamento do serviço é publicada](monitoring-activity-log-alerts-on-service-notifications.md).
