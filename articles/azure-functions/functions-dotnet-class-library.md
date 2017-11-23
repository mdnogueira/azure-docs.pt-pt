---
title: "Bibliotecas de classe do .NET a utilizar com as funções do Azure | Microsoft Docs"
description: "Saiba como criar bibliotecas de classe do .NET para utilização com as funções do Azure"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/10/2017
ms.author: glenga
ms.openlocfilehash: a856c3b04dc458c0f1e0017066c35edd743565fd
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="using-net-class-libraries-with-azure-functions"></a>Bibliotecas de classe do .NET a utilizar com as funções do Azure

Para além dos ficheiros de script, as funções do Azure suporta a publicação de uma biblioteca de classe como a implementação para uma ou mais funções. Recomendamos que utilize o [Azure funções Visual Studio 2017 ferramentas](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

## <a name="prerequisites"></a>Pré-requisitos 

Este artigo tem os seguintes pré-requisitos:

- [Visual Studio 2017 versão 15.3](https://www.visualstudio.com/vs/), ou uma versão posterior.
- Instalar o **programação do Azure** carga de trabalho.

## <a name="functions-class-library-project"></a>As funções de projeto da biblioteca de classe

A partir do Visual Studio, crie um novo projeto de funções do Azure. O novo modelo de projeto cria os ficheiros *host.json* e *local.settings.json*. Pode [personalizar definições de tempo de execução das funções do Azure no host.json](functions-host-json.md). 

O ficheiro *local.settings.json* armazena as definições de aplicação, cadeias de ligação e as definições para ferramentas de núcleos de funções do Azure. Para saber mais sobre a estrutura, consulte [código e testar as funções do Azure localmente](functions-run-local.md#local-settings-file).

### <a name="functionname-attribute"></a>Atributo de FunctionName

O atributo [ `FunctionNameAttribute` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) marca um método como um ponto de entrada da função. Tem de ser utilizado com exatamente um acionador e 0 ou mais argumentos de entrada e saída de enlaces.

### <a name="conversion-to-functionjson"></a>Conversão para function.json

Quando criar um projeto de funções do Azure, uma *function.json* é criado um ficheiro no diretório da função. O nome do diretório é o mesmo que a função de nome que o `[FunctionName]` atributo especifica. O *function.json* ficheiro contiver acionadores e enlaces e aponta para o ficheiro de assemblagem do projeto.

Esta conversão é realizado o pacote NuGet [Microsoft\.NET\.Sdk\.funções](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). A origem está disponível no repositório GitHub [azure\-funções\-vs\-criar\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="triggers-and-bindings"></a>Acionadores e enlaces 

A tabela seguinte lista os acionadores e enlaces que estão disponíveis no projeto da biblioteca de classe das funções do Azure. Todos os atributos estão no espaço de nomes `Microsoft.Azure.WebJobs`.

| Vínculo | Atributo | Pacote NuGet |
|------   | ------    | ------        |
| [Acionador de armazenamento de BLOBs, entrada, saída](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [O blob storage] |
| [Acionador cosmos DB](#cosmos-db) | [CosmosDBTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [BD do cosmos entrada e saída](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] |
| [Acionador de Hubs de eventos e de saída](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [Entrada de ficheiro externo e de saída](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [Acionador HTTP e o webhook](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [As Mobile Apps entrada e saída](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Saída de Hubs de notificação](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Acionador de armazenamento de filas e de saída](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Saída do SendGrid](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Acionador de Service Bus e de saída](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Tabela armazenamento entrada e saída](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Acionador de temporizador](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Saída do Twilio](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-bindings-and-output-bindings"></a>Acionamento do armazenamento de BLOBs, enlaces de entrada e saída enlaces

Acionamento de suporta funções do Azure, de entrada e saída enlaces para o Blob storage do Azure. Para obter mais informações sobre as expressões de enlace e de metadados, consulte [enlaces de armazenamento de BLOBs de funções do Azure](functions-bindings-storage-blob.md).

Um acionador de blob está definido com o `[BlobTrigger]` atributo. Pode utilizar o atributo `[StorageAccount]` para definir o nome da definição de aplicação que contenha a cadeia de ligação para a conta de armazenamento que é utilizada por uma função completa ou a classe.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob de entrada e saída estão definidos utilizando o `[Blob]` atributo along com um `FileAccess` com a indicação de parâmetro de leitura ou escrita. O exemplo seguinte utiliza um blob acionador e blob de saída do enlace.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-trigger-input-bindings-and-output-bindings"></a>Acionamento do cosmos DB, enlaces de entrada e saída enlaces

As funções do Azure suporta acionadores e entrada e saída enlaces de BD do Cosmos. Para saber mais sobre as funcionalidades do enlace Cosmos DB, consulte [enlaces de base de dados do Azure funções Cosmos](functions-bindings-documentdb.md).

Para acionar a partir de um documento do Cosmos DB, utilize o atributo `[CosmosDBTrigger]` no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. Os acionadores de exemplo seguintes do específico `database` e `collection`. A definição `myCosmosDB` contém a ligação à instância de base de dados do Cosmos. 

```csharp
[FunctionName("DocumentUpdates")]
public static void Run(
    [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
IReadOnlyList<Document> documents, TraceWriter log)
{
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
}
```

Para vincular a um documento do Cosmos DB, utilize o atributo `[DocumentDB]` no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. O exemplo seguinte tem um acionador de fila e uma API de DocumentDB vínculo de saída.

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Acionador de Hubs de eventos e de saída

Funções do Azure suporta acionam e de saída vínculos para os Event Hubs. Para obter mais informações, consulte [enlaces de Hub de eventos de funções do Azure](functions-bindings-event-hubs.md).

Os tipos de `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` e `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` são definidos no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

O exemplo seguinte utiliza um acionador de Hub de eventos:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

O exemplo seguinte tem um Hub de eventos de saída, o valor de retorno do método a utilizar como o resultado:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>Entrada de ficheiro externo e de saída

As funções do Azure suporta acionador, de entrada e de saída enlaces para ficheiros externos, como o Google Drive, Dropbox e OneDrive. Para obter mais informações, consulte [enlaces de ficheiro externo de funções do Azure](functions-bindings-external-file.md). Os atributos `[ExternalFileTrigger]` e `[ExternalFile]` são definidos no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.ApiHub].

O exemplo do c# seguinte demonstra um ficheiro externo entrada e saída de enlace. O código copia o ficheiro de entrada para o ficheiro de saída.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP e webhooks

Utilize o `HttpTrigger` atributo para definir um acionador HTTP ou o webhook. Este atributo está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Http]. Pode personalizar o nível de autorização, tipo de webhook, rota e métodos. O exemplo seguinte define um acionador HTTP com a autenticação anónima e _genericJson_ webhook tipo.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>As Mobile Apps entrada e saída

Funções do Azure suporta entrada e saída enlaces para Mobile Apps. Para obter mais informações, consulte [enlaces de Mobile Apps funções do Azure](functions-bindings-mobile-apps.md). O atributo `[MobileTable]` está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps].

O exemplo seguinte demonstra um Mobile Apps vínculo de saída:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Saída de Hubs de notificação

As funções do Azure suporta um enlace de saída para os Notification Hubs. Para obter mais informações, consulte [vínculo de saída do Hub de notificação de funções do Azure](functions-bindings-notification-hubs.md). O atributo `[NotificationHub]` está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs].

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Acionador de armazenamento de filas e de saída

Funções do Azure suporta acionam e de saída enlaces para as filas do Azure. Para obter mais informações, consulte [enlaces de armazenamento de filas do Azure funções](functions-bindings-storage-queue.md).

O exemplo seguinte mostra como utilizar o tipo de retorno da função com uma saída da fila de enlace, utilizando o `[Queue]` atributo. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}

```

Para definir um acionador de fila, utilize o `[QueueTrigger]` atributo.
```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```


<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>Saída do SendGrid

As funções do Azure suporta uma saída de SendGrid enlace para enviar correio eletrónico através de programação. Para obter mais informações, consulte [SendGrid de funções do Azure enlaces](functions-bindings-sendgrid.md).

O atributo `[SendGrid]` está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid]. Um enlace de SendGrid requer uma definição com o nome da aplicação `AzureWebJobsSendGridApiKey`, que contém a chave de API do SendGrid. Este é o nome da definição predefinida para a sua chave de API do SendGrid. Se precisar de ter SendGrid mais do que uma chave ou escolha um nome de uma definição diferente, pode definir este nome a utilizar o `ApiKey` propriedade o `SendGrid` atributo de enlace, como no exemplo seguinte:

    [SendGrid(ApiKey = "MyCustomSendGridKeyName")]

Segue-se um exemplo de utilização de um acionador de fila do Service Bus e um através de enlace de saída do SendGrid `SendGridMessage`:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
Tenha em atenção que este exemplo requer que a chave de API do SendGrid seja armazenamento numa aplicação definição denominada `AzureWebJobsSendGridApiKey`.

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Acionador de Service Bus e de saída

Funções do Azure suporta acionam e de saída enlaces para tópicos e filas do Service Bus. Para obter mais informações sobre como configurar enlaces, consulte [enlaces de funções do Azure Service Bus](functions-bindings-service-bus.md).

Os atributos `[ServiceBusTrigger]` e `[ServiceBus]` são definidos no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

Segue-se um exemplo de um acionador de fila de barramento de serviço:

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

Segue-se um exemplo de uma saída de barramento de serviço de enlace, o tipo de retorno do método a utilizar como o resultado:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Tabela armazenamento entrada e saída

Funções do Azure suporta entrada e saída os enlaces do Table storage do Azure. Para obter mais informações, consulte [enlaces de armazenamento de tabelas de funções do Azure](functions-bindings-storage-table.md).

O exemplo seguinte é uma classe com duas funções, que demonstra a saída de armazenamento de tabela e os enlaces de entrada. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>Acionador de temporizador

As funções do Azure tem um vínculo de Acionador de temporizador que lhe permite executar o código de função com base numa agenda definida. Para saber mais sobre as funcionalidades do enlace, consulte [agendar a execução do código com as funções do Azure](functions-bindings-timer.md).

No plano de consumo, pode definir agendas com um [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression). Se estiver a utilizar um plano do App Service, também pode utilizar uma cadeia de TimeSpan. 

O exemplo seguinte define um acionador de temporizador que é executada a cada cinco minutos:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Saída do Twilio

Funções do Azure suporta Twilio saída enlaces para ativar as suas funções enviar mensagens de texto SMS. Para obter mais informações, consulte [vínculo de saída de mensagens de SMS enviar de funções do Azure utilizando o Twilio](functions-bindings-twilio.md). 

O atributo `[TwilioSms]` está definido no pacote [Microsoft.Azure.WebJobs.Extensions.Twilio].

O exemplo do c# seguinte utiliza uma fila acionador e um Twilio vínculo de saída:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar as funções do Azure em c# scripting, consulte [C de funções do Azure\# referência para programadores de scripts](functions-reference-csharp.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta4
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[CosmosDBTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
