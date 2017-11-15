---
title: Azure esquema de eventos do grupo de recursos de grelha de eventos
description: "Descreve as propriedades que são fornecidas para eventos de grupo de recursos com grelha de eventos do Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: c7435d87f9aaa906c3f6758186b64f3458cb9716
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Esquema de eventos de grelha de eventos do Azure para grupos de recursos

Este artigo fornece as propriedades e esquema de eventos do grupo de recursos. Para uma introdução para esquemas de eventos, consulte [esquema de eventos de grelha de eventos do Azure](event-schema.md).

As subscrições do Azure e grupos de recursos emitir os mesmos tipos de eventos. Os tipos de eventos relacionados com alterações de recursos. A principal diferença é que eventos de recursos no grupo de recursos de emissão de grupos de recursos e as subscrições do Azure emitir eventos para recursos em toda a subscrição. 

## <a name="available-event-types"></a>Tipos de evento disponíveis

Grupos de recursos emitir eventos de gestão do Azure Resource Manager, tal como quando é criada uma VM ou uma conta de armazenamento foi eliminada.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | É desencadeado quando um recurso de criar ou atualizar a operação é bem sucedida. |
| Microsoft.Resources.ResourceWriteFailure | É desencadeado quando criar um recurso ou operação de atualização falha. |
| Microsoft.Resources.ResourceWriteCancel | É desencadeado quando um recurso de criar ou atualizar a operação foi cancelada. |
| Microsoft.Resources.ResourceDeleteSuccess | É desencadeado quando uma operação de eliminação de recurso é concluída com êxito. |
| Microsoft.Resources.ResourceDeleteFailure | É desencadeado quando ocorre uma falha de uma operação de eliminação de recursos. |
| Microsoft.Resources.ResourceDeleteCancel | É desencadeado quando uma operação de eliminação de recursos foi cancelada. Este evento ocorre quando uma implementação do modelo foi cancelada. |

## <a name="example-event"></a>Eventos de exemplo

O exemplo seguinte mostra o esquema de um recurso criado eventos: 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

O esquema para um evento de recurso eliminado é semelhante:

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Tópico | Cadeia | Caminho de recurso completo para a origem do evento. Este campo não é passível de escrita. |
| Requerente | Cadeia | Caminho definida pelo fabricante para o assunto do evento. |
| EventType | Cadeia | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | Cadeia | A hora que do evento é gerado com base na hora UTC do fornecedor. |
| ID | Cadeia | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos do grupo de recursos. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Autorização | Cadeia | A autorização pedida para a operação. |
| afirmações | Cadeia | As propriedades de afirmações. |
| correlationId | Cadeia | Um ID de operação de resolução de problemas. |
| httpRequest | Cadeia | Os detalhes da operação. |
| resourceProvider | Cadeia | O fornecedor de recursos, efetuar a operação. |
| resourceUri | Cadeia | O URI do recurso na operação. |
| operationName | Cadeia | A operação foi efetuada. |
| status | Cadeia | O estado da operação. |
| subscriptionId | Cadeia | O ID de subscrição do recurso. |
| TenantId | Cadeia | O ID de inquilino do recurso. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos do Azure, consulte [Novidades grelha de evento?](overview.md)
* Para obter mais informações sobre como criar uma subscrição de grelha de eventos do Azure, consulte [esquema de subscrição de evento grelha](subscription-creation-schema.md).
