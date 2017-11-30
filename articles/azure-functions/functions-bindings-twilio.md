---
title: "Enlace de funções Twilio do Azure"
description: "Compreenda como utilizar os enlaces do Twilio com as funções do Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae97045c27f3ad8b62e7798b2060ea59ccd66ac5
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="twilio-binding-for-azure-functions"></a>Enlace Twilio para as funções do Azure

Este artigo explica como enviar mensagens de texto com [Twilio](https://www.twilio.com/) enlaces de funções do Azure. Azure funções suporta saída os enlaces do Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#c-example)
* [Script do c#](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Exemplo do c#

O seguinte exemplo mostra um [pré-compilada c# função](functions-dotnet-class-library.md) que envia uma mensagem de texto quando é acionada por uma mensagem de fila.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Este exemplo utiliza o `TwilioSms` atributo com o valor de retorno do método. Uma alternativa consiste em utilizar o atributo com um `out SMSMessage` parâmetro ou um `ICollector<SMSMessage>` ou `IAsyncCollector<SMSMessage>` parâmetro.

### <a name="c-script-example"></a>Exemplo de script do c#

O exemplo seguinte mostra uma saída de Twilio enlace num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função utiliza um `out` parâmetro para enviar uma mensagem de texto.

Segue-se os dados do enlace *function.json* ficheiro:

Function.json de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Eis o código de script do c#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Não é possível utilizar parâmetros no código assíncrono de saída. Eis um assíncrono c# script exemplo de código:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo seguinte mostra uma saída de Twilio enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace.

Segue-se os dados do enlace *function.json* ficheiro:

Function.json de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) atributo, que está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio).

Para obter informações sobre as propriedades de atributo que pode configurar, consulte [configuração](#configuration). Eis um `TwilioSms` exemplo de atributo na assinatura do método:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(
    AccountSidSetting = "TwilioAccountSid", 
    AuthTokenSetting = "TwilioAuthToken", 
    From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    ...
}
 ```

Para obter um exemplo completado, consulte [PRÉ-COMPILADA c# exemplo](#c-example).

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `TwilioSms` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**|| tem de ser definido como `twilioSms`.|
|**direção**|| tem de ser definido como `out`.|
|**nome**|| Nome da variável utilizado no código de função para a mensagem de texto SMS do Twilio. |
|**accountSid**|**AccountSid**| Este valor tem de ser definido para o nome de uma definição de aplicação que contém o Sid de conta do Twilio.|
|**authToken**|**AuthToken**| Este valor tem de ser definido para o nome de uma definição de aplicação que contém o token de autenticação do Twilio.|
|**para**|**Para**| Este valor é definido para o número de telefone que o texto SMS é enviado para.|
|**do**|**Do**| Este valor é definido para o número de telefone que o texto SMS é enviado do.|
|**corpo**|**Corpo**| Este valor pode ser utilizado rígido codificar a mensagem de texto SMS se não precisa de configurá-lo dinamicamente no código para a sua função. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)


