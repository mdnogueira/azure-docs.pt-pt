---
title: "Configurar alertas de estado de funcionamento do serviço do Azure com OpsGenie | Microsoft Docs"
description: "Obter personalizadas notificações sobre eventos de estado de funcionamento de serviço à sua instância OpsGenie."
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
ms.openlocfilehash: a2309a050225dd0d7ac8d5b3e4c762bc5bcb25c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Configurar alertas de estado de funcionamento do serviço com OpsGenie

Este artigo mostra-lhe como configurar alertas de estado de funcionamento do serviço do Azure com OpsGenie com um webhook. Ao utilizar [OpsGenie](https://www.opsgenie.com/)da integração de estado de funcionamento do serviço do Azure, pode reencaminhar alertas de estado de funcionamento de serviço de Azure para OpsGenie. OpsGenie pode determinar as pessoas a notificar corretas com base na chamada agendas, através de correio eletrónico, mensagens de texto (SMS), as chamadas telefónicas, iOS e notificações Android push e, em constante crescendo alertas até que o alerta seja confirmado ou fechado.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Criar um serviço de integração do Estado de funcionamento URL no OpsGenie
1.  Certifique-se de que tenha efetuado a cópia de segurança no e iniciou a sessão na sua [OpsGenie](https://www.opsgenie.com/) conta.

2.  Navegue para o **integrações** secção OpsGenie.

    ![A secção "Integrações" OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Selecione o **estado de funcionamento de serviço de Azure** botão de integração.

    ![O "Estado de funcionamento de serviço do Azure clique no botão" no OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Nome** o alerta e especifique o **atribuído à equipa** campo.

5.  Preencha os outros campos como **destinatários**, **ativado**, e **suprimir notificações**.

6.  Copie e guarde o **integração URL**, que já deve conter o `apiKey` acrescentado ao fim.

    ![O "URL de integração" na OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Selecione **guardar integração**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Criar um alerta com OpsGenie no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 no [criar um alerta uma notificação de estado de funcionamento do serviço para um novo grupo de ação através do portal do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definir na lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o OpsGenie **integração URL** tiver guardado anteriormente.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

3. Selecione **guardar** quando pronto para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

2. No **definições** secção, selecione **grupos ação**.

3. Localize e selecione o grupo de ação que pretende editar.

4. Adicionar à lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o OpsGenie **integração URL** tiver guardado anteriormente.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

5. Selecione **guardar** quando terminar de atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração de webhook através de um pedido POST de HTTP
1. Crie o payload de estado de funcionamento do serviço que pretende enviar. Pode encontrar um payload de webhook do Estado de funcionamento do serviço de exemplo no [Webhooks para a atividade do Azure registar alertas](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie um pedido POST de HTTP da seguinte forma:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Deverá receber um `200 OK` resposta com a mensagem de estado de "êxito".

4. Aceda a [OpsGenie](https://www.opsgenie.com/) para confirmar a sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existente do problema](service-health-alert-webhook-guide.md).
- Reveja o [esquema de webhook alerta de registo de atividade](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre [notificações de estado de funcionamento do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos ação](../monitoring-and-diagnostics/monitoring-action-groups.md).