---
title: "Enlace de funções Notification Hub do Azure | Microsoft Docs"
description: "Compreenda como utilizar o enlace do Notification Hub do Azure das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Enlace de saída do Hub de notificação de funções do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e enlaces de Hub de notificação do Azure de código das funções do Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

As suas funções podem enviar notificações push através de um Hub de notificação do Azure configurada com poucas linhas de código. No entanto, o Hub de notificação do Azure tem de ser configurado para a plataforma de notificações de serviços (PNS) que pretende utilizar. Para obter mais informações sobre a configuração de um Hub de notificação do Azure e desenvolver um aplicações de cliente que registados para receber notificações, consulte [introdução aos Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e clique em sua plataforma de cliente de destino na parte superior.

As notificações que envia podem ser notificações nativas ou notificações de modelo. Notificações nativas uma plataforma de notificação específicos de destino, conforme configurado no `platform` propriedade do enlace de saída. Uma notificação de modelo pode ser utilizada para várias plataformas de destino.   

## <a name="notification-hub-output-binding-properties"></a>Propriedades de enlace de saída de hub de notificação
O ficheiro de function.json fornece as seguintes propriedades:


|Propriedade  |Descrição  |
|---------|---------|
|**nome** | Nome da variável utilizado no código de função para a mensagem de hub de notificação. |
|**tipo** | tem de ser definido como `notificationHub`. |
|**tagExpression** | Expressões de etiqueta permitem-lhe especificar que possível entregar notificações de um conjunto de dispositivos que tenha registado para receber as notificações que corresponde à expressão tag.  Para obter mais informações, consulte [expressões de encaminhamento e etiqueta](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | Nome do recurso de hub de notificação no portal do Azure. |
|**ligação** | Esta cadeia de ligação tem de ser um **definição da aplicação** cadeia de ligação se definido como o *DefaultFullSharedAccessSignature* valor para o seu hub de notificação. |
|**direção** | tem de ser definido como `out`. | 
|**plataforma** | A propriedade da plataforma indica a plataforma de notificação os destinos de notificação. Por predefinição, se a propriedade de plataforma é omitida do enlace de saída, as notificações de modelo podem ser utilizadas para qualquer plataforma configurada no Hub de notificação do Azure de destino. Para obter mais informações sobre a utilização de modelos em geral para enviar cruzada notificações de plataforma com um Hub de notificação do Azure, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Se estiver definida, _plataforma_ tem de ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Serviço Apple Push Notification. Para obter mais informações sobre configurar o notification hub para APNS e receber a notificação na aplicação cliente, consulte [enviar notificações push para o iOS com Notification Hubs do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obter mais informações sobre configurar o notification hub para ADM e receber a notificação numa aplicação Kindle, consulte [introdução aos Notification Hubs para aplicações Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, que é a nova versão do GCM, também é suportada. Para obter mais informações, consulte [enviar notificações push para o Android com Notification Hubs do Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Serviços de notificação Push do Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) direcionada para plataformas Windows. Windows Phone 8.1 e posterior também é suportada pelo WNS. Para obter mais informações, consulte [introdução aos Notification Hubs para aplicações universais do Windows plataforma](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Serviço de notificação Push da Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Esta plataforma suporta o Windows Phone 8 e plataformas do Windows Phone anteriores. Para obter mais informações, consulte [enviar notificações de push com os Hubs de notificação do Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

Function.json de exemplo:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Configuração de cadeia de ligação de hub de notificação
Para utilizar uma saída de hub de notificação de enlace, tem de configurar a cadeia de ligação do hub. Pode selecionar um hub de notificação existente ou criar um direito de um novo do *integrar* separador na sua função. Também pode configurar a cadeia de ligação manualmente. 

Para configurar a cadeia de ligação para um hub de notificação existente:

1. Navegue para o seu hub de notificação no [portal do Azure](https://portal.azure.com), escolha **políticas de acesso**e selecione o botão Copiar junto a **DefaultFullSharedAccessSignature** política. Esta ação copia a cadeia de ligação para o *DefaultFullSharedAccessSignature* política para o notification hub. Esta cadeia de ligação fornece a função de permissão de acesso para enviar mensagens de notificação. 
    ![Copie a cadeia de ligação do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue até à sua aplicação de função no portal do Azure, escolha **definições da aplicação**, adicionar uma chave como `MyHubConnectionString`, cole o copiados *DefaultFullSharedAccessSignature* para o seu hub de notificação como o valor e, em seguida, clique em **guardar**.

Agora, pode utilizar esta definição de aplicação com nome que define a ligação do hub de notificação no enlace de saída.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>Notificações nativas do APNS com os acionadores de filas c#
Este exemplo mostra como utilizar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de APNS nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notificações nativas do GCM com os acionadores de filas c#
Este exemplo mostra como utilizar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de GCM nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>Notificações nativas do WNS com os acionadores de filas c#
Este exemplo mostra como utilizar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de alerta do WNS nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Exemplo de modelo para acionadores de temporizador do Node.js
Neste exemplo envia uma notificação um [registo modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contenha `location` e `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>Exemplo de modelo para o F # temporizador acionadores
Neste exemplo envia uma notificação um [registo modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contenha `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>Exemplo de modelo com um parâmetro de saída
Neste exemplo envia uma notificação um [registo modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contenha um `message` marcador de posição no modelo.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>Exemplo de modelo com função assíncrona
Se estiver a utilizar código assíncrono, parâmetros de saída não são permitidas. Neste caso, utilize `IAsyncCollector` para devolver a notificação de modelo. O código seguinte é um exemplo de código acima assíncrono. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>Utilizar o JSON de exemplo de modelo
Neste exemplo envia uma notificação um [registo modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contenha um `message` marcador de posição no modelo de utilização de uma cadeia JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Exemplo de modelo através de tipos de biblioteca de Hubs de notificação
Este exemplo mostra como utilizar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

