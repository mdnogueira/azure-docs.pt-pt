---
title: "Receber alertas de registo de atividade em notificações de serviço do Azure | Microsoft Docs"
description: "Seja notificado através de SMS, o e-mail ou o webhook quando ocorre o serviço do Azure."
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 6e011ea3d9d8f8453068d43e390cfba46dfb3277
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas de registo de atividade em notificações de serviço
## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar alertas de registo de atividade para notificações de estado de funcionamento do serviço utilizando o portal do Azure.  

Pode receber um alerta quando o Azure envia notificações de estado de funcionamento de serviço à sua subscrição do Azure. Pode configurar o alerta com base em:

- A classe de notificação de estado de funcionamento do serviço (problemas de serviço, manutenção planeada, advisories de estado de funcionamento).
- A subscrição afetada.
- Os serviços afetados.
- Nas regiões afetadas.

Também pode configurar que o alerta deve ser enviado para:

- Selecione um grupo de ação existente.
- Crie um novo grupo de ação (que pode ser utilizado para alertas futuros).

Para mais informações sobre grupos de ação, consulte o artigo [criar e gerir grupos de ação](monitoring-action-groups.md).

Para obter informações sobre como configurar alertas de notificação de estado de funcionamento do serviço utilizando os modelos Azure Resource Manager, consulte [modelos do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Criar um alerta uma notificação de estado de funcionamento do serviço para um novo grupo de ação através do portal do Azure
1. No [portal](https://portal.azure.com), selecione **estado de funcionamento do serviço**.

    ![O serviço de "Estado de funcionamento do serviço"](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. No **alertas** secção, selecione **alertas de estado de funcionamento**.

    ![O separador "Alertas de estado de funcionamento"](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Selecione **alerta de estado de funcionamento do serviço de criar** e preencha os campos.

    ![O comando "Alerta de estado de funcionamento do serviço de criar"](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Introduza um nome no **nome de alerta de registo de atividade** caixa e fornecer um **Descrição**.

    ![A caixa de diálogo "Adicionar o alerta de registo de atividade"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. O **subscrição** caixa autofills com a sua subscrição atual. Esta subscrição é utilizada para guardar o alerta de registo de atividade. O recurso de alerta for implementado para esta subscrição e monitoriza os eventos no registo de atividade para o mesmo.

6. Selecione o **grupo de recursos** no qual o recurso de alerta é criado. Esta operação não é o grupo de recursos que é monitorizado pelo alerta. Em vez disso, é o grupo de recursos onde está localizado o recurso de alerta.

7. O **categoria de evento** caixa é automaticamente definida para **estado de funcionamento do serviço**. Opcionalmente, selecione o **serviço**, **região**, e **tipo** de notificações de estado de funcionamento do serviço que pretende receber.

8. Em **alerta via**, selecione o **novo** botão do grupo de ação. Introduza um nome no **nome do grupo de ação** caixa e introduza um nome no **nome abreviado** caixa. O nome abreviado é referenciado em notificações que são enviadas quando este alerta é acionado.

9. Defina uma lista de recetores ao indicar o recetor:

    a. **Nome**: introduza o nome, o alias ou o identificador do recetor.

    b. **Tipo de ação**: selecione SMS, e-mail, webhook, aplicações do Azure e muito mais.

    c. **Detalhes**: com base no tipo de ação escolhido, introduza um número de telefone, endereço de correio eletrónico, webhook URI, etc.

10. Selecione **OK** para criar o alerta.

Dentro de alguns minutos, o alerta está ativo e começa a acionar com base nas condições que especificou durante a criação.

Saiba como [configurar notificações de webhook para sistemas de gestão existente do problema](../service-health/service-health-alert-webhook-guide.md). Para obter informações sobre o esquema de webhook para alertas de registo de atividade, consulte [Webhooks para a atividade do Azure registar alertas](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ação definido nestes passos está reutilizável como um grupo de ação existente para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta uma notificação de estado de funcionamento do serviço para um grupo de ação existente utilizando o portal do Azure

1. Siga os passos 1 a 7 na secção anterior para criar a notificação de estado de funcionamento do serviço. 

2. Em **alerta via**, selecione o **existentes** botão do grupo de ação. Selecione o grupo de medidas adequadas.

3. Selecione **OK** para criar o alerta.

Dentro de alguns minutos, o alerta está ativo e começa a acionar com base nas condições que especificou durante a criação.

## <a name="manage-your-alerts"></a>Gerir os alertas

Depois de criar um alerta, é visível no **alertas** secção **Monitor**. Selecione o alerta que pretende gerir:

* Editá-lo.
* Elimine-o.
* Desactivar ou activar, se pretender temporariamente interromper ou retomar a receção de notificações para o alerta.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existente do problema](../service-health/service-health-alert-webhook-guide.md).
- Saiba mais sobre [notificações de estado de funcionamento do serviço](monitoring-service-notifications.md).
- Saiba mais sobre [limitação de taxa de notificação](monitoring-alerts-rate-limiting.md).
- Reveja o [esquema de webhook alerta de registo de atividade](monitoring-activity-log-alerts-webhook.md).
- Obter um [descrição geral dos alertas de registo de atividade](monitoring-overview-alerts.md)e saber como receber alertas. 
- Saiba mais sobre [grupos ação](monitoring-action-groups.md).
