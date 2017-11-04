---
title: Mensagens da nuvem para o dispositivo com o Azure IoT Hub (.NET) | Microsoft Docs
description: "Como enviar mensagens da nuvem para o dispositivo a um dispositivo de um hub IoT do Azure com os SDKs IoT do Azure para .NET. Modificar uma aplicação de dispositivo para receber mensagens da nuvem para o dispositivo e modificar uma aplicação de back-end para enviar as mensagens da nuvem para o dispositivo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: a31c05ed-6ec0-40f3-99ab-8fdd28b1a89a
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: ebb78c9ae4e0ae02f9f9d74f4909d9108cd8aef7
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Enviar mensagens da nuvem para o seu dispositivo com o IoT Hub (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução
IoT Hub do Azure é um serviço completamente gerido que o ajuda a ativar fiáveis e seguras comunicações bidirecionais entre milhões de dispositivos e uma solução de back-end. O [introdução ao IoT Hub] tutorial mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo que envia mensagens do dispositivo para a nuvem.

Este tutorial baseia-se [introdução ao IoT Hub]. Mostra-lhe como para:

* Da sua solução de back-end, envie mensagens de nuvem para o dispositivo para um único dispositivo através do IoT Hub.
* Receba mensagens da nuvem para o dispositivo num dispositivo.
* Da sua solução de back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo a partir do IoT Hub.

Pode encontrar mais informações sobre mensagens da nuvem para o dispositivo no [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].

No final deste tutorial, executa duas aplicações de consola .NET:

* **SimulatedDevice**, uma versão modificada da aplicação criada na [introdução ao IoT Hub], que liga ao seu IoT hub e recebe mensagens da nuvem para o dispositivo.
* **SendCloudToDevice**, que envia uma mensagem da nuvem para o dispositivo para a aplicação de dispositivos através do IoT Hub e, em seguida, receber a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para várias plataformas de dispositivos e idiomas (incluindo C, Java e Javascript) através de [SDKs do Azure IoT device]. Para obter instruções passo a passo sobre como ligar o seu dispositivo para código neste tutorial e, geralmente, IoT Hub do Azure, consulte o [guia para programadores do IoT Hub].
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-in-the-device-app"></a>Receber mensagens na aplicação do dispositivo
Nesta secção, irá modificar a aplicação de dispositivo que criou no [introdução ao IoT Hub] para receber mensagens da nuvem para dispositivos do IoT hub.

1. No Visual Studio, no **SimulatedDevice** do projeto, adicione o seguinte método para o **programa** classe.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    O `ReceiveAsync` método devolve no modo assíncrono à mensagem recebida no momento é recebida pelo dispositivo. Devolve *nulo* após um período de tempo limite specifiable (neste caso, é utilizada a predefinição de um minuto). Quando a aplicação recebe um *nulo*, deve continuar a aguardar para novas mensagens. Este requisito é a razão para o `if (receivedMessage == null) continue` linha.
   
    A chamada para `CompleteAsync()` notifica o IoT Hub de que a mensagem foi processada com êxito. Pode ser removida, com segurança, a mensagem da fila de dispositivo. Se um problema que impediu que a aplicação de dispositivo a concluir o processamento da mensagem, o IoT Hub já proporcionadas-lo novamente. Em seguida, é importante que mensagem processar lógica na aplicação do dispositivo é *idempotent*, para que a mesma mensagem a receber várias vezes produz o mesmo resultado. Uma aplicação pode também temporariamente abandonar uma mensagem, o que resulta no IoT hub mantém a mensagem na fila para consumo futura. Em alternativa, a aplicação pode rejeitar uma mensagem, o que remove permanentemente a mensagem da fila. Para obter mais informações sobre o ciclo de vida da mensagem da nuvem para o dispositivo, consulte o [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > Quando utilizar HTTPS em vez de MQTT ou AMQP como transporte, o `ReceiveAsync` método devolve imediatamente. O padrão suportado para as mensagens da nuvem para o dispositivo com HTTPS é dispositivos ligados intermitentemente ligados que verificam mensagens com pouca frequência (inferior a cada 25 minutos). Emitir HTTPS mais recebe os resultados do IoT Hub limitação de pedidos. Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTPS e a limitação do IoT Hub, consulte o [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 
2. Adicione o seguinte método no **Main** método, imediatamente antes do `Console.ReadLine()` linha:
   
        ReceiveC2dAsync();

> [!NOTE]
> Para sake do simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como término exponencial), como sugerido no artigo do MSDN [processamento de erros transitórios].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo
Nesta secção, escreve uma aplicação de consola .NET que envia mensagens da nuvem para o dispositivo para a aplicação de dispositivo.

1. A atual solução do Visual Studio, crie um projeto de Visual c# de aplicação ambiente de trabalho utilizando o **aplicação de consola** modelo de projeto. Nomeie o projeto **SendCloudToDevice**.
   
    ![Novo projeto no Visual Studio][20]
2. No Explorador de soluções, clique com o botão direito na solução e, em seguida, clique em **gerir pacotes NuGet para solução...** . 
   
    Esta ação abre o **gerir pacotes NuGet** janela.
3. Procurar **Microsoft.Azure.Devices**, clique em **instalar**e aceite os termos de utilização. 
   
    Isto transfere, instala e adiciona uma referência para o [pacote NuGet do SDK do Azure IoT serviço].

4. Adicione a seguinte declaração `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do hub IoT do [introdução ao IoT Hub]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa**:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Este método envia uma nova mensagem da nuvem para o dispositivo para o dispositivo com o ID, `myFirstDevice`. Altere este parâmetro, apenas se a modificação da utilizada na [introdução ao IoT Hub].
7. Por fim, adicione as seguintes linhas ao método **Main**:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. A partir no Visual Studio, clique com o botão direito a solução e selecione **definir projetos de arranque...** . Selecione **vários projetos de arranque**, em seguida, selecione o **iniciar** ação para **ReadDeviceToCloudMessages**, **SimulatedDevice**, e **SendCloudToDevice**.
9. Prima **F5**. Todas as aplicações de três devem começar. Selecione o **SendCloudToDevice** windows e prima **Enter**. Deverá ver a mensagem a ser recebida pela aplicação de dispositivo.
   
   ![Mensagem de receção de aplicação][21]

## <a name="receive-delivery-feedback"></a>Receber comentários de entrega
É possível para confirmações de entrega (ou expiração) do pedido do IoT Hub para cada mensagem da nuvem para o dispositivo. Esta opção permite que a solução de back-end informar facilmente a lógica de repetição ou compensação. Para obter mais informações sobre os comentários da nuvem para o dispositivo, consulte o [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].

Nesta secção, modifique o **SendCloudToDevice** aplicação para solicitar os comentários e recebê-lo a partir do IoT Hub.

1. No Visual Studio, no **SendCloudToDevice** do projeto, adicione o seguinte método para o **programa** classe.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Tenha em atenção de que este padrão de receção é a mesma utilizado para receber mensagens da nuvem para o dispositivo da aplicação de dispositivo.
2. Adicione o seguinte método no **Main** método, direito após o `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` linha:
   
        ReceiveFeedbackAsync();
3. Para pedir os comentários para a entrega de mensagens de nuvem para o dispositivo, tem de especificar uma propriedade no **SendCloudToDeviceMessageAsync** método. Adicione a seguinte linha, imediatamente após o `var commandMessage = new Message(...);` linha:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Executar as aplicações, premindo **F5**. Deverá ver todas as aplicações de três iniciar. Selecione o **SendCloudToDevice** windows e prima **Enter**. Deverá ver a mensagem a ser recebida pela aplicação de dispositivo e, após alguns segundos, a mensagem de comentários que está a ser recebida pelo seu **SendCloudToDevice** aplicação.
   
   ![Mensagem de receção de aplicação][22]

> [!NOTE]
> Para sake do simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como término exponencial), como sugerido no artigo do MSDN [processamento de erros transitórios].
> 
> 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a enviar e receber mensagens da nuvem para o dispositivo. 

Para ver os exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, consulte [Azure IoT Suite].

Para obter mais informações sobre como desenvolver soluções de IoT hub, consulte o [guia para programadores do IoT Hub].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[pacote NuGet do SDK do Azure IoT serviço]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[processamento de erros transitórios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[guia para programadores do IoT Hub]: iot-hub-devguide.md
[introdução ao IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT Suite]: https://docs.microsoft.com/en-us/azure/iot-suite/
[SDKs do Azure IoT device]: iot-hub-devguide-sdks.md