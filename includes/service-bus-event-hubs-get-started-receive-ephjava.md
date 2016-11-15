## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Receber mensagens com o EventProcessorHost em Java
O EventProcessorHost é uma classe do Java que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo desses Hubs de Eventos. Se utilizar o EventProcessorHost, pode dividir eventos por vários recetores, mesmo se estiverem alojados em nós diferentes. Este exemplo mostra como utilizar o EventProcessorHost para um recetor único.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Para utilizar o EventProcessorHost, tem de ter uma [Conta de armazenamento do Azure][Conta de armazenamento do Azure]:

1. Inicie sessão no [Portal Clássico do Azure][Portal Clássico do Azure] e clique em **NOVO** na parte inferior do ecrã.
2. Clique em **Serviços de Dados**, em **Armazenamento** em **Criação Rápida** e escreva um nome para a sua conta de armazenamento. Selecione a região pretendida e clique em **Criar Conta de Armazenamento**.
   
    ![][11]
3. Clique na conta de armazenamento acabada de criar e, em seguida, clique em **Gerir Chaves de Acesso**:
   
    ![][12]
   
    Copie a chave de acesso primária para a utilizar posteriormente neste tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Criar um projeto Java com o EventProcessorHost
A biblioteca de cliente Java para os Hubs de Eventos pode ser utilizada em projetos Maven a partir do [Repositório Central Maven][Pacote Maven], e pode ser referenciada através da seguinte declaração de dependência dentro do ficheiro de projeto Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Para diferentes tipos de ambientes de compilação, pode obter explicitamente os ficheiros JAR mais recentes do [Repositório Central Maven][Pacote Maven] ou a partir do [ponto de distribuição de versão no GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. A classe será designada ```ErrorNotificationHandler```.     
   
    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Utilize o seguinte código para criar uma nova classe designada ```EventProcessor```.
   
    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;
   
    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;
   
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }
   
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
   
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }
   
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
   
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```
3. Criar uma classe final designada ```EventProcessorSample``` com o seguinte código.
   
    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;
   
    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";
   
            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
   
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
   
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
   
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }
   
            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
   
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
   
            System.out.println("End of sample");
        }
    }
    ```
4. Substitua os seguintes campos pelos valores utilizados aquando da criação do Hub de Eventos e da conta de armazenamento.
   
    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
   
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
   
    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [!NOTE]
> Este tutorial utiliza uma única instância do EventProcessorHost. Para aumentar o débito, recomendamos que execute várias instâncias do EventProcessorHost. Nesses casos, as várias instâncias coordenam-se automaticamente entre si para equilibrarem a carga dos eventos recebidos. Se pretender várias recetores para que cada um processe *todos* os eventos, terá de utilizar o conceito **ConsumerGroup**. Se receber eventos de vários computadores, poderá ser útil especificar os nomes das instâncias do EventProcessorHost com base nos computadores (ou funções) em que estão implementadas.
> 
> 

<!-- Links -->
[Descrição geral dos Hubs de Eventos]: ../articles/event-hubs/event-hubs-overview.md
[Conta de Armazenamento do Azure]: ../articles/storage/storage-create-storage-account.md
[Portal Clássico do Azure]: http://manage.windowsazure.com
[Pacote Maven]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png



<!--HONumber=Nov16_HO2-->


