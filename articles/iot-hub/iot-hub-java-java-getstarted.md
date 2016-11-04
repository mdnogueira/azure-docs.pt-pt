---
title: IoT Hub do Azure para uma introdução ao Java | Microsoft Docs
description: IoT Hub do Azure com tutorial de introdução ao Java. Utilize o Hub IoT do Azure e o Java com os SDKs IoT do Microsoft Azure para implementar uma solução da Internet das Coisas.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: dobett

---
# Introdução ao IoT Hub do Azure para Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, terá três aplicações de consola Java:

* **create-device-identity**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar o seu dispositivo simulado.
* **read-d2c-messages**, que apresenta a telemetria enviada pelo seu dispositivo simulado.
* **simulated-device**, que liga ao seu IoT Hub com a identidade de dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo através do protocolo AMQPS.

> [!NOTE]
> O artigo [SDKs do IoT Hub][lnk-hub-sdks] fornece informações sobre os vários SDKs que pode utilizar para criar ambas as aplicações a executar em dispositivos e a sua solução de back-end.
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Java SE 8. <br/> [Preparar o seu ambiente de desenvolvimento][Ink-dev-setup] descreve como instalar o Java para este tutorial no Windows ou Linux.
* Maven 3.  <br/> [Preparar o seu ambiente de desenvolvimento][Ink-dev-setup] descreve como instalar o Maven para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como passo final, anote o valor da **Chave primária** e, em seguida, clique em **Mensagens**. No painel **Mensagens**, crie uma nota do **nome compatível com o Event Hub** e o **ponto final compatível com o Event Hub**. Irá precisar destes três valores quando criar a sua aplicação **read-d2c-messages**.

![][6]

Criou o seu Hub IoT e tem agora o nome de anfitrião, a cadeia de ligação e a Chave Primária do Hub IoT, assim como o nome e o ponto final compatível com os Hubs de Eventos de que precisa para concluir este tutorial.

## Criar uma identidade de dispositivo
Nesta secção, irá criar uma aplicação de consola do Java que cria uma nova identidade de dispositivo no registo de identidade do seu Hub IoT. Não é possível ligar um dispositivo ao IoT Hub exceto se tiver uma entrada no registo de identidade de dispositivo. Para mais informações, consulte a secção **Registo de Identidade de Dispositivo** do [Guia para Programadores do IoT Hub][lnk-devguide-identity]. Ao executar esta aplicação de consola, é gerado um ID de dispositivo exclusivo e uma chave que o seu dispositivo pode utilizar para se identificar quando enviar mensagens do dispositivo para a nuvem ao Hub IoT.

1. Crie uma nova pasta vazia designada iot-java-get-started. Na pasta iot-java-get-started, crie um novo projeto Maven designado **create-device-identity** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. Na sua linha de comandos, navegue para a nova pasta create-device-identity.
3. Com um editor de texto, abra o ficheiro pom.xml na pasta create-device-identity e adicione a seguinte dependência ao nó **dependências**. Tal permite-lhe utilizar o pacote iothub-service-sdk na sua aplicação
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```
4. Guarde e feche o ficheiro pom.xml.
5. Com um editor de texto, abra o ficheiro create-device-identity\src\main\java\com\mycompany\app\App.java.
6. Adicione as seguintes declarações de **importação** ao ficheiro:
   
    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Adicione as seguintes variáveis de nível de classe à classe **Aplicação** e substitua **{yourhubconnectionstring}** pelo valor que apontou anteriormente:
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Modifique a assinatura do método **principal** para incluir as exceções da seguinte forma:
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Adicione o seguinte código como o corpo do método **principal**. Se ainda não existir, este código cria um dispositivo designado *javadevice* no seu registo de identidade do IoT Hub. De seguida, apresenta o ID do dispositivo e a chave de que irá precisar posteriormente:
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Guarde e feche o ficheiro App.java.
11. Para criar a aplicação **create-device-identity** com o Maven, execute o seguinte comando na linha de comandos na pasta create-device-identity:
    
    ```
    mvn clean package -DskipTests
    ```
12. Para executar a aplicação **create-device-identity** com o Maven, execute o seguinte comando na linha de comandos na pasta create-device-identity:
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Tome note do **ID do Dispositivo** e da **Chave do Dispositivo**. Irá precisar destas informações posteriormente quando criar uma aplicação que liga ao IoT Hub como um dispositivo.

> [!NOTE]
> O registo de identidade do IoT Hub apenas armazena identidades de dispositivo para permitir um acesso seguro ao Hub. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, consulte o artigo [Guia para Programadores do IoT Hub][lnk-devguide-identity].
> 
> 

## Receber mensagens dispositivo-nuvem
Nesta secção, irá criar uma aplicação de consola do Java que lê mensagens do dispositivo para a nuvem a partir do Hub IoT. Um IoT hub expõe um ponto final compatível com [Event Hub][lnk-event-hubs-overview], o que lhe permite ler mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implementação com débito elevado. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra-lhe como processar mensagens do dispositivo para a nuvem à escala. O tutorial [Introdução aos Hubs de Eventos][Ink-eventhubs-tutorial] fornece mais informações sobre como processar mensagens a partir dos Hubs de Eventos e é aplicável aos pontos finais do Hub IoT compatíveis com os Hubs de Eventos.

> [!NOTE]
> O ponto final compatível com o Event Hubs para a leitura de mensagens do dispositivo para a nuvem utiliza sempre o protocolo AMQPS.
> 
> 

1. Na pasta iot-java-get-started que criou na secção *Criar uma identidade do dispositivo*, crie um novo projeto Maven designado **read-d2c-messages** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. Na sua linha de comandos, navegue para a nova pasta read-d2c-messages.
3. Com um editor de texto, abra o ficheiro pom.xml na pasta read-d2c-messages e adicione a seguinte dependência ao nó **dependências**. Tal permite-lhe utilizar o pacote eventhubs-client na sua aplicação para ler a partir do ponto final compatível com os Hubs de Eventos:
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```
4. Guarde e feche o ficheiro pom.xml.
5. Com um editor de texto, abra o ficheiro read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Adicione as seguintes declarações de **importação** ao ficheiro:
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
   
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```
7. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua **{youriothubkey}**, **{youreventhubcompatibleendpoint}** e **{youreventhubcompatiblename}** pelos valores que apontou anteriormente:
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Adicione o seguinte método **receiveMessages** à classe **Aplicação**. Este método cria uma instância **EventHubClient** para ligar ao ponto final compatível com os Hubs de Eventos e, em seguida, cria de forma assíncrona uma instância **PartitionReceiver** que será lida a partir de uma partição do Hub de Eventos. Repete continuamente o ciclo e imprima os detalhes da mensagem até a aplicação concluir.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Este método utiliza um filtro quando cria o recetor . Deste modo, o recetor apenas poderá ler as mensagens enviadas ao IoT Hub depois de o recetor entrar em execução. Isto é útil num ambiente de teste para que possa ver o conjunto atual de mensagens. Num ambiente de produção, deverá certificar-se de que o seu código processa todas as mensagens – para mais informações, consulte o tutorial [Como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-process-d2c-tutorial].
   > 
   > 
9. Modifique a assinatura do método **principal** para incluir a exceção da seguinte forma:
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Adicione o seguinte código ao método **principal** na classe **Aplicação**. Este código cria as duas instâncias **EventHubClient** e **PartitionReceiver** e permite-lhe fechar a aplicação quando tiver concluído o processamento das mensagens:
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > Este código pressupõe que criou o seu IoT Hub na camada F1 (gratuita). Um IoT Hub gratuito tem duas partições designadas "0" e "1".
    > 
    > 
11. Guarde e feche o ficheiro App.java.
12. Para criar a aplicação **read-d2c-messages** com o Maven, execute o seguinte comando na linha de comandos na pasta read-d2c-messages:
    
    ```
    mvn clean package -DskipTests
    ```

## Criar uma aplicação de dispositivo simulada
Nesta secção, irá criar uma aplicação de consola do Java que simula um dispositivo que envia mensagens do dispositivo para a nuvem a um Hub IoT.

1. Na pasta iot-java-get-started que criou na secção *Criar uma identidade do dispositivo*, crie um novo projeto Maven designado **simulated-device** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. Na sua linha de comandos, navegue para a nova pasta simulated-device.
3. Com um editor de texto, abra o ficheiro pom.xml na pasta simulated-device e adicione as seguintes dependências ao nó **dependências**. Tal permite-lhe utilizar o pacote iothub-java-client na sua aplicação para comunicar com o seu IoT Hub e serializar objetos Java no JSON:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.8</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Guarde e feche o ficheiro pom.xml.
5. Com um editor de texto, abra o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.
6. Adicione as seguintes declarações de **importação** ao ficheiro:
   
    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Adicione as seguintes variáveis de nível de classe à classe **Aplicação** e substitua **{youriothubname}** pelo nome do seu Hub IoT e **{yourdevicekey}** pelo valor-chave do dispositivo que gerou na secção *Criar uma identidade do dispositivo*:
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**. Tanto pode utilizar o protocolo HTTPS como o AMQPS para comunicar com IoT Hub.
8. Adicione a seguinte classe aninhada **TelemetryDataPoint** à classe **Aplicação** para especificar os dados de telemetria que o seu dispositivo envia ao seu IoT Hub:
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Adicione a seguinte classe aninhada **EventCallback** à classe **Aplicação** para apresentar o estatuto de reconhecimento que o IoT Hub devolve quando processa uma mensagem a partir do dispositivo simulado. Este método também notifica o thread principal na aplicação quando a mensagem tiver sido processada:
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. Adicione a seguinte classe aninhada **MessageSender** à classe **Aplicação**. O método **executar** desta classe gera dados de telemetria de exemplo a enviar ao seu IoT Hub e aguarda uma confirmação antes de enviar a mensagem seguinte:
    
    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```
    
    Este método envia uma nova mensagem do dispositivo para a nuvem um segundo depois de o IoT Hub ter confirmado a mensagem anterior. A mensagem contém um objeto JSON serializado com o ID do dispositivo e um número gerado aleatoriamente para simular um sensor de velocidade do vento.
11. Substitua o método **principal** pelo seguinte código que cria um thread para enviar mensagens do dispositivo para a nuvem ao seu IoT Hub:
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Guarde e feche o ficheiro App.java.
13. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Processamento de Erros Transitórios][Ink-transitório-lento] da MSDN.
> 
> 

## Executar as aplicações
Pode agora executar as aplicações.

1. Numa linha de comandos da pasta read-d2c, execute o seguinte comando para começar a monitorizar a primeira partição no seu IoT Hub:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![][7]
2. Numa linha de comandos da pasta simulated-device, execute o seguinte comando para começar a enviar dados de telemetria ao seu IoT Hub:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![][8]
3. O mosaico **Utilização** no [Portal do Azure][Ink-portal] mostra o número de mensagens enviadas ao Hub:
   
    ![][43]

## Passos seguintes
Neste tutorial, configurou um novo IoT Hub no Portal e, de seguida, criou uma identidade de dispositivo no registo de identidade do Hub. Utilizou esta identidade de dispositivo para que a aplicação do dispositivo simulado pudesse enviar mensagens do dispositivo para a nuvem ao Hub. Também criou uma aplicação que apresenta as mensagens recebidas através do Hub. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Ligar o seu dispositivo][lnk-connect-device]
* [Introdução à gestão de dispositivos][lnk-device-management]
* [Introdução ao SDK do Gateway][lnk-gateway-SDK]

Para saber como expandir a sua solução de IoT e processar mensagens do dispositivo para a nuvem à escala, veja o tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial].

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[Ink-transitório-lento]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[Ink-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[Ink-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Ink-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Sep16_HO3-->


