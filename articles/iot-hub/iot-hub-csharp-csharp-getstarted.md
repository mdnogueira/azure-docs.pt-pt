---
title: "Introdução ao hub IoT do Azure (.NET) | Microsoft Docs"
description: "Aprenda a enviar mensagens do dispositivo para a cloud para um hub IoT do Azure com os SDKs para .NET. Crie um dispositivo simulado e aplicações de serviço para registar o seu dispositivo, enviar mensagens e ler as mensagens do hub IoT."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f16f23928977ef6bf53f41c79375290da70a2fa
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>Ligue o seu dispositivo ao seu hub IoT através de .NET

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, terá três aplicações de consola .NET:

* **CreateDeviceIdentity**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar a aplicação do dispositivo.
* **ReadDeviceToCloudMessages**, que apresenta a telemetria enviada pela aplicação do dispositivo.
* **SimulatedDevice**, que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo através do protocolo MQTT.

Pode transferir ou clonar a solução do Visual Studio, que contém as três aplicações do Github.

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs][lnk-hub-sdks].

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu hub IoT e tem agora o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir este tutorial.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Receber mensagens dispositivo-nuvem
Nesta secção, irá criar uma aplicação de consola do .NET que lê mensagens do dispositivo para a cloud a partir do Hub IoT. Um hub IoT expõe um ponto final compatível com os [Hubs de Eventos do Azure][lnk-event-hubs-overview], o que lhe permite ler mensagens do dispositivo para a cloud. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implementação com débito elevado. Para aprender como processar mensagens do dispositivo para a cloud em escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial]. Para obter mais informações sobre como processar mensagens a partir do Event Hubs, veja o tutorial [Introdução ao Event Hubs][lnk-eventhubs-tutorial]. (Este tutorial aplica-se aos pontos finais do Hub IoT compatíveis com o Hub de Eventos.)

> [!NOTE]
> O ponto final compatível com o Hub de Eventos para a leitura de mensagens do dispositivo para a nuvem utiliza sempre o protocolo AMQP.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à solução atual, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **ReadDeviceToCloudMessages** ao projeto.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10a]

2. No Explorador de Soluções, clique com o botão direito do rato no projeto **ReadDeviceToCloudMessages** e, em seguida, clique em **Gerir Pacotes de NuGet**.

3. Na janela **Gestor de Pacote NuGet**, procure **WindowsAzure.ServiceBus**, selecione **Instalar** e aceite os termos de utilização. Este procedimento permite transferir, instalar e adicionar uma referência ao [Azure Service Bus][lnk-servicebus-nuget], com todas as respetivas dependências. Este pacote permite à aplicação estabelecer a ligação ao ponto final compatível com o Hub de Eventos no seu hub IoT.

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção "Create an IoT hub (Criar um Hub IoT)".

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

6. Adicione o seguinte método à classe **Programa**:

    ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
    ```

    Este método utiliza uma instância **EventHubReceiver** para receber mensagens de todas as partições de receção dispositivo para nuvem do IoT Hub. Repare na maneira como transmite um `DateTime.Now` parâmetro ao criar o objeto **EventHubReceiver** de modo a apenas receber mensagens enviadas depois de iniciar. Este filtro é útil num ambiente de teste para que possa ver o conjunto atual de mensagens. Num ambiente de produção, deve certificar-se de que o seu código processa todas as mensagens. Para obter mais informações, veja o tutorial [How to process IoT Hub device-to-cloud messages][lnk-process-d2c-tutorial] (Como processar mensagens do dispositivo para a cloud do Hub IoT).

7. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
    ```

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, irá criar uma aplicação de consola do .NET que simula um dispositivo que envia mensagens do dispositivo para a cloud para um Hub IoT.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à solução atual, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **SimulatedDevice** ao projeto.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10b]

2. No Explorador de Soluções, clique com o botão direito do rato no projeto **SimulatedDevice** e, em seguida, clique em **Gerir Pacotes de NuGet**.

3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **Microsoft.Azure.Devices.Client**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao [pacote NuGet do SDK do dispositivo do Azure IoT][lnk-device-nuget] e às respetivas dependências.

4. Adicione a seguinte declaração `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua `{iot hub hostname}` pelo nome de anfitrião do hub IoT que obteve na secção "Criar um hub IoT". Substitua `{device key}` pela chave de dispositivo que obteve na secção "Criar uma identidade de dispositivo".

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

6. Adicione o seguinte método à classe **Programa**:

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    Este método envia uma nova mensagem dispositivo para nuvem a cada segundo. A mensagem contém um objeto JSON serializado com o ID de dispositivo e números gerados aleatoriamente para simular um sensor de temperatura, e um sensor de humidade.

7. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);

    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    Por predefinição, o método **Criar** numa aplicação .NET Framework cria uma instância **DeviceClient** que utiliza o protocolo AMQP para comunicar com o Hub IoT. Para utilizar o protocolo MQTT ou HTTPS, substitua o método **Criar** que lhe permite especificar o protocolo. Por predefinição, os clientes UWP e PCL utilizam o protocolo HTTPS. Se utilizar o protocolo HTTPS, deverá ainda adicionar o pacote NuGet **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o espaço de nomes **System.Net.Http.Formatting**.

Este tutorial guia-o pelos passos para criar uma aplicação de dispositivo do Hub IoT. Pode ainda utilizar a extensão do Visual Studio [Serviço Ligado para o Hub IoT do Azure][lnk-connected-service] para adicionar o código necessário à aplicação do seu dispositivo.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. No Visual Studio, no Explorador de Soluções, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir projetos de Arranque**. Selecione **Vários projetos de arranque** e, em seguida, selecione **Iniciar** como a ação para ambos os projetos **ReadDeviceToCloudMessages** e **SimulatedDevice**.

    ![Propriedades do projeto de arranque][41]

2. Prima **F5** para começar a executar ambas as aplicações. O resultado da consola da aplicação **SimulatedDevice** apresenta as mensagens que a aplicação do dispositivo envia ao seu Hub IoT. O resultado da consola da aplicação **ReadDeviceToCloudMessages** apresenta as mensagens que recebe o seu Hub IoT.

    ![Resultado da consola das aplicações][42]

3. O mosaico **Utilização** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas ao Hub IoT:

    ![Mosaico Utilização do Portal do Azure][43]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidade do hub IoT. Utilizou esta identidade de dispositivo para que a aplicação de dispositivo pudesse enviar mensagens do dispositivo para a cloud ao Hub IoT. Também criou uma aplicação que apresenta as mensagens recebidas pelo Hub IoT.

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Ligar o seu dispositivo][lnk-connect-device]
* [Getting started with device management (Introdução à gestão de dispositivos)][lnk-device-management]
* [Introdução ao IoT Edge][lnk-iot-edge]

Para saber como expandir a sua solução de IoT e processar mensagens do dispositivo para a cloud em escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial].

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
