---
title: "Enlaces de Hubs de notificação para as funções do Azure"
description: "Compreenda como utilizar o enlace do Notification Hub do Azure das funções do Azure."
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
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: fd5ff8878e6afe95bd620bd9d1910add6dd92f3f
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Hubs de notificação de saída do enlace para as funções do Azure

Este artigo explica como enviar notificações push utilizando [Notification Hubs do Azure](../notification-hubs/notification-hubs-push-notification-overview.md) enlaces de funções do Azure. Azure funções suporta saída enlaces de para os Notification Hubs.

Os Hubs de notificação do Azure tem de ser configurados para a plataforma de notificações de serviço (PNS) que pretende utilizar. Para saber como obter notificações push na sua aplicação de cliente a partir de Hubs de notificação, consulte [introdução aos Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e selecionar a plataforma de cliente de destino na lista pendente junto ao topo da página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>Exemplo - modelo

As notificações que envia podem ser notificações nativas ou [notificações de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Notificações nativas uma plataforma de cliente específicos de destino, conforme configurado no `platform` propriedade do enlace de saída. Uma notificação de modelo pode ser utilizada para várias plataformas de destino.   

Veja o exemplo de específicas do idioma:

* [Script do c# - parâmetro out](#c-script-template-example---out-parameter)
* [Script do c# - assíncrona](#c-script-template-example---asynchronous)
* [Script do c# - JSON](#c-script-template-example---json)
* [Script do c# - tipos de biblioteca](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# exemplo de modelo de script - parâmetro out

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

### <a name="c-script-template-example---asynchronous"></a>C# modelo exemplo de script - assíncrono

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

### <a name="c-script-template-example---json"></a>C# exemplo de modelo script - JSON

Neste exemplo envia uma notificação um [registo modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contenha um `message` marcador de posição no modelo de utilização de uma cadeia JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# exemplo de modelo script - tipos de biblioteca

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

### <a name="f-template-example"></a>Exemplo de modelo F #

Neste exemplo envia uma notificação um [registo modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contenha `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exemplo de modelo de JavaScript

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

## <a name="example---apns-native"></a>Exemplo - APNS nativo

Este exemplo de script do c# mostra como enviar uma notificação de APNS nativa. 

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

## <a name="example---gcm-native"></a>Exemplo - GCM nativo

Este exemplo de script do c# mostra como enviar uma notificação de GCM nativa. 

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

## <a name="example---wns-native"></a>Exemplo - WNS nativo

Este exemplo de script do c# mostra como utilizar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de alerta do WNS nativa. 

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

## <a name="attributes"></a>Atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) atributo, que está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs).

O atributo os parâmetros do construtor e as propriedades descritas o [configuração](#configuration) secção.

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `NotificationHub` atributo:

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** |n/d| Tem de ser definida para "notificationHub". |
|**direção** |n/d| Tem de ser definida para "out". | 
|**nome** |n/d| Nome da variável utilizado no código de função para a mensagem de hub de notificação. |
|**tagExpression** |**TagExpression** | Expressões de etiqueta permitem-lhe especificar que possível entregar notificações de um conjunto de dispositivos que registados para receber as notificações que corresponde à expressão tag.  Para obter mais informações, consulte [expressões de encaminhamento e etiqueta](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nome do recurso de hub de notificação no portal do Azure. |
|**ligação** | **ConnectionStringSetting** | O nome de uma definição de aplicação que contém uma cadeia de ligação de Notification Hubs.  A cadeia de ligação tem de ser definida o *DefaultFullSharedAccessSignature* valor para o seu hub de notificação. Consulte [a configuração de cadeia de ligação](#connection-string-setup) posteriormente neste artigo.|
|**plataforma** | **Plataforma** | A propriedade da plataforma indica a plataforma de cliente os destinos de notificação. Por predefinição, se a propriedade de plataforma é omitida do enlace de saída, as notificações de modelo podem ser utilizadas para qualquer plataforma configurada no Hub de notificação do Azure de destino. Para obter mais informações sobre a utilização de modelos em geral para enviar cruzada notificações de plataforma com um Hub de notificação do Azure, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Se estiver definida, **plataforma** tem de ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Serviço Apple Push Notification. Para obter mais informações sobre configurar o notification hub para APNS e receber a notificação na aplicação cliente, consulte [enviar notificações push para o iOS com Notification Hubs do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obter mais informações sobre configurar o notification hub para ADM e receber a notificação numa aplicação Kindle, consulte [introdução aos Notification Hubs para aplicações Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, que é a nova versão do GCM, também é suportada. Para obter mais informações, consulte [enviar notificações push para o Android com Notification Hubs do Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Serviços de notificação Push do Windows](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) direcionada para plataformas Windows. Windows Phone 8.1 e posterior também é suportada pelo WNS. Para obter mais informações, consulte [introdução aos Notification Hubs para aplicações universais do Windows plataforma](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Serviço de notificação Push da Microsoft](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Esta plataforma suporta o Windows Phone 8 e plataformas do Windows Phone anteriores. Para obter mais informações, consulte [enviar notificações de push com os Hubs de notificação do Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exemplo de ficheiro Function.JSON

Eis um exemplo de um enlace de Notification Hubs num *function.json* ficheiro.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Configuração de cadeia de ligação

Para utilizar uma saída de hub de notificação de enlace, tem de configurar a cadeia de ligação do hub. Pode selecionar um hub de notificação existente ou criar um direito de um novo do *integrar* separador no portal do Azure. Também pode configurar a cadeia de ligação manualmente. 

Para configurar a cadeia de ligação para um hub de notificação existente:

1. Navegue para o seu hub de notificação no [portal do Azure](https://portal.azure.com), escolha **políticas de acesso**e selecione o botão Copiar junto a **DefaultFullSharedAccessSignature** política. Esta ação copia a cadeia de ligação para o *DefaultFullSharedAccessSignature* política para o notification hub. Esta cadeia de ligação permite que a função enviar mensagens de notificação ao hub.
    ![Copie a cadeia de ligação do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue até à sua aplicação de função no portal do Azure, escolha **definições da aplicação**, adicionar uma chave como **MyHubConnectionString**, cole o copiados *DefaultFullSharedAccessSignature* para o seu hub de notificação, como o valor e, em seguida, clique em **guardar**.

O nome desta definição de aplicação é que fica localizado na definição de ligação de enlace de saída no *function.json* ou o atributo de .NET. Consulte o [secção de configuração](#configuration) anteriormente neste artigo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

