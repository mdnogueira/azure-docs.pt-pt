---
title: "Temporizadores nas funções duráveis - Azure"
description: "Saiba como implementar temporizadores duráveis na extensão do durável funções para as funções do Azure."
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
ms.openlocfilehash: 27312846caf4f51e708b48655578846a52cbd1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores nas funções duráveis (funções do Azure)

[Funções duráveis](durable-functions-overview.md) fornece *temporizadores duráveis* para utilização nas funções do orchestrator para implementar atrasos ou definir tempos limite das ações de async. Os temporizadores duráveis devem ser utilizados nas funções do orchestrator em vez de `Thread.Sleep` ou `Task.Delay`.

Criar um temporizador durável chamando [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_). O método devolve uma tarefa que retoma uma data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando cria um temporizador de expiração, 4:30 da tarde, a enqueues durável estrutura de tarefas subjacente uma mensagem que fica visível apenas no 4:30 pm. Quando em execução no plano de consumo de funções do Azure, a mensagem de temporizador recentemente visível irá garantir que a aplicação de função obtém ativada numa VM adequada.

> [!WARNING]
> * Os temporizadores duráveis não não a última já mais de 7 dias devido a limitações no armazenamento do Azure. Estamos a trabalhar um [pedido de funcionalidade para expandir os temporizadores, para além de 7 dias](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Utilize sempre [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) em vez de `DateTime.UtcNow` conforme mostrado nos exemplos abaixo quando informática um prazo relativo de um temporizador durável.

## <a name="usage-for-delay"></a>Utilização de atraso

O exemplo seguinte ilustra como utilizar os temporizadores duráveis para atrasando a execução. O exemplo está a emitir uma notificação de faturação todos os dias durante dez dias.

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> Evite ciclos infinito nas funções do orchestrator. Para obter informações sobre como de forma segura e eficiente implementar cenários de ciclo infinito, consulte [Eternal Orchestrations](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Utilização para o tempo limite

Este exemplo ilustra como utilizar os temporizadores duráveis para implementar os tempos limite.

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!WARNING]
> Utilize um `CancellationTokenSource` para cancelar um temporizador durável se o seu código não espera que a sua conclusão. A estrutura de tarefas durável não alterará o estado de uma orquestração "concluídos" até que todas as tarefas pendentes são concluídas ou foi canceladas.

Este mecanismo não terminar, na verdade, execução de função da atividade em curso. Em vez disso, basta permite que a função do orchestrator ignorar o resultado e avançar. Se a sua aplicação de função utiliza o plano de consumo, ainda será faturado para qualquer tempo e memória consumidas pela função atividade abandonada. Por predefinição, as funções em execução no plano de consumo têm um tempo limite de cinco minutos. Se este limite for excedido, o anfitrião de funções do Azure é reciclado para parar a execução de todas as e impedir que uma situação de faturação possível. O [tempo limite de função é configurável](functions-host-json.md#functiontimeout).

Para obter um exemplo mais aprofundado sobre como implementar tempos limite nas funções do orchestrator, consulte o [interação humana & tempos limite - verificação do telefone](durable-functions-phone-verification.md) explicação passo a passo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como gerar e processar eventos externos](durable-functions-external-events.md)

