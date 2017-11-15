---
title: Azure esquema de eventos do armazenamento de BLOBs de grelha de eventos
description: "Descreve as propriedades que são fornecidas para eventos de armazenamento de Blobs com a grelha de eventos do Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: bdc64733b75fd809cf0245986aa96370343c1a34
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de eventos de grelha de eventos do Azure para o Blob storage

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de Blobs. Para uma introdução para esquemas de eventos, consulte [esquema de eventos de grelha de eventos do Azure](event-schema.md).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Armazenamento de BLOBs emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | É desencadeado quando é criado um blob. |
| Microsoft.Storage.BlobDeleted | É desencadeado quando um blob é eliminado. |

## <a name="example-event"></a>Eventos de exemplo

O exemplo seguinte mostra o esquema de um blob criado eventos: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  }
}]
```

O esquema para um evento de blob eliminado é semelhante: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
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
| dados | objeto | Dados de eventos de armazenamento de Blobs. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | Cadeia | A operação que acionou o evento. |
| clientRequestId | Cadeia | Um valor opaco gerados pelo cliente, com um limite de caracteres de 1 KB. Quando tiver ativado o registo de análise de armazenamento, é registada nos registos de análise. |
| requestId | Cadeia | O identificador exclusivo para o pedido. Utilizá-lo para o pedido de resolução de problemas. |
| ETag | Cadeia | O valor que pode utilizar para executar operações condicionalmente. |
| ContentType | Cadeia | O tipo de conteúdo especificado para o blob. |
| contentLength | número inteiro | O tamanho do blob em bytes. |
| BlobType | Cadeia | O tipo de blob. |
| URL | Cadeia | O caminho para o blob. |
| sequencer | Cadeia | Um valor controlados pelo utilizador que pode utilizar para monitorizar os pedidos. |
| storageDiagnostics | objeto | Informações sobre o diagnóstico de armazenamento. |
 
## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos do Azure, consulte [Novidades grelha de evento?](overview.md)
* Para obter mais informações sobre como criar uma subscrição de grelha de eventos do Azure, consulte [esquema de subscrição de evento grelha](subscription-creation-schema.md).
* Para uma introdução ao trabalhar com eventos de armazenamento de BLOBs, consulte [eventos de armazenamento de BLOBs de rota - CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
