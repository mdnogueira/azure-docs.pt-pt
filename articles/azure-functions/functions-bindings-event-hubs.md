---
title: "Enlaces de funções Event Hubs do Azure | Microsoft Docs"
description: "Compreenda como utilizar os enlaces de Event Hubs do Azure das funções do Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/20/2017
ms.author: wesmc
ms.openlocfilehash: 85eb6985ef3579b1b2313db3ce5f91c3471da72f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Enlaces de funções Event Hubs do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e utilizar [Event Hubs do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) enlaces para as funções do Azure.
Funções do Azure suporta acionam e de saída vínculos para os Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Se estiver familiarizado com o Event Hubs do Azure, consulte o [descrição geral dos Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Acionador de hub de eventos
Utilize o acionador de Event Hubs para responder a um evento enviado para um fluxo de eventos de hub de eventos. Tem de ter acesso de leitura para o hub de eventos para configurar o acionador.

O acionador de função dos Event Hubs utiliza o seguinte objeto JSON no `bindings` matriz de function.json:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup`propriedade é opcional utilizada para definir o [grupo de consumidores](../event-hubs/event-hubs-features.md#event-consumers) utilizado para subscrever o hub de eventos. Se for omitido, o `$Default` é utilizado o grupo de consumidores.  
`connection`tem de ser o nome de uma definição de aplicação que contenha a cadeia de ligação ao espaço de nomes o hub de eventos.
Copie esta cadeia de ligação ao clicar no **informações de ligação** botão para o *espaço de nomes*, não o hub de eventos em si. Esta cadeia de ligação tem de ter, pelo menos, permissões de leitura para ativar o acionador.

[Definições adicionais](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) pode ser fornecido num ficheiro host.json para obter mais ajustar os acionadores de Event Hubs.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilização de Acionador
Quando uma função de Acionador de Event Hubs é acionada, a mensagem que aciona é transmitida para a função como uma cadeia.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemplo de Acionador
Suponha que tem o seguinte Event Hubs acionar no `bindings` matriz de function.json:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Consulte o exemplo de específicas do idioma que regista o corpo da mensagem do acionador de hub de eventos.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de Acionador em c# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Também pode receber o evento como uma [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objeto, que lhe dá acesso aos metadados de eventos.

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Para receber eventos num batch, alterar a assinatura de método para `string[]` ou `EventData[]`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemplo de Acionador em F # #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de Acionador no Node.js

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Os Event Hubs vínculo de saída
Utilize a saída de Event Hubs enlace escrever eventos no registo para uma transmissão de eventos de hub de eventos. Tem de ter permissão de envio para um hub de eventos para escrever eventos no mesmo.

O enlace de saída utiliza o seguinte objeto JSON no `bindings` matriz de function.json:

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection`tem de ser o nome de uma definição de aplicação que contenha a cadeia de ligação ao espaço de nomes o hub de eventos.
Copie esta cadeia de ligação ao clicar no **informações de ligação** botão para o *espaço de nomes*, não o hub de eventos em si. Esta cadeia de ligação tem de ter permissões de envio para enviar a mensagem para o fluxo de eventos.

## <a name="output-usage"></a>Utilização de saída
Esta secção mostra como utilizar o resultado dos Event Hubs no seu código de função de enlace.

O utilizador pode apresentar mensagens para o hub de eventos configurados com os seguintes tipos de parâmetro:

* `out string`
* `ICollector<string>`(para várias mensagens de saída)
* `IAsyncCollector<string>`(versão assíncrona de `ICollector<T>`)

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemplo de saída
Suponha que tem o seguinte Event Hubs enlace de saída a `bindings` matriz de function.json:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Consulte o exemplo de específicas do idioma que escreve um evento no mesmo fluxo.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemplo de saída em c# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Em alternativa, para criar várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemplo de saída no F # #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Exemplo de saída para Node.js

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Ou, para enviar mensagens de vários

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
