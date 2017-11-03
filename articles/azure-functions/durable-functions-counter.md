---
title: "Com monitorização de estado singletons nas funções durável - Azure"
description: "Saiba como implementar um singleton com monitorização de estado na extensão do durável funções para as funções do Azure."
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
ms.openlocfilehash: ec7d51d3f30eb3417a48fbf8d31a9b8359e39ab9
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Com monitorização de estado singletons nas funções durável - exemplo de contador

Com monitorização de estado singletons são funções do orchestrator (potencialmente eternal) execução longa que podem armazenar o estado e ser invocadas e consultar por outras funções. Singletons com monitorização de estado são semelhantes de [modelo Ator](https://en.wikipedia.org/wiki/Actor_model) da informática distribuída.

Ao não é uma implementação de "ator" adequado, as funções do orchestrator tem muitas das mesmas características de tempo de execução. Por exemplo, são com monitorização de estado, fiável, single-threaded, transparente para a localização e globalmente endereçável. Estas são as características de que as implementações de ator real particularmente útil, mas sem a necessidade de uma estrutura separada.

Este artigo mostra como executar o *contador* exemplo. O exemplo demonstra um objeto singleton que suporte *incremento* e *diminuir* operações e atualiza o estado interno em conformidade.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as instruções em [instalar funções durável](durable-functions-install.md) para configurar o exemplo.
* Este artigo pressupõe que já leu o [Hello sequência](durable-functions-sequence.md) instruções de exemplo.

## <a name="scenario-overview"></a>Descrição geral do cenário

O cenário de contador é surprisingly difíceis de implementar utilizando regulares funções sem monitorização de estado. Um dos principais desafios tiver está a gerir **simultaneidade**. Operações como *incremento* e *diminuir* têm de ser atómico ou caso contrário, pode existir condições race que fazer com que as operações substituídas entre si.

A utilização de uma única VM para alojar os dados de contador é uma opção, mas é dispendioso e gestão **fiabilidade** pode ser um desafio, uma vez que uma única VM pode ser reiniciada periodicamente. Em alternativa pode utilizar uma plataforma distribuída com a sincronização com ferramentas como concessões de BLOBs para ajudar a gerir a simultaneidade, mas isto apresenta um elevado grau de **complexidade**.

Funções duráveis faz com que este tipo de cenário trivial implementar porque as instâncias de orquestração são com afinidade com uma única VM e execução de função do orchestrator é sempre único thread. Não só que, mas são longa execução, com monitorização de estado e possam reagir a eventos externos. O código de exemplo abaixo demonstra como implementar o contador de como uma função do orchestrator de execução longa.

## <a name="the-sample-function"></a>A função de exemplo

Este artigo explica o **E3_Counter** função na aplicação de exemplo.

As secções seguintes explicam o código que é utilizado para o desenvolvimento de Visual Studio. O código para o desenvolvimento de portal do Azure é semelhante.

## <a name="the-counter-orchestration"></a>A orquestração de contador

Eis o código que implementa a função do orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

Esta função do orchestrator, essencialmente, faz o seguinte:

1. Escuta um evento externo com o nome *operação* utilizando [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Incrementos ou é decrementada o `counterState` variável local, dependendo da operação pedida.
3. Reiniciar o orchestrator utilizando o [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) método, a definição do valor mais recente do `counterState` como entrada novo.
4. Continua a executar indefinidamente, ou até um *final* mensagem é recebida.

Este é um exemplo de um *eternal orchestration* &mdash; ou seja, um que potencialmente nunca termina. Responder às mensagens enviadas pelo [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) método, o que pode ser chamado por qualquer função não do orchestrator.

Uma característica exclusiva desta função do orchestrator é que forma eficaz não tem nenhum histórico: o `ContinueAsNew` método repõe o histórico após cada evento processado. Esta é a forma preferida para implementar um orchestrator que tem uma duração arbitrária. Utilizar um `while` ciclo pode fazer com que o histórico da função do orchestrator a crescer unbounded, o que resulta numa utilização de memória elevada desnecessariamente.

> [!NOTE]
> O `ContinueAsNew` método tem outros casos de utilização além eternal orchestrations. Para obter mais informações, consulte [Eternal Orchestrations](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Executar o exemplo

Pode começar a orquestração enviando o seguinte pedido de HTTP POST. Para permitir `counterState` para iniciar o zero (o valor predefinido para `int`), não há nenhum conteúdo neste pedido.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

O **E3_Counter** instância é iniciado e, em seguida, imediatamente aguarda que um evento ser enviado a ela com `RaiseEventAsync` ou utilizando o **sendEventUrl** webhook HTTP POST referenciada na resposta 202. Válido `eventName` os valores incluem *incr*, *decr*, e *final*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Pode ver os resultados da operação "incr" observando os registos de função no portal das funções do Azure.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Da mesma forma, se verificar o estado do orchestrator, consulte o `input` campo foi definido para o valor atualizado (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Pode continuar a enviar novas operações para esta instância e observe a que o seu estado obtém atualizado em conformidade. Se pretender eliminar a instância, pode fazê-enviando um *final* operação.

> [!WARNING]
> No momento da escrita, não existe são conhecidos condições race quando chamar `ContinueAsNew` durante o processamento em simultâneo de mensagens, tais como pedidos de terminação ou eventos externos. Para informações mais recentes nestas condições race, consulte este [problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Passos seguintes

Este exemplo foi demonstrado como processar [eventos externos](durable-functions-external-events.md) e implementar [eternal orchestrations](durable-functions-eternal-orchestrations.md) no [singletons com monitorização de estado](durable-functions-singletons.md). O exemplo seguinte mostra como utilizar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com uma interação humana.

> [!div class="nextstepaction"]
> [Executar o exemplo de interação humana](durable-functions-phone-verification.md)
