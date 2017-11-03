---
title: "Singletons para funções duráveis - Azure"
description: "Como utilizar singletons na extensão do Functons durável para as funções do Azure."
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
ms.openlocfilehash: 8384d17405653a29207cdfa4f6143504d0db2022
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrators singleton nas funções durável (funções do Azure)

Para tarefas em segundo plano ou orchestrations de estilo de ator, muitas vezes, é necessitam garantir que apenas uma instância de um determinado orchestrator executa cada vez. Isto pode ser feito [funções durável](durable-functions-overview.md) atribuindo específico ID de instância para um orchestrator quando criar.

## <a name="singleton-example"></a>Exemplo de singleton

O exemplo do c# seguinte mostra uma função de Acionador de HTTP que cria uma orquestração de tarefa em segundo plano singleton. O código garante que apenas uma instância não existe para um ID de instância especificada.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Por predefinição, a instância IDs aleatoriamente são gerados GUIDs. Mas neste caso, o ID de instância é transmitido nos dados de rota do URL. As chamadas de código [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) para verificar se uma instância com o ID especificado já está em execução. Se não, é criada uma instância com esse ID.

Os detalhes de implementação da função do orchestrator, na verdade, é importante. Pode ser uma função do regular orchestrator que é iniciada e concluída ou pode ser um que é executada indefinidamente (ou seja, um [Eternal Orchestration](durable-functions-eternal-orchestrations.md)). O ponto importante é o que há alguma vez apenas uma instância em execução num momento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como chamar orchestrations secundárias](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um singleton de exemplo](durable-functions-counter.md)
