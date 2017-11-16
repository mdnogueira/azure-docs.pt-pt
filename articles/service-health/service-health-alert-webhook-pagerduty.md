---
title: "Configurar alertas de estado de funcionamento do serviço do Azure com PagerDuty | Microsoft Docs"
description: "Obter personalizadas notificações sobre eventos de estado de funcionamento de serviço à sua instância PagerDuty."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 9edcb727b9f0af348cacd5533523c4f2e8214703
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Configurar alertas de estado de funcionamento do serviço com PagerDuty

Este artigo mostra-lhe como configurar notificações de estado de funcionamento do serviço do Azure através de PagerDuty com um webhook. Ao utilizar [PagerDuty](https://www.pagerduty.com/)do tipo de integração do Microsoft Azure personalizado, pode adicionar effortlessly alertas de estado de funcionamento do serviço aos seus serviços de PagerDuty novos ou existentes.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criar um serviço de integração do Estado de funcionamento URL no PagerDuty
1.  Certifique-se de que tenha efetuado a cópia de segurança no e iniciou a sessão na sua [PagerDuty](https://www.pagerduty.com/) conta.

2.  Navegue para o **serviços** secção PagerDuty.

    ![A secção "Serviços" PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Selecione **adicionar novo serviço** ou abra um serviço existente que configurou.

4.  No **definições de integração**, selecione o seguinte:

    a. **Tipo de integração**: Microsoft Azure

    b. **Nome de integração**: \<nome\>

    ![As "definições de integração" PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Preencha quaisquer outros campos obrigatórios e selecione **adicionar**.

6.  Abrir este novo integração e copie e guarde o **integração URL**.

    ![O "URL de integração" na PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta com PagerDuty no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 no [criar um alerta uma notificação de estado de funcionamento do serviço para um novo grupo de ação através do portal do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definir na lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o PagerDuty **integração URL** tiver guardado anteriormente.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

3. Selecione **guardar** quando pronto para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

2. No **definições** secção, selecione **grupos ação**.

3. Localize e selecione o grupo de ação que pretende editar.

4. Adicionar à lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o PagerDuty **integração URL** tiver guardado anteriormente.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

5. Selecione **guardar** quando terminar de atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração de webhook através de um pedido POST de HTTP
1. Crie o payload de estado de funcionamento do serviço que pretende enviar. Pode encontrar um payload de webhook do Estado de funcionamento do serviço de exemplo no [Webhooks para a atividade do Azure registar alertas](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie um pedido POST de HTTP da seguinte forma:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Deverá receber um `202 Accepted` com uma mensagem que contém o "ID de evento"

4. Aceda a [PagerDuty](https://www.pagerduty.com/) para confirmar a sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existente do problema](service-health-alert-webhook-guide.md).
- Reveja o [esquema de webhook alerta de registo de atividade](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre [notificações de estado de funcionamento do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos ação](../monitoring-and-diagnostics/monitoring-action-groups.md).