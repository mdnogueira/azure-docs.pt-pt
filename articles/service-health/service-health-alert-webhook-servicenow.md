---
title: "Configurar alertas de estado de funcionamento do serviço do Azure com ServiceNow | Microsoft Docs"
description: "Obter personalizadas notificações sobre eventos de estado de funcionamento de serviço à sua instância do ServiceNow."
author: shawntabrizi
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
ms.openlocfilehash: 625718ab82443c897d1b15c2eac51dea3d0dfeb4
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Configurar alertas de estado de funcionamento de serviço com o ServiceNow

Este artigo mostra-lhe como integrar o ServiceNow alertas de estado de funcionamento do serviço do Azure com um webhook. Depois de configurar a integração de webhook com a instância do ServiceNow, obtenha alertas através da infraestrutura de notificação existente quando afetam problemas de serviço do Azure. Sempre que é acionado um alerta de estado de funcionamento de serviço de Azure, aquele invoca um webhook através da API de REST de criar um script do ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Criar uma API de REST com script no ServiceNow
1.  Certifique-se de que tenha efetuado a cópia de segurança no e iniciou a sessão na sua [ServiceNow](https://www.servicenow.com/) conta.

2.  Navegue para o **serviços Web do System** secção ServiceNow e selecione **APIs REST do convertidos em script**.

    ![A secção "Criar um script serviço Web" ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

3.  Selecione **novo** para criar um novo serviço de REST convertidos em script.
 
    ![Botão "Novo convertidos em script API REST" ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Adicionar um **nome** para a REST API e defina o **API ID** para `azureservicehealth`.

5.  Selecione **submeter**.

    ![As "REST API definições" ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Selecione a API de REST que criou, e, no **recursos** separador selecione **novo**.

    ![O "separador recursos" ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

7.  **Nome** o novo recurso `event` e altere o **método HTTP** para `POST`.

8.  No **Script** secção, adicione o seguinte código JavaScript:

    >[!NOTE]
    >Tem de atualizar o `<secret>`,`<group>`, e `<email>` valor no script abaixo.
    >* `<secret>`deve ser uma cadeia aleatória, como um GUID
    >* `<group>`deve ser o grupo de ServiceNow que pretende atribuir o incidente a
    >* `<email>`deve ser a pessoa específica que pretende atribuir o incidente para (opcional)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

9.  No separador de segurança, desmarque **requer autenticação** e selecione **submeter**. O `<secret>` conjunto protege esta API em vez disso.

    ![A caixa de verificação "Requer autenticação" no ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

10.  Novamente na secção de APIs de REST convertidos em script, deverá considerar o **Base API caminho** para a nova API de REST:

     ![O "caminho de API de Base" no ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  O URL completo de integração tem o seguinte:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Criar um alerta com ServiceNow no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 no [neste artigo](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) para criar um alerta com um novo grupo de ação.

2. Definir na lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o ServiceNow **integração URL** tiver guardado anteriormente.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

3. Selecione **guardar** quando pronto para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

2. No **definições** secção, selecione **grupos ação**.

3. Localize e selecione o grupo de ação que pretende editar.

4. Adicionar à lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o ServiceNow **integração URL** tiver guardado anteriormente.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

5. Selecione **guardar** quando terminar de atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração de webhook através de um pedido POST de HTTP
1. Crie o payload de estado de funcionamento do serviço que pretende enviar. Pode encontrar um payload de webhook do Estado de funcionamento do serviço de exemplo no [Webhooks para a atividade do Azure registar alertas](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie um pedido POST de HTTP da seguinte forma:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Deverá receber um `200 OK` resposta com a mensagem "Incidente criado."

4. Aceda a [ServiceNow](https://www.servicenow.com/) para confirmar a sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existente do problema](service-health-alert-webhook-guide.md).
- Reveja o [esquema de webhook alerta de registo de atividade](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre [notificações de estado de funcionamento do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos ação](../monitoring-and-diagnostics/monitoring-action-groups.md).