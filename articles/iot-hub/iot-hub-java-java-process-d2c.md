---
title: Processar mensagens do dispositivo para a nuvem do IoT Hub do Azure (Java) | Microsoft Docs
description: "Como processar mensagens de dispositivo para a nuvem do IoT Hub utilizando regras de encaminhamento e os pontos finais personalizados para emitir mensagens a outros serviços de back-end."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: 0fb3e9012ae88112515ebb552e49fa463a087f54
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>Processar mensagens do dispositivo para a nuvem do IoT Hub (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

IoT Hub do Azure é um serviço completamente gerido que permite fiável e seguras comunicações bidirecionais entre milhões de dispositivos e uma solução de back-end. Outros tutoriais ([introdução ao IoT Hub] e [enviar mensagens da nuvem para o dispositivo com o IoT Hub][lnk-c2d]) mostram-lhe como utilizar o basic dispositivo para nuvem e da nuvem para o dispositivo funcionalidade serviço de mensagens do IoT Hub.

Este tutorial baseia-se o código mostrado o [introdução ao IoT Hub] tutorial e mostra-lhe como utilizar o encaminhamento de mensagens para processar mensagens do dispositivo para a nuvem de uma forma escalável. O tutorial ilustra como processar mensagens que requerem ação imediata da solução de back-end. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que aciona a inserir um pedido de um sistema CRM. Por outro lado, as mensagens de ponto de dados simplesmente feed para um motor de análise. Por exemplo, a telemetria de temperatura de um dispositivo que está a ser armazenados durante a análise posterior é uma mensagem de ponto de dados.

No final deste tutorial, execute três aplicações de consola Java:

* **simulated-device**, uma versão modificada da aplicação criada no [introdução ao IoT Hub] tutorial, envia mensagens do dispositivo para a nuvem de ponto de dados de cada segundo e interativa dispositivo-nuvem mensagens cada 10 segundos . Esta aplicação utiliza o protocolo AMQP para comunicar com o IoT Hub.
* **Read-d2c-messages** apresenta a telemetria enviada pela sua aplicação de dispositivo.
* **leitura críticos fila** remove as mensagens de evento crítico da fila de barramento de serviço ligado ao IoT hub.

> [!NOTE]
> IoT Hub tem suporte SDK para várias plataformas de dispositivos e idiomas, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo neste tutorial com um dispositivo físico e como ligar dispositivos para um IoT Hub, consulte o [Centro de programadores do IoT do Azure].

Para concluir este tutorial, precisa do seguinte:

* Uma versão completa do trabalho do [introdução ao IoT Hub] tutorial.
* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

É necessário ter algum conhecimento básico de [Storage do Azure] e [Service Bus do Azure].

## <a name="send-interactive-messages-from-a-device-app"></a>Enviar mensagens interativas de uma aplicação de dispositivo
Nesta secção, modificar a aplicação de dispositivo que criou no [introdução ao IoT Hub] tutorial ocasionalmente enviar mensagens que necessitam de processamento de imediato.

1. Utilize um editor de texto para abrir o ficheiro simulated-device\src\main\java\com\mycompany\app\app.Java. Este ficheiro contém o código para o **simulated-device** aplicação que criou no [introdução ao IoT Hub] tutorial.

2. Substitua o **MessageSender** classe com o seguinte código:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
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
   
    Este método aleatoriamente adiciona a propriedade `"level": "critical"` às mensagens enviadas pelo dispositivo, que simula uma mensagem que necessita de uma ação imediata, o back-end da aplicação. A aplicação passa a informação nas propriedades da mensagem, em vez de no corpo da mensagem, pelo que o IoT Hub pode encaminhar a mensagem para o destino de mensagem adequado.
   
   > [!NOTE]
   > Pode utilizar as propriedades da mensagem para encaminhar mensagens para vários cenários, incluindo o processamento de frio caminho, para além do exemplo de caminho frequente apresentado aqui.

2. Guarde e feche o ficheiro simulated-device\src\main\java\com\mycompany\app\app.Java.

    > [!NOTE]
    > Com vista à, simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar uma política de repetição como término exponencial, como sugerido no artigo do MSDN [processamento de erros transitórios].

3. Para criar a aplicação **simulated-device** com o Maven, execute o seguinte comando na linha de comandos da pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adicionar uma fila ao seu IoT hub e rota mensagens ao mesmo

Nesta secção, criar uma fila do Service Bus, ligue-o ao seu IoT hub e configurar o seu IoT hub para enviar mensagens para a fila com base na presença de uma propriedade na mensagem. Para obter mais informações sobre como processar mensagens de filas do Service Bus, consulte [introdução às filas][lnk-sb-queues-java].

1. Criar uma fila do Service Bus, conforme descrito em [introdução às filas][lnk-sb-queues-java]. Tome nota do nome do espaço de nomes e a fila.

2. No portal do Azure, abra o seu IoT hub e clique em **pontos finais**.

    ![Pontos finais do IoT hub][30]

3. No **pontos finais** painel, clique em **adicionar** na parte superior para adicionar uma fila ao seu IoT hub. O ponto final de nome **CriticalQueue** e utilizar listas pendentes para selecionar **fila do Service Bus**, o espaço de nomes de barramento de serviço em que reside a sua fila e o nome da sua fila. Quando tiver terminado, clique em **guardar** na parte inferior.

    ![Adicionar um ponto final][31]

4. Agora, clique em **rotas** no seu IoT Hub. Clique em **adicionar** na parte superior do painel para criar uma regra de encaminhamento encaminha mensagens para a fila acabou de adicionar. Selecione **DeviceTelemetry** como origem de dados. Introduza `level="critical"` como condição e escolha a fila que acabou de adicionar como um ponto final personalizado como o encaminhamento ponto final da regra. Quando tiver terminado, clique em **guardar** na parte inferior.

    ![Adicionar uma rota][32]

    Certifique-se a rota de contingência está definida como **ON**. Esta definição é a configuração predefinida de um hub IoT.

    ![Rota de contingência][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Ler a partir do ponto final de fila

Opcionalmente, pode ler as mensagens do ponto final fila ao seguir as instruções no [introdução às filas][lnk-sb-queues-java]. Nome da aplicação **leitura críticos fila**.

## <a name="run-the-applications"></a>Executar as aplicações

Agora, está pronto para executar as aplicações de três.

1. Para executar o **read-d2c-messages** aplicação, numa linha de comandos ou a shell, navegue para a pasta read-d2c e execute o seguinte comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Executar read-d2c-messages][readd2c]

2. Para executar o **leitura críticos fila** aplicação, numa linha de comandos ou shell navegue para a pasta de leitura fila críticos e execute o seguinte comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar leitura mensagens crítico][readqueue]

3. Para executar o **simulated-device** aplicação, numa linha de comandos ou a shell, navegue para a pasta simulated-device e execute o seguinte comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar simulated-device][simulateddevice]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a fiável emitir mensagens do dispositivo para nuvem utilizando a funcionalidade de encaminhamento de mensagens do IoT Hub.

O [como enviar mensagens da nuvem para o dispositivo com o IoT Hub] [ lnk-c2d] mostra como enviar mensagens para os dispositivos da sua solução de back-end.

Para ver os exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para obter mais informações sobre como desenvolver soluções de IoT hub, consulte o [guia para programadores do IoT Hub].

Para saber mais sobre o encaminhamento de mensagens no IoT Hub, veja [enviar e receber mensagens com o IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Storage do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Service Bus do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[guia para programadores do IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[introdução ao IoT Hub]: iot-hub-java-java-getstarted.md
[Centro de programadores do IoT do Azure]: https://azure.microsoft.com/develop/iot
[processamento de erros transitórios]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Processamento de falhas transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
