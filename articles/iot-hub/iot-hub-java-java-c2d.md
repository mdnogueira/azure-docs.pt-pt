---
title: Mensagens da nuvem para dispositivo IoT hub do Azure (Java) | Microsoft Docs
description: "Como enviar mensagens da nuvem para o dispositivo a um dispositivo de um hub IoT do Azure com os SDKs IoT do Azure para Java. Modificar uma aplicação de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar uma aplicação de back-end para enviar as mensagens da nuvem para o dispositivo."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 6a5f14f411c2ec82478fef6d20d22f8b8dc8d7bf
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Enviar mensagens da nuvem para o dispositivo com o IoT Hub (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

IoT Hub do Azure é um serviço completamente gerido que o ajuda a ativar fiáveis e seguras comunicações bidirecionais entre milhões de dispositivos e uma solução de back-end. O [introdução ao IoT Hub] tutorial mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Este tutorial baseia-se [introdução ao IoT Hub]. Mostra-lhe como para:

* Da sua solução de back-end, envie mensagens de nuvem para o dispositivo para um único dispositivo através do IoT Hub.
* Receba mensagens da nuvem para o dispositivo num dispositivo.
* Da sua solução de back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo a partir do IoT Hub.

Pode encontrar mais informações sobre mensagens da nuvem para o dispositivo no [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].

No final deste tutorial, executa duas aplicações de consola Java:

* **simulated-device**, uma versão modificada da aplicação criada na [introdução ao IoT Hub], que liga ao seu IoT hub e recebe mensagens da nuvem para o dispositivo.
* **Enviar-c2d-messages**, que envia uma mensagem da nuvem para o dispositivo para a aplicação de dispositivo simulado através do IoT Hub e, em seguida, receber a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para várias plataformas de dispositivos e idiomas (incluindo C, Java e Javascript) através de SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como ligar o seu dispositivo para código neste tutorial e, geralmente, IoT Hub do Azure, consulte o [Centro de programadores do IoT do Azure].

Para concluir este tutorial, precisa do seguinte:

* Uma versão completa do trabalho do [introdução ao IoT Hub](iot-hub-java-java-getstarted.md) ou [mensagens de dispositivo para a nuvem do IoT Hub do processo](iot-hub-java-java-process-d2c.md) tutorial.
* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação do dispositivo simulado

Nesta secção, modificar a aplicação de dispositivo simulado que criou no [introdução ao IoT Hub] para receber mensagens da nuvem para dispositivos do IoT hub.

1. Com um editor de texto, abra o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione o seguinte **MessageCallback** classe do que uma classe aninhada dentro de **aplicação** classe. O **executar** método é invocado quando o dispositivo recebe uma mensagem a partir do IoT Hub. Neste exemplo, o dispositivo sempre notifica o IoT hub que terminou a mensagem:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Modificar o **principal** método para criar um **AppMessageCallback** instância e a chamada de **setMessageCallback** método antes de abrir o cliente da seguinte forma:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Se utilizar o HTTPS em vez de MQTT ou AMQP como transporte, o **DeviceClient** instância verifica a existência de mensagens a partir do IoT Hub com pouca frequência (inferior a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTPS e a limitação do IoT Hub, consulte o [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].

4. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo

Nesta secção, crie uma aplicação de consola do Java que envia mensagens da nuvem para o dispositivo para a aplicação de dispositivo simulado. É necessário o ID de dispositivo do dispositivo adicionado no [introdução ao IoT Hub] tutorial. Também precisa da cadeia de ligação do IoT Hub para o seu hub que pode encontrar o [portal do Azure].

1. Criar um projeto Maven designado **send-c2d-messages** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção de que este comando é um comando único, por extenso:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de envio-c2d-messages.

3. Com um editor de texto, abra o ficheiro pom.xml na pasta a enviar-c2d-messages e adicione a seguinte dependência à **dependências** nós. Adicionar a dependência permite-lhe utilizar o **iothub-java--o cliente do serviço** pacote na sua aplicação para comunicar com o serviço de hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-service-client** utilizando a [pesquisa Maven][lnk-maven-service-search].

4. Guarde e feche o ficheiro pom.xml.

5. Com um editor de texto, abra o ficheiro de send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis de nível de classe para o **aplicação** classe, substituindo **{yourhubconnectionstring}** e **{yourdeviceid}** com os valores que apontou anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Substitua o **principal** método com o seguinte código. Este código liga ao seu IoT hub, envia uma mensagem para o seu dispositivo e, em seguida, aguarda uma confirmação de que o dispositivo recebida e processada a mensagem:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Para sake do simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como término exponencial), como sugerido no artigo do MSDN [processamento de erros transitórios].


9. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Pode agora executar as aplicações.

1. Numa linha de comandos da pasta simulated-device, execute o seguinte comando para começar a enviar telemetria ao seu IoT hub e escutam as mensagens da nuvem para o dispositivo enviadas do seu hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Executar a aplicação de dispositivo simulada][img-simulated-device]

2. Numa linha de comandos na pasta a enviar-c2d-messages, execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e a aguardar uma confirmação de comentários:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Execute o comando para enviar a mensagem da nuvem para o dispositivo][img-send-command]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e receber mensagens da nuvem para o dispositivo. 

Para ver os exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, consulte [Azure IoT Suite].

Para obter mais informações sobre como desenvolver soluções de IoT hub, consulte o [guia para programadores do IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[introdução ao IoT Hub]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[guia para programadores do IoT Hub]: iot-hub-devguide.md
[Centro de programadores do IoT do Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[processamento de erros transitórios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portal do Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22