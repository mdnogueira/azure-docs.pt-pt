---
title: Receber eventos de Hubs de Eventos do Azure com o Framework .NET | Microsoft Docs
description: Siga este tutorial para receber eventos dos Hubs de Eventos do Azure com o Framework .NET.
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: fe331199333d492dbc42c9125c9da96a44066ee1
ms.openlocfilehash: 4a3656d09f216e5c895bac5d7d680b44d76f4df8


---
# <a name="get-started-with-event-hubs-using-the-net-framework"></a>Introdução aos Hubs de Eventos com o Framework .NET

## <a name="receive-messages-with-eventprocessorhost"></a>Receber mensagens com o EventProcessorHost
O [EventProcessorHost][EventProcessorHost] é uma classe do .NET que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo desses Hubs de Eventos. Se utilizar o [EventProcessorHost][EventProcessorHost], pode dividir eventos por vários recetores, mesmo se estiverem alojados em nós diferentes. Este exemplo mostra como utilizar o [EventProcessorHost][EventProcessorHost] para um recetor único. O exemplo de [Processamento de eventos aumentados horizontalmente][Scale out Event Processing with Event Hubs] mostra como utilizar o [EventProcessorHost][EventProcessorHost] com vários recetores.

Para utilizar o [EventProcessorHost][EventProcessorHost], tem de ter uma [Conta de Armazenamento do Azure][Azure Storage account]:

1. Inicie sessão no [Portal do Azure][Azure portal] e clique em **Novo** na parte superior esquerda do ecrã.
2. Clique em **Armazenamento** e, em seguida, clique em **Conta de armazenamento**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. No painel **Criar conta de armazenamento**, escreva um nome para a conta de armazenamento. Escolha uma subscrição do Azure, um grupo de recursos e uma localização na que pretenda criar o recurso. Em seguida, clique em **Criar**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. Na lista de contas de armazenamento, clique na conta de armazenamento recentemente criada.
5. No painel da conta de armazenamento, clique em **Chaves de acesso**. Copie o valor da **chave1** para utilizar mais tarde neste tutorial.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)
6. No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Recetor**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
7. No Explorador de Soluções, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet para Solução**.
8. Clique no separador **Procurar** e procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Verifique se o nome do projeto (**Recetor**) está especificado na caixa **Versões**. Clique em **Instalar** e aceite os termos de utilização.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    O Visual Studio permite transferir, instalar e adicionar uma referência ao [do Service Bus do Azure – Pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.
9. Clique com o botão direito do rato no projeto **Recetor**, clique em **Adicionar** e em **Classe**. Dê à nova classe o nome **SimpleEventProcessor** e, em seguida, clique em **Adicionar** para criar a classe.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
10. Adicione as seguintes declarações na parte superior do ficheiro SimpleEventProcessor.cs:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     using System.Diagnostics;
     ```
    
     Em seguida, substitua o seguinte código do corpo da classe:
    
     ```csharp
     class SimpleEventProcessor : IEventProcessor
     {
         Stopwatch checkpointStopWatch;
    
         async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
         {
             Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
             if (reason == CloseReason.Shutdown)
             {
                 await context.CheckpointAsync();
             }
         }
    
         Task IEventProcessor.OpenAsync(PartitionContext context)
         {
             Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
             this.checkpointStopWatch = new Stopwatch();
             this.checkpointStopWatch.Start();
             return Task.FromResult<object>(null);
         }
    
         async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
         {
             foreach (EventData eventData in messages)
             {
                 string data = Encoding.UTF8.GetString(eventData.GetBytes());
    
                 Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                     context.Lease.PartitionId, data));
             }
    
             //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
             if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
             {
                 await context.CheckpointAsync();
                 this.checkpointStopWatch.Restart();
             }
         }
     }
     ```
    
     Esta classe será chamada pelo **EventProcessorHost** para processar eventos recebidos do Hub de Eventos. Tenha em atenção que a classe `SimpleEventProcessor` utiliza um cronómetro para chamar periodicamente o método de ponto de verificação no contexto do **EventProcessorHost**. Esta ação garante que, se o recetor for reiniciado, não irá perder mais do que cinco minutos de trabalho de processamento.
11. Na classe **Program**, adicione a seguinte declaração `using` na parte superior do ficheiro:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     ```
    
     Em seguida, substitua o método `Main` na classe `Program` pelo seguinte código, substituindo o nome do Hub de Eventos e a cadeia de ligação ao nível do espaço de nomes que guardou anteriormente e a conta de armazenamento e a chave que copiou nas secções anteriores. 
    
     ```csharp
     static void Main(string[] args)
     {
       string eventHubConnectionString = "{Event Hub connection string}";
       string eventHubName = "{Event Hub name}";
       string storageAccountName = "{storage account name}";
       string storageAccountKey = "{storage account key}";
       string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
    
       string eventProcessorHostName = Guid.NewGuid().ToString();
       EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
       Console.WriteLine("Registering EventProcessor...");
       var options = new EventProcessorOptions();
       options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
       eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
    
       Console.WriteLine("Receiving. Press enter key to stop worker.");
       Console.ReadLine();
       eventProcessorHost.UnregisterEventProcessorAsync().Wait();
     }
     ```

> [!NOTE]
> Este tutorial utiliza uma única instância do [EventProcessorHost][EventProcessorHost]. Para aumentar o débito, recomendamos que execute várias instâncias do [EventProcessorHost][EventProcessorHost], como mostrado no exemplo de [Processamento de eventos aumentados horizontalmente][Processamento de eventos aumentados horizontalmente]. Nesses casos, as várias instâncias coordenam-se automaticamente entre si para equilibrarem a carga dos eventos recebidos. Se pretender várias recetores para que cada um processe *todos* os eventos, terá de utilizar o conceito **ConsumerGroup**. Se receber eventos de vários computadores, poderá ser útil especificar os nomes das instâncias do [EventProcessorHost][EventProcessorHost] com base nos computadores (ou funções) em que estão implementadas. Para obter mais informações sobre estes tópicos, veja [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)][Event Hubs Overview] e [Event Hubs Programming Guide (Guia de Programação dos Hubs de Eventos)][Event Hubs Programming Guide].
> 
> 

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Passos seguintes
Agora que compilou uma aplicação de trabalho que cria um Hub de Eventos e envia e recebe dados, pode saber mais ao visitar as seguintes ligações:

* [Anfitrião do Processador de Eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3


<!--HONumber=Feb17_HO1-->


