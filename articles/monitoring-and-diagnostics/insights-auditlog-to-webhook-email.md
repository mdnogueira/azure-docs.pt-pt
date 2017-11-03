---
title: Invocar um webhook no alertas de registo de atividade do Azure | Microsoft Docs
description: "Eventos de registo de atividade de rotas para outros serviços para ações personalizadas. Por exemplo enviar SMS, registo de erros ou notificar a equipa através do serviço de mensagens/chat."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 341ab32ad0ec691285fbf1537ee298ab30156a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Invocar um webhook no alertas de registo de atividade do Azure
Webhooks permitem-lhe uma notificação de alerta do Azure de rotas para outros sistemas de ações de pós-processamento ou personalizados. Pode utilizar um webhook num alerta para o encaminhar para os serviços que enviar SMS, registo de erros, notificar a equipa através de serviços de chat/mensagens ou fazer qualquer número de outras ações. Este artigo descreve como definir um webhook ser chamado quando um desencadeado de alerta de registo de atividade do Azure. Também mostra que aspeto o payload para o POST de HTTP para um webhook. Para obter informações sobre a configuração e o esquema para um alerta de métrica do Azure, [consulte em vez disso, esta página](insights-webhooks-alerts.md). Também pode configurar um alerta de registo de atividade para enviar correio eletrónico quando ativado.

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização e será removida, a determinada altura no futuro.
>
>

Pode configurar um registo de atividade alertas utilizando o [Cmdlets do PowerShell do Azure](insights-powershell-samples.md#create-metric-alerts), [CLI de várias plataformas](insights-cli-samples.md#work-with-alerts), ou [API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). Atualmente, não é possível definir uma cópia de segurança no portal do Azure.

## <a name="authenticating-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar utilizando um dos seguintes métodos:

1. **Autorização baseada em tokens** -o webhook URI é guardado com um ID de token, por exemplo,`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorização básica** -o webhook URI é guardado com um nome de utilizador e palavra-passe, por exemplo,`https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de payload
A operação POST contém a seguinte payload JSON e o esquema para todos os alertas com base no registo de atividade. Este esquema é semelhante às utilizadas pelo alertas com base em métrica.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

| Nome do elemento | Descrição |
| --- | --- |
| status |Utilizado para os alertas de métricas. Sempre definido como "ativado" para os alertas de registo de atividade. |
| Contexto |Contexto do evento. |
| resourceProviderName |O fornecedor de recursos do recurso afetado. |
| conditionType |Sempre "evento de". |
| nome |Nome da regra de alerta. |
| ID |ID de recurso do alerta. |
| descrição |Descrição do alerta conforme definido durante a criação do alerta. |
| subscriptionId |ID da subscrição do Azure. |
| carimbo de data/hora |Tempo no qual o evento foi gerado pelo serviço do Azure que processou o pedido. |
| resourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos para o recurso afectado |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |
| evento |Elemento que contenha metadados sobre o evento. |
| Autorização |As propriedades RBAC do evento. Estes incluem, normalmente, "action", "função" e "âmbito." |
| categoria |Categoria do evento. Os valores suportados incluem: administrativas, de alerta, segurança, ServiceHealth, recomendação. |
| autor da chamada |Endereço de e-mail do utilizador que executou a operação, afirmação UPN ou afirmação SPN com base na disponibilidade. Pode ser nulo para determinadas chamadas de sistema. |
| correlationId |Normalmente, um GUID no formato de cadeia. Eventos com correlationId pertencerem à mesma ação superior e, normalmente, partilham um correlationId. |
| eventDescription |Descrição de texto estático do evento. |
| eventDataId |Identificador exclusivo para o evento. |
| EventSource |Nome do serviço do Azure ou infraestrutura que gerou o evento. |
| httpRequest |Normalmente inclui o "clientRequestId", "clientIpAddress" e "method" (o método HTTP por exemplo, colocar). |
| nível |Um dos seguintes valores: "Críticas", "Error", "Aviso", "Informativa" e "Verboso". |
| operationId |Normalmente, um GUID partilhado entre os eventos que correspondem a única operação. |
| operationName |Nome da operação. |
| propriedades |Propriedades do evento. |
| status |Cadeia. Estado da operação. Os valores comuns incluem: "Iniciado", "Em curso", "Foi concluída com êxito", "Falha", "Ativo", "Resolvido". |
| Subestado |Normalmente inclui o código de estado HTTP da chamada REST correspondente. Também poderão incluir outras cadeias que descrevem um subestado. Os valores de subestado comuns incluem: OK (código de estado de HTTP: 200), criado (código de estado de HTTP: 201), aceite (código de estado de HTTP: 202), não conteúdo (código de estado HTTP: 204), pedido incorreto (código de estado de HTTP: 400), não encontrado (código de estado de HTTP: 404), conflito (código de estado HTTP: 409), erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503), tempo limite do Gateway (código de estado HTTP: 504) |

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre o registo de atividade](monitoring-overview-activity-logs.md)
* [Executar scripts de automatização do Azure (Runbooks) nos alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Utilize a aplicação lógica para enviar um SMS através do Twilio a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Neste exemplo é para alertas métricas, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
* [Utilize a aplicação lógica para enviar uma mensagem Slack a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Neste exemplo é para alertas métricas, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
* [Utilize a aplicação lógica para enviar uma mensagem para uma fila do Azure a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Neste exemplo é para alertas métricas, mas pode ser modificado para trabalhar com um alerta de registo de atividade.
