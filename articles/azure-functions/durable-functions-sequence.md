---
title: "Função encadeamento nas funções durável - Azure"
description: "Saiba como executar um exemplo de funções durável que executa uma sequência de funções."
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
ms.openlocfilehash: 913805901bf8131e4908be03e9213539a26205ed
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Encadeamento nas funções durável - exemplo de sequência de Olá de função

Encadeamento de função refere-se o padrão de executar uma sequência de funções por uma ordem específica. Muitas vezes, o resultado de uma função tem de ser aplicada para a entrada de outra função. Este artigo explica um exemplo que utiliza [funções durável](durable-functions-overview.md) para implementar o encadeamento de função.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as instruções em [instalar funções durável](durable-functions-install.md) para configurar o exemplo.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* `E1_HelloSequence`: Uma função do orchestrator que chama `E1_SayHello` várias vezes numa sequência. Armazena as saídas do `E1_SayHello` chama e regista os resultados.
* `E1_SayHello`: Uma função de atividade que prepends uma cadeia com "Olá".

As secções seguintes explicam o código que são utilizados para programação portal do Azure e configuração. O código para o desenvolvimento de Visual Studio é apresentado no final do artigo.
 
## <a name="functionjson-file"></a>ficheiro Function.JSON

Se utilizar o portal do Azure para desenvolvimento, eis o conteúdo a *function.json* ficheiro para a função do orchestrator. A maioria das orchestrator *function.json* ficheiros quase exatamente este aspeto.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

O mais importante é o `orchestrationTrigger` tipo de enlace. Todas as funções do orchestrator tem de utilizar este tipo de Acionador.

> [!WARNING]
> Para cumprir a regra "nenhum e/s" das funções do orchestrator, não utilize qualquer entrada ou saída enlaces ao utilizar o `orchestrationTrigger` acionar o enlace.  Se outras de entrada ou saída enlaces são necessários, estes em vez disso, devem ser utilizadas no contexto de `activityTrigger` funções.

## <a name="c-script"></a>Script C#

Eis o código de origem:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Todas as funções de orquestração da c# tem de ter um `DurableOrchestrationContext` parâmetro, o que existe no `Microsoft.Azure.WebJobs.Extensions.DurableTask` assemblagem. Se estiver a utilizar o script do c#, a assemblagem pode ser referenciada através de `#r` notação. Este objeto de contexto permite-lhe chamar outros *atividade* funções e transmita os parâmetros de entrada com o respetivo [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) método.

As chamadas de código `E1_SayHello` três vezes numa sequência com valores de parâmetro diferentes. O valor de cada chamada de retorno é adicionado ao `outputs` lista, que é devolvida no final da função.

O *function.json* ficheiro para a função de atividade `E1_SayHello` é semelhante de `E1_HelloSequence` exceto que utiliza um `activityTrigger` enlace de tipo em vez de um `orchestrationTrigger` tipo de enlace.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Qualquer função chamada por uma função de orquestração tem de utilizar o `activityTrigger` enlace.

A implementação de `E1_SayHello` é uma cadeia relativamente trivial formatação a operação.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Esta função tem um [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)parâmetro, que utiliza para obter a entrada que foi transmitida para o mesmo por chamada da função do orchestrator para [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_)>.

## <a name="run-the-sample"></a>Executar o exemplo

Para executar o `E1_HelloSequence` orchestration, o HTTP POST seguinte pedido de envio.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Por exemplo, se estiver a executar o exemplo de uma aplicação de função com o nome "myfunctionapp", substitua "{anfitrião}" com "myfunctionapp.azurewebsites.net".

O resultado é uma resposta de HTTP 202, como esta (cortado de uma forma abreviada):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Neste momento, a orquestração é colocada na fila cópias de segurança e começa a executar imediatamente. O URL no `Location` cabeçalho pode ser utilizado para verificar o estado de execução.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

O resultado é o estado da orquestração. Este é executado e concluído rapidamente, para que vê-lo no *concluído* Estado com uma resposta semelhante (cortado de uma forma abreviada):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como pode ver, a `runtimeStatus` da instância é *concluído* e `output` contém o resultado da execução de função do orchestrator JSON serializado.

> [!NOTE]
> O ponto final de HTTP POST iniciado a função do orchestrator é implementado na aplicação de exemplo como um HTTP acionar a função com o nome "HttpStart". Pode implementar a lógica de arranque semelhantes para outros tipos de Acionador, como `queueTrigger`, `eventHubTrigger`, ou `timerTrigger`.

Consulte os registos de execução de função. O `E1_HelloSequence` função iniciada e concluída várias vezes devido ao comportamento de repetição descrito a [descrição geral](durable-functions-overview.md). Por outro lado, ocorreram apenas três execuções de `E1_SayHello` , uma vez que esses execuções de função não obter reproduzidas.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis o orchestration como um único c# ficheiro um projeto do Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Passos seguintes

Este exemplo foi demonstrado uma orquestração de encadeamento de função simple. O exemplo seguinte mostra como implementar o padrão fan-out/fan-em. 

> [!div class="nextstepaction"]
> [Executar o exemplo Fan-out/fan-em](durable-functions-cloud-backup.md)
