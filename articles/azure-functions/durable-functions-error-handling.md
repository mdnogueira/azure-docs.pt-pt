---
title: "Processamento de erros nas funções durável - Azure"
description: "Saiba como processar erros na extensão do durável funções para as funções do Azure."
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
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Processamento de erros nas funções durável (funções do Azure)

Duráveis orchestrations de função são implementados no código e podem utilizar as capacidades de processamento de erros de linguagem de programação. Com isto em mente, realmente não sabemos de quaisquer novos conceitos que precisa de saber mais sobre ao incorporando tratamento de erros e de compensação no seu orchestrations. No entanto, existem alguns comportamentos que deve ter em consideração.

## <a name="errors-in-activity-functions"></a>Erros nas funções de atividade

Qualquer exceção que é acionada numa função de atividade é marshalled novamente para a função do orchestrator e emitida como um `TaskFailedException`. Pode escrever o código de processamento e a compensação de erro que se adapta às suas necessidades na função do orchestrator.

Por exemplo, considere a seguinte função do orchestrator que transfere funds a partir de uma conta para outra:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

Se a chamada para o **CreditAccount** função falha para a conta de destino, a função do orchestrator compensates para este por crediting os funds novamente para a conta de origem.

## <a name="automatic-retry-on-failure"></a>Repetição automática em caso de falha

Quando chamar as funções de atividade ou política de repetição de funções de orquestração secundárias que pode especificar um automático. O exemplo seguinte tenta chamar uma função até 3 vezes e aguarda 5 segundos entre cada tentativa:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

O `CallActivityWithRetryAsync` API demora um `RetryOptions` parâmetro. Orquestração secundárias chama utilizando o `CallSubOrchestratorWithRetryAsync` API pode utilizar estas mesmas políticas de repetição.

Existem várias opções para personalizar a política de repetição automática. Entre eles, incluem-se:

* **Número máx. de tentativas**: O número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: A quantidade de tempo de espera antes de repetir a primeira tentativa.
* **Coeficiente de término**: coeficiente utilizado para determinar a taxa de aumento de término. Por predefinição, 1.
* **Intervalo entre tentativas de máx.**: A quantidade máxima de tempo de espera entre tentativas de repetição.
* **Repita o tempo limite**: repete a quantidade máxima de tempo gaste fazê-lo. É o comportamento predefinido para repetir indefinidamente.
* **Personalizada**: pode ser especificada uma chamada de retorno definida pelo utilizador que determina se é ou não uma chamada de função deve ser repetida.

## <a name="function-timeouts"></a>Tempos limite de função

Pode querer abandonar uma chamada de função numa função orchestrator se estiver a demorar demasiado tempo a concluir. A forma adequada para efetuar este procedimento hoje é através da criação de um [temporizador durável](durable-functions-timers.md) utilizando `context.CreateTimer` em conjunto com `Task.WhenAny`, como no exemplo seguinte:

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

## <a name="unhandled-exceptions"></a>Exceções não processadas

Se uma função do orchestrator falhar com uma exceção não processada, os detalhes da exceção são registados e a instância concluída com um `Failed` estado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
