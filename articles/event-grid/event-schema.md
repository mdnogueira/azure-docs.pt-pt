---
title: Esquema de eventos de grelha de eventos do Azure
description: "Descreve as propriedades que são fornecidas para eventos com grelha de eventos do Azure"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos de grelha de eventos do Azure

Este artigo descreve as propriedades e o esquema que estão presentes todos os eventos. Eventos são compostos por um conjunto de propriedades de cadeia necessário cinco e um objeto de dados necessário. As propriedades são comuns a todos os eventos a partir de qualquer fabricante. O objeto de dados contém as propriedades que são específicas para cada publicador. Tópicos de sistema, estas propriedades são específicas ao fornecedor de recursos, tais como o Storage do Azure ou Event Hubs do Azure.

Eventos são enviados à grelha de eventos do Azure numa matriz, que pode conter muitos objetos de eventos. Se houver apenas um único evento, a matriz tem um comprimento de 1. A matriz pode ter um tamanho total de 1 MB. Cada evento na matriz está limitado a 64 KB.

## <a name="event-schema"></a>Esquema de eventos

O exemplo seguinte mostra as propriedades que são utilizadas por todos os publicadores de eventos:

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

Por exemplo, se o esquema publicado um evento de armazenamento de Blobs do Azure:

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
 
## <a name="event-properties"></a>Propriedades do evento

Todos os eventos contenham os seguintes dados de nível superior mesmos:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Tópico | Cadeia | Caminho de recurso completo para a origem do evento. Este campo não é passível de escrita. |
| Requerente | Cadeia | Caminho definida pelo fabricante para o assunto do evento. |
| EventType | Cadeia | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | Cadeia | A hora que do evento é gerado com base na hora UTC do fornecedor. |
| ID | Cadeia | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos específicos do fornecedor de recursos. |

Para saber mais sobre as propriedades no objecto de dados, consulte a origem do evento:

* [Subscrições do Azure (operações de gestão)](event-schema-subscriptions.md)
* [Armazenamento de blobs](event-schema-blob-storage.md)
* [Event hubs](event-schema-event-hubs.md)
* [Grupos de recursos (operações de gestão)](event-schema-resource-groups.md)

Para tópicos personalizados, o publicador de eventos determina o objeto de dados. Os dados de nível superior devem conter os campos mesmos como eventos definidos pelo recurso padrão. Quando publica eventos tópicos personalizados, deve considerar o assunto do seu eventos para ajudar a filtragem e de encaminhamento de modelação.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos do Azure, consulte [Novidades grelha de evento?](overview.md)
* Para obter mais informações sobre como criar uma subscrição de grelha de eventos do Azure, consulte [esquema de subscrição de evento grelha](subscription-creation-schema.md).
