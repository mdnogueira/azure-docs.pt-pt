---
title: "Enlace de funções Twilio do Azure | Microsoft Docs"
description: "Compreenda como utilizar os enlaces do Twilio com as funções do Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Enviar SMS mensagens de funções do Azure utilizando o Twilio vínculo de saída
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e utilizar enlaces do Twilio com as funções do Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Funções do Azure suporta enlaces para ativar as suas funções enviar mensagens de texto SMS com algumas linhas de código de saída do Twilio e um [Twilio](https://www.twilio.com/) conta. 

## <a name="functionjson-for-the-twilio-output-binding"></a>Function.JSON do Twilio vínculo de saída
O ficheiro de function.json fornece as seguintes propriedades:

|Propriedade  |Descrição  |
|---------|---------|
|**nome**| Nome da variável utilizado no código de função para a mensagem de texto SMS do Twilio. |
|**tipo**| tem de ser definido como `twilioSms`.|
|**accountSid**| Este valor tem de ser definido para o nome de uma definição de aplicação que contém o Sid de conta do Twilio.|
|**authToken**| Este valor tem de ser definido para o nome de uma definição de aplicação que contém o token de autenticação do Twilio.|
|**para**| Este valor é definido para o número de telefone que o texto SMS é enviado para.|
|**do**| Este valor é definido para o número de telefone que o texto SMS é enviado do.|
|**direção**| tem de ser definido como `out`.|
|**corpo**| Este valor pode ser utilizado rígido codificar a mensagem de texto SMS se não precisa de configurá-lo dinamicamente no código para a sua função. |

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


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Enlace de saída de exemplo c# fila acionador com Twilio
#### <a name="synchronous"></a>Síncrona
Este código de exemplo síncrona para um acionador de fila de armazenamento do Azure utiliza um parâmetro de saída para enviar uma mensagem de texto para um cliente que colocada uma ordem.

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

#### <a name="asynchronous"></a>Assíncrona
Este código de exemplo assíncrona para um acionador de fila de armazenamento do Azure envia uma mensagem de texto para um cliente que colocada uma ordem.

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

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Enlace de saída de Acionador de fila de Node.js de exemplo com Twilio
Neste exemplo de Node.js envia uma mensagem de texto para um cliente que colocada uma ordem.

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

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

