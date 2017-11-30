---
title: "Enlaces de Service Bus do Azure para as funções do Azure"
description: "Compreenda como utilizar o Service Bus do Azure acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: tdykstra
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
ms.author: tdykstra
ms.openlocfilehash: 6d59b26fa4ab17c17827a8e3450e808e40e5c2dd
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Enlaces de Service Bus do Azure para as funções do Azure

Este artigo explica como trabalhar com os enlaces de Service Bus do Azure das funções do Azure. Funções do Azure suporta acionam e de saída enlaces para tópicos e filas do Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Acionador

Utilize o acionador de Service Bus para responder a mensagens a partir de uma fila do Service Bus ou tópico. 

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#trigger---c-example)
* [Script do c#](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Acionador - c# exemplo

O seguinte exemplo mostra um [pré-compilada c# função](functions-dotnet-class-library.md) que regista uma mensagem de fila do Service Bus.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um acionador de Service Bus enlace num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função regista uma mensagem de fila do Service Bus.

Segue-se os dados do enlace *function.json* ficheiro:

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

Eis o código de script do c#:

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---f-example"></a>Acionador - F # exemplo

O exemplo seguinte mostra um acionador de Service Bus enlace num *function.json* ficheiro e uma [F # função](functions-reference-fsharp.md) que utiliza o enlace. A função regista uma mensagem de fila do Service Bus. 

Segue-se os dados do enlace *function.json* ficheiro:

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

Eis o código de script F #:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um acionador de Service Bus enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função regista uma mensagem de fila do Service Bus. 

Segue-se os dados do enlace *function.json* ficheiro:

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

Eis o código de script de JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

## <a name="trigger---attributes"></a>Acionador - atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize os seguintes atributos para configurar um acionador de barramento de serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus)

  O construtor do atributo tem o nome da fila ou tópico e a subscrição. Também pode especificar os direitos de acesso da ligação. Se não especificar direitos de acesso, a predefinição é `Manage`. Como escolher os definição de direitos de acesso é explicado no [acionador - configuração](#trigger---configuration) secção. Eis um exemplo que mostra o atributo utilizado com um parâmetro de cadeia:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Pode definir o `Connection` propriedade para especificar a conta de barramento de serviço a utilizar, conforme mostrado no exemplo seguinte:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Para obter um exemplo completado, consulte [acionador - pré-compilada c# exemplo](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus)

  Outra forma para especificar a conta de barramento de serviço a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação do Service Bus. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo seguinte mostra o nível de classe e o nível de método:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

A conta de barramento de serviço a utilizar é determinada pela seguinte ordem:

* O `ServiceBusTrigger` do atributo `Connection` propriedade.
* O `ServiceBusAccount` atributo aplicado para o mesmo parâmetro como o `ServiceBusTrigger` atributo.
* O `ServiceBusAccount` atributo aplicado à função.
* O `ServiceBusAccount` atributo aplicado à classe.
* A definição de aplicação "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `ServiceBusTrigger` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definida para "serviceBusTrigger". Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definida para "em". Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a mensagem de fila ou um tópico no código da função. Definido como "$return" para o valor de retorno da função de referência. | 
|**queueName**|**QueueName**|Nome da fila para monitorizar.  Defina apenas se uma fila, não para um tópico de monitorização.
|**topicName**|**TopicName**|Nome do tópico para monitorizar. Defina apenas se a monitorização um tópico, não para uma fila.|
|**subscriptionName**|**SubscriptionName**|Nome da subscrição para monitorizar. Defina apenas se a monitorização um tópico, não para uma fila.|
|**ligação**|**Ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação de barramento de serviço a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome. Por exemplo, se definir `connection` para "MyServiceBus", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyServiceBus." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação do Service Bus predefinida na definição de aplicação com o nome "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos apresentados em [obter as credenciais de gestão](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico. |
|**accessRights**|**Acesso**|Direitos de acesso para a cadeia de ligação. Os valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se utilizar uma cadeia de ligação que não tenha o **gerir** conjunto de permissões, `accessRights` "escutar". Caso contrário, as funções de tempo de execução poderá falhar a tentar efetuar operações que exigem gerirem direitos.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

Em c# e c# script, aceder a mensagem de fila ou um tópico com um parâmetro de método como `string paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. Pode utilizar qualquer um dos seguintes tipos de, em vez de `string`:

* `byte[]`-Útil para dados binários.
* Um tipo personalizado - se a mensagem contém um JSON, as funções do Azure tentar anular a serialização os dados JSON.
* `BrokeredMessage`-Dá-lhe a mensagem de serialização anulada com o [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) método.

Em JavaScript, aceder a mensagem de fila ou um tópico utilizando `context.bindings.<name>`. `<name>`o valor especificado no `name` propriedade *function.json*. A mensagem do barramento de serviço é transmitida para a função como uma cadeia ou um objeto JSON.

## <a name="trigger---poison-messages"></a>Acionador - mensagens nocivas

Processamento de mensagens nocivas não pode ser controlado ou configurado nas funções do Azure. Barramento de serviço processa mensagens nocivas em si.

## <a name="trigger---peeklock-behavior"></a>Acionador - PeekLock comportamento

O tempo de execução de funções recebe uma mensagem na [PeekLock modo](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Chama `Complete` na mensagem se a função for concluído com êxito ou chamadas `Abandon` se falhar a função. Se a função é executada mais do que o `PeekLock` tempo limite, o bloqueio foi renovado automaticamente.

## <a name="trigger---hostjson-properties"></a>Acionador - host.json propriedades

O [host.json](functions-host-json.md#servicebus) ficheiro contém definições que controlam o comportamento de Acionador de Service Bus.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Saída

Utilize o enlace de saída do Service Bus do Azure para enviar mensagens de fila ou um tópico.

## <a name="output---example"></a>De saída - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#output---c-example)
* [Script do c#](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - c# exemplo

O seguinte exemplo mostra um [pré-compilada c# função](functions-dotnet-class-library.md) que envia uma mensagem de fila de barramento de serviço:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

O exemplo seguinte mostra uma saída de Service Bus enlace num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função utiliza um acionador de temporizador para enviar uma mensagem de fila cada 15 segundos.

Segue-se os dados do enlace *function.json* ficheiro:

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

Eis c# código de script que cria uma única mensagem:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

C# código de script Eis que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Saída - F # exemplo

O exemplo seguinte mostra uma saída de Service Bus enlace num *function.json* ficheiro e uma [função de script F #](functions-reference-fsharp.md) que utiliza o enlace. A função utiliza um acionador de temporizador para enviar uma mensagem de fila cada 15 segundos.

Segue-se os dados do enlace *function.json* ficheiro:

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

Eis o F # script código que cria uma única mensagem:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

O exemplo seguinte mostra uma saída de Service Bus enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função utiliza um acionador de temporizador para enviar uma mensagem de fila cada 15 segundos.

Segue-se os dados do enlace *function.json* ficheiro:

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

Eis o código de script de JavaScript que cria uma única mensagem:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Eis o código de script de JavaScript cria várias mensagens:

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

## <a name="output---attributes"></a>Saída - atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), que está definido no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

O construtor do atributo tem o nome da fila ou tópico e a subscrição. Também pode especificar os direitos de acesso da ligação. Como escolher os definição de direitos de acesso é explicado no [de saída - configuração](#output---configuration) secção. Eis um exemplo que mostra o atributo aplicado ao valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Pode definir o `Connection` propriedade para especificar a conta de barramento de serviço a utilizar, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Para obter um exemplo completado, consulte [resultado - pré-compilada c# exemplo](#output---c-example).

Pode utilizar o `ServiceBusAccount` atributo para especificar a conta de barramento de serviço a utilizar ao nível de classe, método ou parâmetro.  Para obter mais informações, consulte [acionador - atributos](#trigger---attributes-for-precompiled-c).

## <a name="output---configuration"></a>De saída - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `ServiceBus` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definida para "serviceBus". Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definida para "out". Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a fila ou um tópico no código da função. Definido como "$return" para o valor de retorno da função de referência. | 
|**queueName**|**QueueName**|Nome da fila.  Defina apenas se o envio de mensagens da fila, não para um tópico.
|**topicName**|**TopicName**|Nome do tópico para monitorizar. Defina apenas se o envio de mensagens de tópico, não para uma fila.|
|**subscriptionName**|**SubscriptionName**|Nome da subscrição para monitorizar. Defina apenas se o envio de mensagens de tópico, não para uma fila.|
|**ligação**|**Ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação de barramento de serviço a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome. Por exemplo, se definir `connection` para "MyServiceBus", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyServiceBus." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação do Service Bus predefinida na definição de aplicação com o nome "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos apresentados em [obter as credenciais de gestão](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico.|
|**accessRights**|**Acesso** |Direitos de acesso para a cadeia de ligação. Os valores disponíveis são "Gerir" e "escutar". A predefinição é "Gerir", que indica que a ligação tem **gerir** permissões. Se utilizar uma cadeia de ligação que não tenha **gerir** definir as permissões, `accessRights` "escutar". Caso contrário, as funções de tempo de execução poderá falhar a tentar efetuar operações que exigem gerirem direitos.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Em c# e c# script, aceder a fila ou um tópico utilizando um parâmetro de método como `out string paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. Pode utilizar qualquer um dos seguintes tipos de parâmetro:

* `out T paramName` - `T`pode ser qualquer tipo de JSON serializável. Se o valor do parâmetro é nulo quando a função sai, funções cria a mensagem com um objeto nulo.
* `out string`-Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out byte[]`-Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out BrokeredMessage`-Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.

Para criar várias mensagens num c# ou função de script do c#, pode utilizar `ICollector<T>` ou `IAsyncCollector<T>`. Uma mensagem é criada quando chamar o `Add` método.

Em JavaScript, aceder a fila ou um tópico utilizando `context.bindings.<name>`. `<name>`o valor especificado no `name` propriedade *function.json*. Pode atribuir um objeto de Javascript (anulada a serialização JSON), uma cadeia ou uma matriz de bytes a `context.binding.<name>`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
