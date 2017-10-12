---
title: Receber eventos de Hubs de Eventos do Azure com o Framework .NET | Microsoft Docs
description: Siga este tutorial para receber eventos dos Hubs de Eventos do Azure com o Framework .NET.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 5d2f6f53af182a8ac0430de0ca3701a9a30e0bf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Receber eventos de Hubs de Eventos do Azure com o .NET Framework

## <a name="introduction"></a>Introdução

Os Event Hubs são um serviço que processa grandes quantidades de dados de eventos (telemetria) em aplicações e dispositivos ligados. Depois de recolher os dados para os Event Hubs, pode armazená-los utilizando um cluster de armazenamento ou transformá-los utilizando um fornecedor de análise em tempo real. Esta capacidade de processamento e recolha de eventos de grande escala é um componente importante das arquiteturas das aplicações modernas, incluindo a Internet das Coisas (IoT).

Este tutorial mostra como escrever uma aplicação de consola do .NET Framework que recebe mensagens de um hub de eventos com o **[Anfitrião do Processador de Eventos][EventProcessorHost]**. Para enviar eventos com o .NET Framework, veja o artigo [Enviar eventos para Hubs de Eventos do Azure com o .NET Framework](event-hubs-dotnet-framework-getstarted-send.md) ou clique no idioma de envio adequado no índice à esquerda.

O [Anfitrião do Processador de Eventos][EventProcessorHost] é uma classe do .NET que simplifica a receção de eventos provenientes dos hubs de eventos ao gerir pontos de verificação persistentes e receções em paralelo desses hubs de eventos. Se utilizar o [Anfitrião do Processador de Eventos][Event Processor Host], pode dividir eventos por múltiplos recetores, mesmo se estiverem alojados em nós diferentes. Este exemplo mostra como utilizar o [Anfitrião do Processador de Eventos][EventProcessorHost] para um único recetor. O exemplo de [Processamento de eventos aumentados horizontalmente][Scale out Event Processing with Event Hubs] mostra como utilizar o [Anfitrião do Processador de Eventos][EventProcessorHost] com múltiplos recetores.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* [Microsoft Visual Studio 2015 ou superior](http://visualstudio.com). As capturas de ecrã neste tutorial utilizam o Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma, pode criar uma conta gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um hub de eventos

O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento descrito [neste artigo](event-hubs-create.md) e, em seguida, continue com os passos seguintes deste tutorial.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Para utilizar o [Anfitrião do Processador de Eventos][EventProcessorHost], tem de ter uma [Conta de Armazenamento do Azure][Azure Storage account]:

1. Inicie sessão no [Portal do Azure][Azure portal] e clique em **Novo** na parte superior esquerda do ecrã.
2. Clique em **Armazenamento** e, em seguida, clique em **Conta de armazenamento**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. No painel **Criar conta de armazenamento**, escreva um nome para a conta de armazenamento. Escolha uma subscrição do Azure, um grupo de recursos e uma localização na que pretenda criar o recurso. Em seguida, clique em **Criar**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. Na lista de contas de armazenamento, clique na conta de armazenamento recentemente criada.
5. No painel da conta de armazenamento, clique em **Chaves de acesso**. Copie o valor da **chave1** para utilizar mais tarde neste tutorial.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

## <a name="create-a-receiver-console-application"></a>Criar uma aplicação de consola de recetor

1. No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Recetor**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
2. No Explorador de Soluções, clique com o botão direito do rato no projeto **Recetor** e, em seguida, clique em **Gerir Pacotes NuGet para Solução**.
3. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Clique em **Instalar** e aceite os termos de utilização.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    O Visual Studio permite transferir, instalar e adicionar uma referência ao [do Service Bus do Azure – Pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.
4. Clique com o botão direito do rato no projeto **Recetor**, clique em **Adicionar** e em **Classe**. Dê à nova classe o nome **SimpleEventProcessor** e, em seguida, clique em **Adicionar** para criar a classe.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
5. Adicione as seguintes declarações na parte superior do ficheiro SimpleEventProcessor.cs:
    
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
    
  Esta classe é chamada pelo **EventProcessorHost** para processar eventos recebidos do hub de eventos. A classe `SimpleEventProcessor` utiliza um cronómetro para chamar periodicamente o método de ponto de verificação no contexto do **EventProcessorHost**. Este processamento garante que, se o recetor for reiniciado, não perde mais do que cinco minutos de trabalho de processamento.
6. Na classe **Program**, adicione a seguinte declaração `using` na parte superior do ficheiro:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  ```
    
  Em seguida, substitua o método `Main` na classe `Program` pelo seguinte código, substituindo o nome do hub de eventos e a cadeia de ligação ao nível do espaço de nomes que guardou anteriormente e a conta de armazenamento e a chave que copiou nas secções anteriores. 
    
  ```csharp
  static void Main(string[] args)
  {
    string eventHubConnectionString = "{Event Hubs namespace connection string}";
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

7. Execute o programa e certifique-se de que não existem erros.
  
Parabéns! Recebeu agora mensagens de um hub de eventos com o Anfitrião do Processador de Eventos.


> [!NOTE]
> Este tutorial utiliza uma única instância do [EventProcessorHost][EventProcessorHost]. Para aumentar o débito, recomendamos que execute várias instâncias do [EventProcessorHost][EventProcessorHost], como mostrado no exemplo de [Processamento de eventos aumentados horizontalmente][Processamento de eventos aumentados horizontalmente]. Nesses casos, as várias instâncias coordenam-se automaticamente entre si para equilibrarem a carga dos eventos recebidos. Se pretender várias recetores para que cada um processe *todos* os eventos, terá de utilizar o conceito **ConsumerGroup**. Se receber eventos de vários computadores, poderá ser útil especificar os nomes das instâncias do [EventProcessorHost][EventProcessorHost] com base nos computadores (ou funções) em que estão implementadas. Para obter mais informações sobre estes tópicos, veja [Event Hubs overview][Event Hubs overview] (Descrição geral dos Hubs de Eventos) e [Event Hubs programming guide][Event Hubs Programming Guide] (Guia de programação dos Hubs de Eventos).
> 
> 

## <a name="next-steps"></a>Passos seguintes

Agora que compilou uma aplicação de trabalho que cria um hub de eventos e envia e recebe dados, pode saber mais ao visitar as seguintes ligações:

* [Anfitrião do Processador de Eventos][Event Processor Host]
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com
