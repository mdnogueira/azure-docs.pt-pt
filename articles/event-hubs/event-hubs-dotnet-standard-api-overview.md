---
title: "Descrição geral sobre os Event Hubs do Azure APIs padrão do .NET | Microsoft Docs"
description: "Descrição geral de API padrão do .NET"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: eea682c40cd415b383a8b2f0004a5f3648e2f01f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-net-standard-api-overview"></a>Descrição geral dos Event Hubs .NET API padrão
Este artigo resume algumas da chave de Event Hubs .NET padrão APIs do cliente. Não existem atualmente duas bibliotecas de cliente .NET padrão:
* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
  *  Esta biblioteca fornece todas as operações de tempo de execução básico.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
  * Esta biblioteca adiciona funcionalidades adicionais que permite para controlar eventos processados e é a forma mais fácil de ler a partir de um hub de eventos.

## <a name="event-hubs-client"></a>Cliente de Hubs de eventos
[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) é o objeto principal Utilize para enviar eventos, crie recetores e para obter informações de tempo de execução. Este cliente está ligado a um hub de eventos específico e cria uma nova ligação ao ponto final do Event Hubs.

### <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs
Um [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) objeto é criado a partir de uma cadeia de ligação. A forma mais simples para instanciar um novo cliente é mostrada no exemplo seguinte:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hubs connection string}");
```

Para editar programaticamente a cadeia de ligação, pode utilizar o [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) classe e passar a cadeia de ligação como um parâmetro para [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hubs connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Enviar eventos
Para enviar eventos para um hub de eventos, utilize o [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) classe. O corpo tem de ser um `byte` matriz, ou um `byte` segmento de matriz.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Receber eventos
O modo recomendado para receber eventos provenientes dos Hubs de eventos está a utilizar o [anfitrião do processador de eventos](#event-processor-host-apis), que fornece funcionalidade para automaticamente controlar o desvio e informações da partição. No entanto, existem determinadas situações em que poderá querer utilizar a flexibilidade de biblioteca principal do Event Hubs para receber eventos.

#### <a name="create-a-receiver"></a>Criar um recetor
Recetores estão associadas a partições específicas, por isso, para poder receber todos os eventos num hub de eventos, terá de criar várias instâncias. Um modo geral, é uma boa prática para obter as informações de partição x509securitytokenparameters, em vez de pré-programar os ids de partição. Para tal, pode utilizar o [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) método.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition ids
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Uma vez que os eventos nunca são removidos de um hub de eventos (e apenas expirarem), tem de especificar o ponto de partida adequado. O exemplo seguinte mostra as combinações possíveis.

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Consumir um evento
```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>APIs de anfitrião do processador de eventos
Estas APIs fornecem resiliência em processos de trabalho que poderão ficar indisponíveis, por distribuição partições em trabalhadores disponíveis.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes of the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Segue-se uma implementação de exemplo do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [O que é Event Hubs do Azure?](event-hubs-what-is-event-hubs.md)
* [Apis de Hubs de eventos disponível](event-hubs-api-overview.md)

As referências de .NET API são aqui:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)