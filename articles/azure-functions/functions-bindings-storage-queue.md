---
title: "Enlaces de armazenamento de filas do Azure para as funções do Azure"
description: "Compreenda como utilizar o acionador de armazenamento de filas do Azure e de saída do enlace das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 0aae58fa52f9f7f64b08e1701b7688a90c56e6ed
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Enlaces de armazenamento de filas do Azure para as funções do Azure

Este artigo explica como trabalhar com enlaces de armazenamento de filas do Azure das funções do Azure. Funções do Azure suporta acionam e enlaces para filas de saída.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Acionador

Utilize o acionador de fila para iniciar uma função quando é recebido um novo item de uma fila. A mensagem da fila é fornecida como entrada para a função.

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#trigger---c-example)
* [Script do c#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Acionador - c# exemplo

O seguinte exemplo mostra [pré-compilada c#](functions-dotnet-class-library.md) código que consulta o `myqueue-items` fila e escreve um registo de cada vez que um item de fila é processado.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um acionador de blob enlace num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza o enlace. Os inquéritos de função a `myqueue-items` fila e escreve um registo de cada vez que um item de fila é processado.

Eis o *function.json* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código de script do c#:

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

O [utilização](#trigger---usage) secção explica `myQueueItem`, que é designado pelo `name` propriedade no function.json.  O [secção de metadados da mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um acionador de blob enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. Os inquéritos de função a `myqueue-items` fila e escreve um registo de cada vez que um item de fila é processado.

Eis o *function.json* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

O [utilização](#trigger---usage) secção explica `myQueueItem`, que é designado pelo `name` propriedade no function.json.  O [secção de metadados da mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

## <a name="trigger---attributes"></a>Acionador - atributos
 
Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize os seguintes atributos para configurar um acionador de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  O construtor do atributo utiliza o nome da fila para monitorizar, conforme mostrado no exemplo seguinte:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo seguinte:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Para obter um exemplo completado, consulte [acionador - pré-compilada c# exemplo](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Outra forma para especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo seguinte mostra o nível de classe e o nível de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a utilizar é determinada pela seguinte ordem:

* O `QueueTrigger` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `QueueTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A definição de aplicação "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `QueueTrigger` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d| tem de ser definido como `queueTrigger`. Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure.|
|**direção**| n/d | No *function.json* apenas de ficheiros. tem de ser definido como `in`. Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure. |
|**nome** | n/d |O nome da variável que representa a fila no código da função.  | 
|**queueName** | **QueueName**| O nome da fila para consultar. | 
|**ligação** | **Ligação** |O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação de armazenamento predefinida na definição de aplicação com o nome `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização
 
Em c# e c# script, aceder aos dados blob utilizando um parâmetro de método como `Stream paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. É possível vincular a qualquer um dos seguintes tipos:

* POCO objeto - funções o tempo de execução deserializes um payload JSON para um objeto de POCO. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Em JavaScript, utilize `context.bindings.<name>` para aceder ao payload de item de fila. Se o payload JSON, este é anular a serialização para um objeto.

## <a name="trigger---message-metadata"></a>Acionador - mensagem metadados

O acionador de fila fornece várias propriedades de metadados. Estas propriedades podem ser utilizadas como parte das expressões de enlace noutros enlaces ou como parâmetros no seu código. Os valores têm a mesma semântica como [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Payload de fila (se for uma cadeia válida). Se a fila de mensagens payload como uma cadeia, `QueueTrigger` tem o mesmo valor que a variável com o nome de `name` propriedade no *function.json*.|
|`DequeueCount`|`int`|O número de vezes que esta mensagem foi removida.|
|`ExpirationTime`|`DateTimeOffset?`|A hora de expiração a mensagem.|
|`Id`|`string`|ID de mensagem de fila.|
|`InsertionTime`|`DateTimeOffset?`|O tempo que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset?`|O tempo que a mensagem seguinte será visível.|
|`PopReceipt`|`string`|Receção pop da mensagem.|

## <a name="trigger---poison-messages"></a>Acionador - mensagens nocivas

Quando uma função de Acionador de fila falha, as funções do Azure tentará novamente a função de até cinco vezes para uma mensagem de fila especificado, incluindo a primeira tentativa. Se a todas as tentativas de cinco falharem, o tempo de execução de funções adiciona uma mensagem para uma fila com o nome  *&lt;originalqueuename >-nocivas*. Pode escrever uma função para processar mensagens da fila nocivas pelo registá-los ou enviar uma notificação que atenção manual é necessária.

Para processar mensagens nocivas manualmente, verifique o [dequeueCount](#trigger---message-metadata) da mensagem de fila.

## <a name="trigger---hostjson-properties"></a>Acionador - host.json propriedades

O [host.json](functions-host-json.md#queues) ficheiro contém definições que controlam o comportamento de Acionador de fila.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Saída

Utilize a saída de armazenamento de filas do Azure para guardar as mensagens de uma fila de enlace.

## <a name="output---example"></a>De saída - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#output---c-example)
* [Script do c#](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - c# exemplo

O seguinte exemplo mostra [pré-compilada c#](functions-dotnet-class-library.md) código que cria uma mensagem de fila para cada pedido HTTP recebido.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

O exemplo seguinte mostra um acionador de blob enlace num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza o enlace. A função cria um item de fila com um payload POCO para cada pedido HTTP recebido.

Eis o *function.json* ficheiro:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

O [configuração](#output---configuration) secção explica estas propriedades.

Eis c# código de script que cria uma mensagem de fila única:

```cs
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

Pode enviar mensagens de vários em simultâneo utilizando um `ICollector` ou `IAsyncCollector` parâmetro. Script código c# Eis que envia mensagens vários, uma com os dados do pedido de HTTP e uma com valores hard-coded:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

O exemplo seguinte mostra um acionador de blob enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função cria um item de fila para cada pedido HTTP recebido.

Eis o *function.json* ficheiro:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

O [configuração](#output---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Pode enviar várias mensagens ao mesmo tempo que define uma matriz de mensagem para o `myQueueItem` vínculo de saída. O seguinte código JavaScript envia duas mensagens de fila com valores hard-coded para cada pedido HTTP recebido.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos
 
Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), que está definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O atributo aplica-se a um `out` parâmetro ou o valor de retorno da função. O construtor do atributo utiliza o nome da fila, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Para obter um exemplo completado, consulte [resultado - pré-compilada c# exemplo](#output---c-example).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento ao nível de classe, método ou parâmetro. Para obter mais informações, consulte [acionador - atributos](#trigger---attributes-for-precompiled-c).

## <a name="output---configuration"></a>De saída - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `Queue` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | tem de ser definido como `queue`. Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure.|
|**direção** | n/d | tem de ser definido como `out`. Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a fila no código da função. Definido como `$return` para referenciar o valor de retorno da função.| 
|**queueName** |**QueueName** | O nome da fila. | 
|**ligação** | **Ligação** |O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação de armazenamento predefinida na definição de aplicação com o nome `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização
 
Em c# e c# script, gravar uma mensagem de fila única utilizando um parâmetro de método como `out T paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. Pode utilizar o tipo de retorno do método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um POCO serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Em c# e c# script, escreva vários de fila de mensagens através de um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Funções de JavaScript, utilizar `context.bindings.<name>` para aceder a mensagem da fila de saída. Pode utilizar uma cadeia ou um objeto JSON serializável para o payload de item de fila.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ir para um guia de introdução que utiliza um acionador de armazenamento de filas](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Ir para um tutorial que utiliza um armazenamento de filas de enlace de saída](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage