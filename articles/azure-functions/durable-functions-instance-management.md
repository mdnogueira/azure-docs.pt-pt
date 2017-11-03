---
title: "Gerir instâncias nas funções durável - Azure"
description: "Saiba como gerir instâncias na extensão do durável funções para as funções do Azure."
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
ms.openlocfilehash: 734853dbee7a6f216763cd82d424c9e639b1f8c9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gerir instâncias nas funções durável (funções do Azure)

[Funções duráveis](durable-functions-overview.md) instâncias de orquestração podem ser iniciadas, terminadas, consultar e enviar eventos de notificação. Toda a gestão de instância é feita utilizando o [cliente orchestration enlace](durable-functions-bindings.md). Este artigo entra os detalhes de cada operação de gestão de instância.

## <a name="starting-instances"></a>A partir de instâncias

O [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) método no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) inicia uma nova instância de uma função do orchestrator. As instâncias desta classe podem ser adquiridas utilizando o `orchestrationClient` enlace. Internamente, este método enqueues uma mensagem na fila de controlo, em seguida, aciona o início de uma função com o nome especificado que utiliza o `orchestrationTrigger` acionar o enlace.

Os parâmetros são os seguintes:

* **Nome**: O nome da função do orchestrator a agendar.
* **Entrada**: quaisquer dados JSON serializável que devem ser transmitidos como entrada para a função do orchestrator.
* **InstanceId**: (opcional) o exclusivo ID da instância. Se não for especificado, será gerado um ID de instância aleatório.

Eis um exemplo simples c#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Para idiomas não .NET, a função de enlace de saída pode ser utilizado para iniciar, bem como novas instâncias. Neste caso, pode ser utilizado qualquer objeto serializável para o JSON que tem os parâmetros de três acima como campos. Por exemplo, considere a seguinte função Node.js:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Recomendamos que utilize um identificador aleatório para o ID de instância. Isto irá ajudar a assegurar uma distribuição de carga igual quando dimensionamento funções do orchestrator em várias VMs. O tempo adequado para utilizar os IDs de instância não aleatórias é quando o ID tem de ser de uma origem externa ou quando implementar o [singleton orchestrator](durable-functions-singletons.md) padrão.

## <a name="querying-instances"></a>Consulta de instâncias

O [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) método no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe consulta o estado de uma instância de orquestração. Demora um `instanceId` como um parâmetro e devolve um objeto com as seguintes propriedades:

* **Nome**: O nome da função do orchestrator.
* **InstanceId**: O ID de instância do orchestration (deve ser o mesmo que o `instanceId` entrada).
* **CreatedTime**: A hora em que a função do orchestrator começou a ser executada.
* **LastUpdatedTime**: A hora em que o orchestration último checkpointed.
* **Entrada**: A entrada da função como um valor JSON.
* **Saída**: O resultado da função como um valor JSON (se a função foi concluída). Se a função do orchestrator falhou, esta propriedade incluirá os detalhes da falha. Se a função do orchestrator foi terminada, esta propriedade incluirá o motivo fornecido para a terminação (se aplicável).
* **RuntimeStatus**: um dos seguintes valores:
    * **Executar**: A instância começar a ser executada.
    * **Concluir**: A instância concluída normalmente.
    * **ContinuedAsNew**: A instância reiniciou-se com um histórico de novo. Este é um estado transitório.
    * **Não foi possível**: A instância falhou com um erro.
    * **Terminada**: A instância abruptamente foi terminada.
    
Este método devolve `null` se a instância não existe ou ainda não foi iniciado em execução.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await checker.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Consulta de instância é atualmente suportado apenas para funções de c#.

## <a name="terminating-instances"></a>Instâncias de terminação

Uma instância em execução pode ser terminada utilizando o [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) método o [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe. Os dois parâmetros são um `instanceId` e um `reason` cadeia, que será escrita nos registos e o estado de instância. Uma instância terminada deixa de ser executado assim que chegar a próxima `await` ponto ou terminará imediatamente se já está ativada uma `await`.

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Está atualmente a terminação de instância só são suportadas para funções de c#.

## <a name="sending-events-to-instances"></a>Envio de eventos para instâncias

As notificações de eventos podem ser enviadas para executar instâncias utilizando o [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) método o [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe. As instâncias que podem processar estes eventos são aqueles que estão a aguardar uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). As introduções são:

* **InstanceId**: O ID exclusivo da instância.
* **EventName**: O nome do evento para enviar.
* **EventData**: um payload JSON serializável para enviar para a instância.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Gerar eventos atualmente só é suportada para as funções de c#.

> [!WARNING]
> Se não houver nenhuma instância de orquestração com especificado *ID de instância* ou se a instância não está à espera que o especificado na *nome do evento*, a mensagem de evento é rejeitada. Para obter mais informações sobre este comportamento, consulte o [problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como utilizar as APIs de HTTP para a instância de gestão](durable-functions-http-api.md)
