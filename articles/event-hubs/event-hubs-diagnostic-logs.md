---
title: "Os registos de diagnóstico dos Event Hubs do Azure | Microsoft Docs"
description: "Saiba como configurar registos de diagnóstico para os event hubs no Azure."
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: bcc8427d57a001f73d321fbf35c5226a047b68d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-diagnostic-logs"></a>Registos de diagnóstico de Hubs de eventos

Pode ver dois tipos de registos de Event Hubs do Azure:
* **[Registos de atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Estes registos tem informações sobre as operações executadas numa tarefa. Os registos estão sempre ativados.
* **[Os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Pode configurar os registos de diagnóstico para uma vista mais rico de tudo o que acontece com uma tarefa. Registos de diagnóstico abrange atividades desde o momento em que é criada a tarefa até que a tarefa é eliminada, incluindo atualizações e as atividades que ocorrem enquanto a tarefa está em execução.

## <a name="turn-on-diagnostic-logs"></a>Ativar registos de diagnóstico

Registos de diagnóstico estão desativados por predefinição. Para ativar os registos de diagnóstico:

1.  No [portal do Azure](https://portal.azure.com), em **monitorização + gestão**, clique em **registos de diagnóstico**.

    ![navegação do painel para os registos de diagnóstico](./media/event-hubs-diagnostic-logs/image1.png)

2.  Clique no recurso que pretende monitorizar.

3.  Clique em **ative os diagnósticos**.

    ![Ativar registos de diagnóstico](./media/event-hubs-diagnostic-logs/image2.png)

4.  Para **estado**, clique em **no**.

    ![Alterar o estado de registos de diagnóstico](./media/event-hubs-diagnostic-logs/image3.png)

5.  Definir o destino de arquivo que pretende; Por exemplo, uma conta de armazenamento, um hub de eventos ou Log Analytics do Azure.

6.  Guarde as novas definições de diagnóstico.

Novas definições entram em vigor no cerca de 10 minutos. Depois disso, os registos são apresentados no destino arquivo configurado, no **registos de diagnóstico** painel.

Para obter mais informações sobre a configuração de diagnósticos, consulte o [descrição geral dos registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Categorias de registos de diagnóstico
Os Event Hubs captura registos de diagnóstico para duas categorias:

* **ArchiveLogs**: registos relacionados com os Event Hubs arquivos, especificamente, os registos relacionados com erros de arquivo.
* **OperationalLogs**: informações sobre o que acontece durante as operações de Event Hubs, especificamente, a operação de tipo, incluindo a criação do hub de eventos, os recursos utilizados e o estado da operação.

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico
Todos os registos são armazenados no formato de JavaScript Object Notation (JSON). Cada entrada tem campos de cadeia que utilizam o formato descrito nas secções seguintes.

### <a name="archive-logs-schema"></a>Esquema de registos de arquivo

As cadeias JSON do arquivo registo incluir elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
TaskName | Descrição da tarefa que falhou.
ActivityId | ID interno utilizado para o controlo.
trackingId | ID interno utilizado para o controlo.
resourceId | ID de recurso de Gestor de recursos do Azure.
EventHub | Hub de eventos nome completo (inclui o nome do espaço de nomes).
partitionId | Partição do Hub de eventos que está a ser escrita para.
archiveStep | ArchiveFlushWriter
startTime | Hora de início de falha.
falhas | Número de vezes que ocorreu a falha.
durationInSeconds | Duração da falha.
Mensagem | Mensagem de erro.
categoria | ArchiveLogs

O código seguinte é um exemplo de um registo de arquivo cadeia JSON:

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Esquema de registos operacionais

Cadeias JSON do registo operacional incluem elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
ActivityId | ID interno utilizado para controlar o objetivo.
EventName | Nome da operação.  
resourceId | ID de recurso de Gestor de recursos do Azure.
SubscriptionId | ID da subscrição.
EventTimeString | Tempo da operação.
EventProperties | Propriedades da operação.
Estado | Estado da operação.
Autor da chamada | Autor da chamada da operação (Azure portal ou de gestão do cliente).
categoria | OperationalLogs

O código seguinte é um exemplo de uma cadeia JSON do registo operacional:

```json
Example:
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Event Hubs](event-hubs-what-is-event-hubs.md)
* [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
* [Introdução ao Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
