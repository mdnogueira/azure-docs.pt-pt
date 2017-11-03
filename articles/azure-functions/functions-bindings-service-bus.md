---
title: "Acionadores de funções Service Bus do Azure e os enlaces | Microsoft Docs"
description: "Compreenda como utilizar o Service Bus do Azure acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Enlaces de funções Service Bus do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com os enlaces de Service Bus do Azure das funções do Azure. 

Funções do Azure suporta acionam e de saída enlaces para tópicos e filas do Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Acionador de Service Bus
Utilize o acionador de Service Bus para responder a mensagens a partir de uma fila do Service Bus ou tópico. 

Os acionadores de fila e o tópico do Service Bus são definidos pelos seguintes objetos JSON no `bindings` matriz de function.json:

* *fila* acionador:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* *tópico* acionador:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Tenha em atenção o seguinte:

* Para `connection`, [criar uma definição de aplicação na sua aplicação de função](functions-how-to-use-azure-function-app-settings.md) que contém a cadeia de ligação para o espaço de nomes do Service Bus, em seguida, especifique o nome da definição de aplicação no `connection` propriedade no seu acionador. Obter a cadeia de ligação, seguindo os passos apresentados em [obter as credenciais de gestão](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico.
  Se deixar `connection` vazio, o acionador parte do princípio de que uma cadeia de ligação de barramento de serviço predefinido está especificada numa aplicação definição denominada `AzureWebJobsServiceBus`.
* Para `accessRights`, os valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se utilizar uma cadeia de ligação que não tenha o **gerir** conjunto de permissões, `accessRights` para `listen`. Caso contrário, as funções de tempo de execução poderá falhar a tentar efetuar operações que exigem gerirem direitos.

## <a name="trigger-behavior"></a>Comportamento de Acionador
* **Single-threading** - por predefinição, os processos de tempo de execução de funções várias mensagens em simultâneo. Para direcionar o tempo de execução para processar apenas uma fila única ou uma mensagem de tópico simultaneamente, defina `serviceBus.maxConcurrentCalls` para 1 na *host.json*. 
  Para obter informações sobre *host.json*, consulte [estrutura de pastas](functions-reference.md#folder-structure) e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Processamento de mensagens de poison** -Service Bus o suas próprias processamento de mensagens nocivas, que não pode ser controlado ou configurado na configuração das funções do Azure ou de código. 
* **Comportamento de PeekLock** -funções o tempo de execução recebe uma mensagem na [ `PeekLock` modo](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) e chamadas `Complete` na mensagem se a função for concluído com êxito ou chamadas `Abandon` se falhar a função. 
  Se a função é executada mais do que o `PeekLock` tempo limite, o bloqueio foi renovado automaticamente.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilização de Acionador
Esta secção mostra como utilizar o acionador de barramento de serviço no seu código de função. 

Em c# e F #, a mensagem de Acionador de Service Bus pode anular a serialização para qualquer um dos seguintes tipos de entrada:

* `string`-útil para mensagens de cadeia
* `byte[]`-útil para dados binários
* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para dados serializados para JSON.
  Se declarar um tipo de entrada personalizado, tal como `CustomType`, as funções do Azure tentar anular a serialização os dados JSON para o tipo especificado.
* `BrokeredMessage`-dá-lhe a mensagem de serialização anulada com o [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) método.

No Node.js, a mensagem de Acionador de Service Bus é transmitida para a função como uma cadeia ou um objeto JSON.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemplo de Acionador
Suponha que tem o function.json seguintes:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Consulte o exemplo de específicas do idioma que processa uma mensagem de fila do Service Bus.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de Acionador em c# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemplo de Acionador em F # #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de Acionador no Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Enlace de saída do Service Bus
A saída de fila e o tópico de barramento de serviço para uma função de utilizar os seguintes objetos JSON no `bindings` matriz de function.json:

* *fila* saída:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* *tópico* saída:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Tenha em atenção o seguinte:

* Para `connection`, [criar uma definição de aplicação na sua aplicação de função](functions-how-to-use-azure-function-app-settings.md) que contém a cadeia de ligação para o espaço de nomes do Service Bus, em seguida, especifique o nome da definição de aplicação no `connection` propriedade no seu enlace de saída. Obter a cadeia de ligação, seguindo os passos apresentados em [obter as credenciais de gestão](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico.
  Se deixar `connection` vazio, o enlace de saída parte do princípio de que uma cadeia de ligação de barramento de serviço predefinido está especificada numa aplicação definição denominada `AzureWebJobsServiceBus`.
* Para `accessRights`, os valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se utilizar uma cadeia de ligação que não tenha o **gerir** conjunto de permissões, `accessRights` para `listen`. Caso contrário, as funções de tempo de execução poderá falhar a tentar efetuar operações que exigem gerirem direitos.

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilização de saída
Em c# e F #, das funções do Azure pode criar uma mensagem de fila do Service Bus, a partir de qualquer um dos seguintes tipos:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) -aspeto a definição do parâmetro `out T paramName` (c#).
  As funções deserializes o objeto para uma mensagem JSON. Se o valor de saída é nulo quando a função sai, funções cria a mensagem com um objeto nulo.
* `string`-Definição do parâmetro aspeto `out string paraName` (c#). Se o valor do parâmetro for não nulo quando a função sai, funções cria uma mensagem.
* `byte[]`-Definição do parâmetro aspeto `out byte[] paraName` (c#). Se o valor do parâmetro for não nulo quando a função sai, funções cria uma mensagem.
* `BrokeredMessage`Definição do parâmetro aspeto `out BrokeredMessage paraName` (c#). Se o valor do parâmetro for não nulo quando a função sai, funções cria uma mensagem.

Para criar várias mensagens de uma função de c#, pode utilizar `ICollector<T>` ou `IAsyncCollector<T>`. Uma mensagem é criada quando chamar o `Add` método.

No Node.js, pode atribuir um objeto de Javascript (anulada a serialização JSON), uma cadeia ou uma matriz de bytes a `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemplo de saída
Suponha que tem o seguinte function.json, que define uma saída da fila de barramento de serviço:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Consulte o exemplo de específicas do idioma que envia uma mensagem para a fila de barramento de serviço.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemplo de saída em c# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Em alternativa, para criar várias mensagens:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemplo de saída no F # #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemplo de saída no Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Em alternativa, para criar várias mensagens:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

