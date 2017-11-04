---
title: Compreender o esquema de webhook utilizado nos alertas do registo de atividade | Microsoft Docs
description: "Saiba mais sobre o esquema de JSON que é registado para um URL do webhook quando ativa a um alerta de registo de atividade."
author: johnkemnetz
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
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 75c71bcd16573d4f4dd3377c623aa9b414aa3906
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks para alertas de registo de atividade do Azure
Como parte da definição de um grupo de ação, pode configurar pontos finais de webhook para receber notificações de alerta de registo de atividade. Com webhooks, pode encaminhar estas notificações para outros sistemas de ações de pós-processamento ou personalizados. Este artigo mostra que aspeto o payload para o POST de HTTP para um webhook.

Para obter mais informações sobre alertas de registo de atividade, consulte como [criam alertas de registo de atividade do Azure](monitoring-activity-log-alerts.md).

Para informações sobre os grupos de ação, consulte como [criar grupos de ação](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook, opcionalmente, pode utilizar com base no token de autorização para autenticação. O webhook URI é guardado com um ID de token, por exemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Esquema de payload
O payload JSON contido na operação POST difere com base no campo de data.context.activityLog.eventSource o payload.

###<a name="common"></a>Common
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>Administrativos
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

Para detalhes de esquema específicos sobre alertas de registo de atividade de notificação de estado de funcionamento de serviço, consulte [notificações de estado de funcionamento do serviço](monitoring-service-notifications.md).

Para detalhes de esquema específico em todos os outros alertas de registo de atividade, consulte [descrição geral do registo de atividade do Azure](monitoring-overview-activity-logs.md).

| Nome do elemento | Descrição |
| --- | --- |
| status |Utilizado para os alertas de métricas. Sempre definido como "ativado" para os alertas de registo de atividade. |
| Contexto |Contexto do evento. |
| resourceProviderName |O fornecedor de recursos do recurso afetado. |
| conditionType |Sempre "evento de". |
| nome |Nome da regra de alerta. |
| ID |ID de recurso do alerta. |
| descrição |Descrição do alerta definida quando o alerta é criado. |
| subscriptionId |ID da subscrição do Azure. |
| carimbo de data/hora |Tempo no qual o evento foi gerado pelo serviço do Azure que processou o pedido. |
| resourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos para o recurso afetado. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |
| evento |Elemento que contém metadados sobre o evento. |
| Autorização |As propriedades de controlo de acesso baseado em funções do evento. Estas propriedades incluem, normalmente, a ação, a função e o âmbito. |
| categoria |Categoria do evento. Os valores suportados incluem administração, o alerta, segurança, ServiceHealth e recomendação. |
| autor da chamada |Endereço de e-mail do utilizador que executou a operação, afirmação UPN ou afirmação SPN com base na disponibilidade. Pode ser nulo para determinadas chamadas de sistema. |
| correlationId |Normalmente, um GUID no formato de cadeia. Eventos com correlationId pertencerem à mesma ação superior e, normalmente, partilham um correlationId. |
| eventDescription |Descrição de texto estático do evento. |
| eventDataId |Identificador exclusivo para o evento. |
| EventSource |Nome do serviço do Azure ou infraestrutura que gerou o evento. |
| httpRequest |O pedido inclui normalmente o clientRequestId, clientIpAddress e método HTTP (por exemplo, colocar). |
| nível |Um dos seguintes valores: crítico, erro, aviso, informativo e verboso. |
| operationId |Normalmente, um GUID partilhado entre os eventos que correspondem a única operação. |
| operationName |Nome da operação. |
| propriedades |Propriedades do evento. |
| status |Cadeia. Estado da operação. Os valores comuns incluem iniciado, em curso, com êxito, falha, Active Directory e resolvido. |
| Subestado |Normalmente inclui o código de estado HTTP da chamada REST correspondente. Também poderão incluir outras cadeias que descrevem um subestado. Valores de subestado comuns incluem OK (código de estado de HTTP: 200), criado (código de estado de HTTP: 201), aceite (código de estado de HTTP: 202), não conteúdo (código de estado de HTTP: 204), pedido incorreto (código de estado de HTTP: 400), não foi encontrado (código de estado HTTP: 404), conflito (código de estado de HTTP: 409), erro interno do servidor (código de estado de HTTP: 500), serviço indisponível (código de estado HTTP: 503) e tempo limite do Gateway (código de estado HTTP : 504). |

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o registo de atividade](monitoring-overview-activity-logs.md).
* [Executar scripts de automatização (Runbooks) nos alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* [Utilizar uma aplicação lógica para enviar um SMS através do Twilio a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Neste exemplo é para alertas métricas, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
* [Utilizar uma aplicação lógica para enviar uma mensagem Slack a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Neste exemplo é para alertas métricas, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
* [Utilizar uma aplicação lógica para enviar uma mensagem para uma fila do Azure a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Neste exemplo é para alertas métricas, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
