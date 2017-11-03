---
title: Processar mensagens de dispositivo para a nuvem do IoT Hub do Azure utilizar rotas (.Net) | Microsoft Docs
description: "Como processar mensagens de dispositivo para a nuvem do IoT Hub utilizando regras de encaminhamento e os pontos finais personalizados para emitir mensagens a outros serviços de back-end."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 1d2b52ea005ab520bf294efa603512c00a92ee63
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="process-iot-hub-device-to-cloud-messages-using-routes-net"></a>Processar mensagens do dispositivo para a nuvem do IoT Hub utilizar rotas (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial baseia-se a [introdução ao IoT Hub] tutorial. Tutorial:

* Mostra como utilizar as regras de encaminhamento para emitir mensagens do dispositivo para nuvem de uma forma fácil, baseadas na configuração.
* Ilustra a isolar mensagens interativas que requerem ação imediata da solução de back-end para processamento adicional. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que aciona a inserir um pedido de um sistema CRM. Em contrapartida, as mensagens de ponto de dados, tais como a telemetria de temperatura, feed para um motor de análise.

No final deste tutorial, execute três aplicações de consola .NET:

* **SimulatedDevice**, uma versão modificada da aplicação criada no [introdução ao IoT Hub] tutorial envia mensagens do dispositivo para a nuvem de ponto de dados de cada segundo e interativa dispositivo-nuvem mensagens cada 10 segundos.
* **ReadDeviceToCloudMessages** que apresenta a telemetria não críticos enviada pela sua aplicação de dispositivo.
* **ReadCriticalQueue** remove as mensagens de evento crítico enviadas pela aplicação de dispositivo de uma fila do Service Bus. Esta fila está ligada ao IoT hub.

> [!NOTE]
> IoT Hub tem suporte SDK para várias plataformas de dispositivos e idiomas, incluindo C, Java e JavaScript. Para saber como substituir o dispositivo simulado neste tutorial com um dispositivo físico, consulte o [Centro de programadores do IoT do Azure].

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

É necessário ter algum conhecimento básico de [Storage do Azure] e [Service Bus do Azure].

## <a name="send-interactive-messages"></a>Enviar mensagens interativas

Modificar a aplicação de dispositivo que criou no [introdução ao IoT Hub] tutorial ocasionalmente enviar mensagens interativas.

No Visual Studio, no **SimulatedDevice** do projeto, substitua o `SendDeviceToCloudMessagesAsync` método com o seguinte código:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            messageString = "This is a critical message";
            levelValue = "critical";
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Este método aleatoriamente adiciona a propriedade `"level": "critical"` às mensagens enviadas pelo dispositivo, que simula uma mensagem que necessita de uma ação imediata, a solução de back-end. A aplicação de dispositivo transfere a informação nas propriedades da mensagem, em vez de no corpo da mensagem, pelo que o IoT Hub pode encaminhar a mensagem para o destino de mensagem adequado.

> [!NOTE]
> Pode utilizar as propriedades da mensagem para encaminhar mensagens para vários cenários, incluindo o processamento de frio caminho, para além de exemplo-path mostrado aqui.

> [!NOTE]
> Com vista à, simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar uma política de repetição como término exponencial, como sugerido no artigo do MSDN [processamento de erros transitórios].

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Encaminhar mensagens a uma fila no seu IoT hub

Nesta secção, pode:

* Crie uma fila do Service Bus.
* Ligue-o ao seu IoT hub.
* Configure o seu IoT hub para enviar mensagens para a fila com base na presença de uma propriedade na mensagem.

Para obter mais informações sobre como processar mensagens de filas do Service Bus, consulte [introdução às filas][Service Bus queue].

1. Criar uma fila do Service Bus, conforme descrito em [introdução às filas][Service Bus queue]. A fila tem de ser na mesma subscrição e região como o seu IoT hub. Tome nota do nome do espaço de nomes e a fila.

    > [!NOTE]
    > Utilizado como pontos finais de IoT Hub não pode ter de tópicos e filas de Service Bus **sessões** ou **duplicado deteção** ativada. Se qualquer um dessas opções estiverem ativado, o ponto final é apresentado como **Unreachable** no portal do Azure.

2. No portal do Azure, abra o seu IoT hub e clique em **pontos finais**.
    
    ![Pontos finais do IoT hub][30]

3. No **pontos finais** painel, clique em **adicionar** na parte superior para adicionar uma fila ao seu IoT hub. O ponto final de nome **CriticalQueue** e utilizar listas pendentes para selecionar **fila do Service Bus**, o espaço de nomes de barramento de serviço em que reside a sua fila e o nome da sua fila. Quando tiver terminado, clique em **guardar** na parte inferior.
    
    ![Adicionar um ponto final][31]
    
4. Agora, clique em **rotas** no seu IoT Hub. Clique em **adicionar** na parte superior do painel para criar uma regra de encaminhamento encaminha mensagens para a fila acabou de adicionar. Selecione **DeviceTelemetry** como origem de dados. Introduza `level="critical"` como condição e escolha a fila que acabou de adicionar como um ponto final personalizado como o encaminhamento ponto final da regra. Quando tiver terminado, clique em **guardar** na parte inferior.
    
    ![Adicionar uma rota][32]
    
    Certifique-se a rota de contingência está definida como **ON**. Este valor é a configuração predefinida para um hub IoT.
    
    ![Rota de contingência][33]

## <a name="read-from-the-queue-endpoint"></a>Ler a partir do ponto final de fila

Nesta secção, ler as mensagens a partir do ponto final de fila.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à solução atual, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Nomeie o projeto **ReadCriticalQueue**.

2. No Explorador de soluções, clique com botão direito do **ReadCriticalQueue** projeto e, em seguida, clique em **gerir pacotes NuGet**. Esta operação apresenta o **Gestor de pacotes NuGet** janela.

3. Procurar **Windowsazure**, clique em **instalar**e aceite os termos de utilização. Esta operação transfere, instala e adiciona uma referência para o Service Bus do Azure, com as respetivas dependências.

4. Adicione o seguinte **utilizando** declarações na parte superior do **Program.cs** ficheiro:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por fim, adicione as seguintes linhas à **Main** método. Substitua a cadeia de ligação com **escutar** permissões para a fila:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar as aplicações
Agora pode executar as aplicações.

1. No Visual Studio, no Explorador de soluções, faça duplo clique a sua solução e selecione **definir projetos de arranque**. Selecione **vários projetos de arranque**, em seguida, selecione **iniciar** como a ação para o **ReadDeviceToCloudMessages**, **SimulatedDevice**, e **ReadCriticalQueue** projetos.
2. Prima **F5** para iniciar os três aplicações de consola. O **ReadDeviceToCloudMessages** a aplicação tem apenas não críticos mensagens enviadas pelo **SimulatedDevice** aplicação e o **ReadCriticalQueue** a aplicação tem apenas mensagens de evento crítico.
   
   ![Três aplicações de consola][50]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a fiável emitir mensagens do dispositivo para nuvem utilizando a funcionalidade de encaminhamento de mensagens do IoT Hub.

O [como enviar mensagens da nuvem para o dispositivo com o IoT Hub] [ lnk-c2d] mostra como enviar mensagens para os dispositivos da sua solução de back-end.

Para ver os exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para obter mais informações sobre como desenvolver soluções de IoT hub, consulte o [guia para programadores do IoT Hub].

Para saber mais sobre o encaminhamento de mensagens no IoT Hub, veja [enviar e receber mensagens com o IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Storage do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Service Bus do Azure]: https://azure.microsoft.com/documentation/services/service-bus/
[guia para programadores do IoT Hub]: iot-hub-devguide.md
[introdução ao IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Centro de programadores do IoT do Azure]: https://azure.microsoft.com/develop/iot
[processamento de erros transitórios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
