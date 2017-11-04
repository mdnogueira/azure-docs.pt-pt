---
title: "Enlaces de armazenamento de fila de funções do Azure | Microsoft Docs"
description: "Compreenda como utilizar o armazenamento do Azure acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: b68ce106ceb25d19ee0bbde287891d553a448560
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Enlaces de armazenamento de filas de funções do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo descreve como configurar e enlaces de armazenamento de filas do Azure de código das funções do Azure. Funções do Azure suporta acionam e de saída enlaces para as filas do Azure. Para as funcionalidades que estão disponíveis em todos os enlaces, consulte [acionadores de funções do Azure e conceitos de enlaces](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Acionador de armazenamento de filas
O acionador de armazenamento de filas do Azure permite-lhe monitorizar um armazenamento de filas para novas mensagens e reagir aos mesmos. 

Definir um acionador de fila utilizando o **integrar** separador no portal de funções. O portal cria a seguinte definição no **enlaces** secção *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* O `connection` propriedade tem de conter o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. No portal do Azure, o editor padrão no **integrar** separador configura esta definição de aplicação para, quando seleciona uma conta de armazenamento.

Definições adicionais que podem ser fornecidas num [host.json ficheiro](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) para aperfeiçoar ainda mais os acionadores de armazenamento de filas. Por exemplo, pode alterar a fila de intervalo na host.json de consulta.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Utilizar um acionador de fila
Funções de Node.js, aceder a dados de fila utilizando `context.bindings.<name>`.


O payload de fila utilizando um parâmetro de método como aceder a funções de .NET, `CloudQueueMessage paramName`. Aqui, `paramName` é o valor especificado no [configuração acionador](#trigger). A mensagem da fila pode anular a serialização para qualquer um dos seguintes tipos:

* Objeto POCO. Utilize se o payload de fila é um objeto JSON. O tempo de execução de funções deserializes o payload para o objeto POCO. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadados de Acionador de fila
O acionador de fila fornece várias propriedades de metadados. Estas propriedades podem ser utilizadas como parte das expressões de enlace noutros enlaces ou como parâmetros no seu código. Os valores têm a mesma semântica como [ `CloudQueueMessage` ].

* **QueueTrigger** -payload de fila (se for uma cadeia válida)
* **DequeueCount** -tipo `int`. O número de vezes que esta mensagem foi removida.
* **ExpirationTime** -tipo `DateTimeOffset?`. A hora de expiração a mensagem.
* **ID** -tipo `string`. ID de mensagem de fila.
* **InsertionTime** -tipo `DateTimeOffset?`. O tempo que a mensagem foi adicionada à fila.
* **NextVisibleTime** -tipo `DateTimeOffset?`. O tempo que a mensagem seguinte será visível.
* **PopReceipt** -tipo `string`. Receção pop da mensagem.

Ver como utilizar os metadados de fila no [exemplo acionador](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemplo de Acionador
Suponha que tem o seguinte function.json que define um acionador de fila:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Consulte o exemplo de específicas do idioma que obtém e registos de metadados de fila de espera.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de Acionador em c# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de Acionador no Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Processamento de mensagens nocivas fila
Quando uma função de Acionador de fila falha, as funções do Azure tentará novamente essa função até cinco vezes para uma mensagem de fila especificado, incluindo a primeira tentativa. Se a todas as tentativas de cinco falharem, o tempo de execução de funções adiciona uma mensagem para um armazenamento de filas com o nome  *&lt;originalqueuename >-nocivas*. Pode escrever uma função para processar mensagens da fila nocivas pelo registá-los ou enviar uma notificação que atenção manual é necessária. 

Para processar mensagens nocivas manualmente, verifique o `dequeueCount` da mensagem de fila (consulte [metadados de Acionador de fila](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Armazenamento de filas de enlace de saída
O armazenamento de filas do Azure enlace de saída permite-lhe escrever mensagens a uma fila. 

Definir uma saída de fila enlace utilizando o **integrar** separador no portal de funções. O portal cria a seguinte definição no **enlaces** secção *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* O `connection` propriedade tem de conter o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. No portal do Azure, o editor padrão no **integrar** separador configura esta definição de aplicação para, quando seleciona uma conta de armazenamento.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Utilizar uma fila de saída do enlace
Funções de Node.js, vai aceder a fila de saída utilizando `context.bindings.<name>`.

Nas funções de .NET, o utilizador pode apresentar a qualquer um dos seguintes tipos. Quando existe um parâmetro de tipo `T`, `T` tem de ser um dos tipos de saída suportado, tal como `string` ou um POCO.

* `out T`(serializado como JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Também pode utilizar o tipo de retorno do método que o enlace de saída.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Exemplo de saída da fila
O seguinte *function.json* define um acionador HTTP com uma fila de saída do enlace:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Consulte o exemplo de específicas do idioma que produz uma mensagem de fila com o payload HTTP de entrada.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Exemplo de saída da fila em c# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Para enviar mensagens múltiplas, utilize um `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Exemplo de saída de fila no Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Ou, para enviar mensagens de vários

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes

Para obter um exemplo de uma função que utiliza os acionadores de armazenamento de filas e os enlaces, consulte [criar uma função acionada por armazenamento de filas do Azure](functions-create-storage-queue-triggered-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

['CloudQueueMessage']: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
