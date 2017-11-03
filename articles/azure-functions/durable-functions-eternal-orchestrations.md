---
title: "Orchestrations eternal nas funções durável - Azure"
description: "Saiba como implementar eternal orchestrations utilizando a extensão de funções durável para as funções do Azure."
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
ms.openlocfilehash: 79fcbbf5f506858789a6bd1e6ad2e292c72b65a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrations eternal nas funções durável (funções do Azure)

*Eternal orchestrations* são as funções do orchestrator que nunca terminar. São úteis quando pretender utilizar [funções durável](durable-functions-overview.md) para o agregador e qualquer cenário que requeira um ciclo infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

Conforme explicado no [pontos de verificação e repetição](durable-functions-checkpointing-and-replay.md), a estrutura de tarefas durável mantém um registo do histórico de orquestração cada função. Este histórico cresce continuamente desde que a função do orchestrator continua a agendar novo trabalho. Se a função do orchestrator entra no ciclo infinito e continuamente as agendas de trabalho, este histórico foi possível crescer extremamente grande e causar problemas de desempenho significativas. O *eternal orchestration* conceito foi concebido para mitigar estes tipos de problemas para as aplicações que precisam de ciclos de infinito.

## <a name="resetting-and-restarting"></a>A repor e reiniciar

Em vez de utilizar ciclos infinito, as funções do orchestrator repor o respetivo estado ao chamar o [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) método. Este método aceita um único parâmetro JSON serializável, que torna-se a nova entrada para a próxima geração de função do orchestrator.

Quando `ContinueAsNew` denomina-se, a instância enqueues uma mensagem a próprio antes de que sai. A mensagem de reinicia a instância com o valor de entrada novo. O mesmo ID de instância é mantido, mas o histórico da função do orchestrator eficazmente é truncado.

> [!NOTE]
> A estrutura de tarefas durável mantém o mesmo ID de instância, mas internamente cria uma nova *ID de execução* para a função do orchestrator que obtém repor por `ContinueAsNew`. Este ID de execução, geralmente, não está exposta externamente, mas poderá ser útil de saber sobre quando a depuração de execução de orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódica

Um caso de utilização para eternal orchestrations é o código que necessita para realizar o trabalho periódico indefinidamente.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer<string>(nextCleanup);

    context.ContinueAsNew();
}
```

A diferença entre este exemplo e uma função acionada por temporizador é que os tempos de Acionador de limpeza aqui não se baseiam com base numa agenda. Por exemplo, uma agenda CRON que executa uma função de cada hora será executá-lo em 1:00, 2:00, 3:00 etc. e potencialmente foi depare com problemas de sobreposição. Neste exemplo, no entanto, se a limpeza demora 30 minutos, em seguida, este será agendada às 1:00, 2:30, 4:00, etc. e não existe nenhuma oportunidade de sobreposição.

## <a name="counter-example"></a>Exemplo de contador

Eis um exemplo simplificado de um *contador* função escuta *incremento* e *diminuir* eternally eventos.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração eternal

Se uma função do orchestrator tem de concluir, eventualmente, em seguida, tudo o que precisa de fazer é *não* chamar `ContinueAsNew` e permitir que a função de sair.

Se tiver uma função do orchestrator um ciclo infinito e tem de ser parado, utilize o [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) método para a interromper. Para obter mais informações, consulte [instância gestão](durable-functions-instance-management.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar singleton orchestrations](durable-functions-singletons.md)

> [!div class="nextstepaction"]
> [Executar uma orquestração eternal de exemplo](durable-functions-counter.md)
