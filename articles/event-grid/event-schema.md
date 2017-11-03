---
title: Esquema de eventos de grelha de eventos do Azure
description: "Descreve as propriedades que são fornecidas para eventos com grelha de eventos do Azure"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/20/2017
ms.author: babanisa
ms.openlocfilehash: e251cbfe7c4d8dfbd492817a8fa7af48e6b379df
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos de grelha de eventos do Azure

Este artigo fornece as propriedades e esquema de eventos. Eventos são compostos por um conjunto de propriedades de cadeia necessário cinco e um objeto de dados necessário. As propriedades são comuns a todos os eventos a partir de qualquer fabricante. O objeto de dados contém as propriedades que são específicas para cada publicador. Tópicos de sistema, estas propriedades são específicas ao fornecedor de recursos, tais como o Storage do Azure ou Event Hubs do Azure.

Eventos são enviados à grelha de eventos do Azure numa matriz, que pode conter muitos objetos de eventos. Se houver apenas um único evento, a matriz tem um comprimento de 1. A matriz pode ter um tamanho total de 1 MB. Cada evento na matriz está limitado a 64 KB.
 
## <a name="event-properties"></a>Propriedades do evento

Todos os eventos irão conter os seguintes dados de nível superior mesmos:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Tópico | Cadeia | Caminho de recurso completo para a origem do evento. Este campo não é passível de escrita. |
| Requerente | Cadeia | Caminho definida pelo fabricante para o assunto do evento. |
| EventType | Cadeia | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | Cadeia | A hora que do evento é gerado com base na hora UTC do fornecedor. |
| ID | Cadeia | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos específicos do fornecedor de recursos. |

## <a name="available-event-sources"></a>Origens de eventos disponível

As seguintes origens de eventos publicar eventos para consumo através de grelha de evento:

* Grupos de recursos (operações de gestão)
* Subscrições do Azure (operações de gestão)
* Event hubs
* Tópicos personalizados

## <a name="azure-subscriptions"></a>Subscrições do Azure

As subscrições do Azure agora podem emitir eventos de gestão do Azure Resource Manager, tal como quando é criada uma VM ou uma conta de armazenamento foi eliminada.

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.Resources.ResourceWriteSuccess**: gerado quando um recurso crie ou operação de atualização é concluída com êxito.  
- **Microsoft.Resources.ResourceWriteFailure**: gerado quando criar um recurso ou falha da operação de atualização.  
- **Microsoft.Resources.ResourceWriteCancel**: gerado quando um recurso de criar ou atualizar a operação foi cancelada.  
- **Microsoft.Resources.ResourceDeleteSuccess**: desencadeado quando uma operação de eliminação de recurso é concluída com êxito.  
- **Microsoft.Resources.ResourceDeleteFailure**: desencadeado quando ocorre uma falha de uma operação de eliminação de recursos.  
- **Microsoft.Resources.ResourceDeleteCancel**: desencadeado quando uma operação de eliminação de recursos foi cancelada. Isto acontece quando uma implementação do modelo foi cancelada.

### <a name="example-event-schema"></a>Esquema de eventos de exemplo

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
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



## <a name="resource-groups"></a>Grupos de recursos

Grupos de recursos agora podem emitir eventos de gestão do Azure Resource Manager, tal como quando é criada uma VM ou uma conta de armazenamento foi eliminada.

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.Resources.ResourceWriteSuccess**: gerado quando um recurso crie ou operação de atualização é concluída com êxito.  
- **Microsoft.Resources.ResourceWriteFailure**: gerado quando criar um recurso ou falha da operação de atualização.  
- **Microsoft.Resources.ResourceWriteCancel**: gerado quando um recurso de criar ou atualizar a operação foi cancelada.  
- **Microsoft.Resources.ResourceDeleteSuccess**: desencadeado quando uma operação de eliminação de recurso é concluída com êxito.  
- **Microsoft.Resources.ResourceDeleteFailure**: desencadeado quando ocorre uma falha de uma operação de eliminação de recursos.  
- **Microsoft.Resources.ResourceDeleteCancel**: desencadeado quando uma operação de eliminação de recursos foi cancelada. Isto acontece quando uma implementação do modelo foi cancelada.

### <a name="example-event"></a>Eventos de exemplo

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
        "httpRequest":"",
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



## <a name="event-hubs"></a>Event Hubs

Eventos de Hubs de eventos atualmente são emitidos apenas quando um ficheiro serão automaticamente enviado para o armazenamento através da funcionalidade de captura.

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.EventHub.CaptureFileCreated**: desencadeado quando é criado um ficheiro de captura.

### <a name="example-event"></a>Eventos de exemplo

Este evento de exemplo mostra o esquema de um evento de Event Hubs é desencadeado quando a funcionalidade de captura armazena um ficheiro: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="available-event-types"></a>Tipos de evento disponíveis

- **Microsoft.Storage.BlobCreated**: desencadeado quando é criado um blob.
- **Microsoft.Storage.BlobDeleted**: gerado quando um blob é eliminado.

### <a name="example-event"></a>Eventos de exemplo

Este evento de exemplo mostra o esquema de um evento de armazenamento é desencadeado quando é criado um blob: 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>Tópicos personalizados

O payload de dados dos seus eventos personalizados está definido por si e pode ser qualquer objeto JSON corretamente formatado. Os dados de nível superior devem conter os campos mesmos como eventos definidos pelo recurso padrão. Quando publica eventos tópicos personalizados, deve considerar o assunto do seu eventos para ajudar a filtragem e de encaminhamento de modelação.

### <a name="example-event"></a>Eventos de exemplo

O exemplo seguinte mostra um evento para um tópico personalizado:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos do Azure, consulte [Novidades grelha de evento?](overview.md).
* Para obter mais informações sobre como criar uma subscrição de grelha de eventos do Azure, consulte [esquema de subscrição de evento grelha](subscription-creation-schema.md).
