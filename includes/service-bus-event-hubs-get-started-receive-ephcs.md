## <a name="receive-messages-with-eventprocessorhost"></a>Receber mensagens com o EventProcessorHost
O [EventProcessorHost][EventProcessorHost] é uma classe do .NET que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo desses Hubs de Eventos. Se utilizar o [EventProcessorHost][EventProcessorHost], pode dividir eventos por vários recetores, mesmo se estiverem alojados em nós diferentes. Este exemplo mostra como utilizar o [EventProcessorHost][EventProcessorHost] para um recetor único. O exemplo de [Processamento de eventos aumentados horizontalmente][Processamento de eventos aumentados horizontalmente] mostra como utilizar o [EventProcessorHost][EventProcessorHost] com vários recetores.

Para utilizar o [EventProcessorHost][EventProcessorHost], tem de ter uma [Conta de armazenamento do Azure][Conta de armazenamento do Azure]:

1. Inicie sessão no [Portal do Azure][Portal do Azure] e clique em **Novo** na parte superior esquerda do ecrã.
2. Clique em **Dados + Armazenamento** e, em seguida, clique em **Conta de armazenamento**.
   
    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)
3. No painel **Criar conta de armazenamento**, escreva um nome para a conta de armazenamento. Escolha uma subscrição do Azure, um grupo de recursos e uma localização na que pretenda criar o recurso. Em seguida, clique em **Criar**.
   
    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)
4. Na lista de contas de armazenamento, clique na conta de armazenamento recentemente criada.
5. No painel da conta de armazenamento, clique em **Chaves de acesso**. Copie o valor da **chave1** para utilizar mais tarde neste tutorial.
   
    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)
6. No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Recetor**.
   
    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)
7. No Explorador de Soluções, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet para Solução**.
8. Clique no separador **Procurar** e procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Verifique se o nome do projeto (**Recetor**) está especificado na caixa **Versões**. Clique em **Instalar** e aceite os termos de utilização.
   
    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)
   
    O Visual Studio permite transferir, instalar e adicionar uma referência ao [do Service Bus do Azure – Pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.
9. Clique com o botão direito do rato no projeto **Recetor**, clique em **Adicionar** e em **Classe**. Dê à nova classe o nome **SimpleEventProcessor** e, em seguida, clique em **Adicionar** para criar a classe.
   
    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)
10. Adicione as seguintes declarações na parte superior do ficheiro SimpleEventProcessor.cs:
    
     ```
     using Microsoft.ServiceBus.Messaging;
     using System.Diagnostics;
     ```
    
     Em seguida, substitua o seguinte código do corpo da classe:
    
     ```
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
    
     ```
     using Microsoft.ServiceBus.Messaging;
     ```
    
     Em seguida, substitua o método `Main` na classe `Program` pelo seguinte código, substituindo o nome do Hub de Eventos e a cadeia de ligação ao nível do espaço de nomes que guardou anteriormente e a conta de armazenamento e a chave que copiou nas secções anteriores. 
    
     ```
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
> Este tutorial utiliza uma única instância do [EventProcessorHost][EventProcessorHost]. Para aumentar o débito, recomendamos que execute várias instâncias do [EventProcessorHost][EventProcessorHost], como mostrado no exemplo de [Processamento de eventos aumentados horizontalmente][Processamento de eventos aumentados horizontalmente]. Nesses casos, as várias instâncias coordenam-se automaticamente entre si para equilibrarem a carga dos eventos recebidos. Se pretender várias recetores para que cada um processe *todos* os eventos, terá de utilizar o conceito **ConsumerGroup**. Se receber eventos de vários computadores, poderá ser útil especificar os nomes das instâncias do [EventProcessorHost][EventProcessorHost] com base nos computadores (ou funções) em que estão implementadas. Para obter mais informações sobre estes tópicos, veja [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)][Event Hubs Overview (Descrição Geral dos Hubs de Eventos)] e [Event Hubs Programming Guide (Guia de Programação dos Hubs de Eventos)][Hubs de Eventos Programming Guide (Guia de Programação dos Hubs de Eventos)].
> 
> 

<!-- Links -->
[Descrição Geral dos Hubs de Eventos]: ../articles/event-hubs/event-hubs-overview.md
[Guia de Programação dos Hubs de Eventos]: ../articles/event-hubs/event-hubs-programming-guide.md
[Processamento de eventos aumentados horizontalmente]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Conta de Armazenamento do Azure]: ../articles/storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Portal do Azure]: https://portal.azure.com

<!--HONumber=Nov16_HO2-->


