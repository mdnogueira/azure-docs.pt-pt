---
title: "Processar eventos externos nas funções durável - Azure"
description: "Saiba como processar eventos externos na extensão do durável funções para as funções do Azure."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 23c99031ae3146a83867d10bd97d4eee8878188a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Processamento de eventos externos nas funções durável (funções do Azure)

As funções do Orchestrator têm a capacidade de espera e escutar eventos externos. Esta funcionalidade de [funções durável](durable-functions-overview.md) , muitas vezes, é útil para processamento de interação humana ou outros acionadores externos.

## <a name="wait-for-events"></a>Aguarde eventos

O [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) método permite que uma função do orchestrator no modo assíncrono, aguarde e escutar um evento externo. O autor da chamada declara o *nome* do evento e o *forma dos dados* espera receber.

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

O exemplo anterior escuta para um único evento e executa a ação quando é recebido.

Pode oiça vários eventos em simultâneo, tal como no exemplo seguinte, que aguarda que um dos três notificações de eventos possíveis.

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

O exemplo anterior escuta *qualquer* de vários eventos. Também é possível aguardar *todos os* eventos.

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) aguarda indefinidamente algumas entrada.  A aplicação de função pode estar em segurança descarregada ao aguardar a resposta. Se e quando é recebido um evento para esta instância de orquestração, é reativado automaticamente e imediatamente processa o evento.

> [!NOTE]
> São cobradas não taxas enquanto uma função do orchestrator está a aguardar uma tarefa de `WaitForExternalEvent`, independentemente do período de tempo de espera.

Se o payload do evento não é possível converter o tipo esperado `T`, é emitida uma exceção.

## <a name="send-events"></a>Enviar eventos

O [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) método o [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe envia os eventos que `WaitForExternalEvent` aguarda.  O `RaiseEventAsync` método *eventName* e *eventData* como parâmetros. Os dados do evento tem de ser serializáveis JSON.

Segue-se uma função de acionada pela fila de exemplo que enviará um evento de "Aprovação" a uma instância de função do orchestrator. O ID de instância de orquestração provém do corpo da mensagem de fila.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Internamente, `RaiseEventAsync` enqueues uma mensagem que obtém captada pela função espera do orchestrator.

> [!WARNING]
> Se não houver nenhuma instância de orquestração com especificado *ID de instância* ou se a instância não está à espera que o especificado na *nome do evento*, a mensagem de evento é rejeitada. Para obter mais informações sobre este comportamento, consulte o [problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como configurar eternal orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Execute um exemplo que aguarda eventos externos](durable-functions-counter.md)

> [!div class="nextstepaction"]
> [Execute um exemplo que aguarda para uma interação humana](durable-functions-phone-verification.md)

