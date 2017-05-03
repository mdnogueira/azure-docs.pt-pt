---
title: "Introdução ao hub IoT do Azure (.NET) | Microsoft Docs"
description: "Como enviar mensagens do dispositivo para a cloud a partir de um dispositivo para um hub IoT do Azure com os SDKs do Azure IoT para .NET. Cria uma aplicação de dispositivo simulada para enviar mensagens, uma aplicação de serviço para registar o seu dispositivo no registo de identidade e uma aplicação de serviço para ler as mensagens do dispositivo para a cloud a partir do hub IoT."
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
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 33ddab887ade0d788129367eec4db7e70f35e0b9
ms.lasthandoff: 04/25/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-net"></a>Ligar o dispositivo simulado ao seu hub IoT com o .NET
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, terá três aplicações de consola .NET:

* **CreateDeviceIdentity**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar a aplicação do dispositivo simulado.
* **ReadDeviceToCloudMessages**, que apresenta a telemetria enviada pela aplicação do dispositivo simulado.
* **SimulatedDevice**, que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo através do protocolo MQTT.

> [!NOTE]
> Para informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end, veja [Azure IoT SDKs][lnk-hub-sdks].
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu hub IoT e tem agora o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo
Nesta secção, vai criar uma aplicação de consola .NET que cria uma identidade de dispositivo no registo de identidade do seu Hub IoT. Não é possível ligar um dispositivo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, veja a secção "Identity registry" (Registo de identidades) do [Hub IoT developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity]. Ao executar esta aplicação de consola, será gerado um ID de dispositivo único e uma chave que o seu dispositivo pode utilizar para identificar-se quando enviar mensagens do dispositivo para a nuvem ao IoT Hub.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows a uma solução nova, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua ao projeto o nome **CreateDeviceIdentity** e atribua à solução o nome **IoTHubGetStarted**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10]
2. No Explorador de Soluções, clique com o botão direito do rato no projeto **CreateDeviceIdentity** e, em seguida, clique em **Gerir Pacotes de NuGet**.
3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.
   
    ![Janela Gestor de Pacote NuGet][11]
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa**:
   
        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }
   
    Este método cria uma identidade de dispositivo com o ID **myFirstDevice**. (Se o ID desse dispositivo já existir no registo de identidade, o código apenas obtém as informações do dispositivo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Esta chave vai ser utilizada na aplicação do dispositivo simulado para ligar ao seu hub IoT.
7. Por fim, adicione as seguintes linhas ao método **Main**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Execute esta aplicação e tome nota da chave do dispositivo.
   
    ![Chave do dispositivo gerada pela aplicação][12]

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity].
> 
> 

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Receber mensagens dispositivo-nuvem
Nesta secção, irá criar uma aplicação de consola do .NET que lê mensagens do dispositivo para a cloud a partir do Hub IoT. Um hub IoT expõe um ponto final compatível com os [Hubs de Eventos do Azure][lnk-event-hubs-overview], o que lhe permite ler mensagens do dispositivo para a cloud. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implementação com débito elevado. Para aprender como processar mensagens do dispositivo para a cloud em escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial]. Para obter mais informações sobre como processar mensagens a partir do Event Hubs, veja o tutorial [Introdução ao Event Hubs][lnk-eventhubs-tutorial]. (Este tutorial aplica-se aos pontos finais do Hub IoT compatíveis com o Hub de Eventos.)

> [!NOTE]
> O ponto final compatível com o Hub de Eventos para a leitura de mensagens do dispositivo para a nuvem utiliza sempre o protocolo AMQP.
> 
> 

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à solução atual, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **ReadDeviceToCloudMessages** ao projeto.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10a]
2. No Explorador de Soluções, clique com o botão direito do rato no projeto **ReadDeviceToCloudMessages** e, em seguida, clique em **Gerir Pacotes de NuGet**.
3. Na janela **Gestor de Pacote NuGet**, procure **WindowsAzure.ServiceBus**, selecione **Instalar** e aceite os termos de utilização. Este procedimento permite transferir, instalar e adicionar uma referência ao [Azure Service Bus][lnk-servicebus-nuget], com todas as respetivas dependências. Este pacote permite à aplicação estabelecer a ligação ao ponto final compatível com o Hub de Eventos no seu hub IoT.
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.ServiceBus.Messaging;
        using System.Threading;
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção "Create an IoT hub (Criar um Hub IoT)".
   
        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;
6. Adicione o seguinte método à classe **Programa**:
   
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
   
    Este método utiliza uma instância **EventHubReceiver** para receber mensagens de todas as partições de receção dispositivo para nuvem do IoT Hub. Repare na maneira como transmite um `DateTime.Now` parâmetro ao criar o objeto **EventHubReceiver** de modo a apenas receber mensagens enviadas depois de iniciar. Este filtro é útil num ambiente de teste para que possa ver o conjunto atual de mensagens. Num ambiente de produção, deve certificar-se de que o seu código processa todas as mensagens. Para obter mais informações, veja o tutorial [Como processar mensagens do dispositivo para a cloud do Hub IoT][lnk-process-d2c-tutorial].
7. Por fim, adicione as seguintes linhas ao método **Main**:
   
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

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, irá criar uma aplicação de consola do .NET que simula um dispositivo que envia mensagens do dispositivo para a cloud para um Hub IoT.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à solução atual, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **SimulatedDevice** ao projeto.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10b]
2. No Explorador de Soluções, clique com o botão direito do rato no projeto **SimulatedDevice** e, em seguida, clique em **Gerir Pacotes de NuGet**.
3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **Microsoft.Azure.Devices.Client**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao [pacote NuGet do SDK do dispositivo do Azure IoT][lnk-device-nuget] e às respetivas dependências.
4. Adicione a seguinte declaração `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
5. Adicione os seguintes campos à classe **Programa**. Substitua os valores do marcador de posição pelo nome de anfitrião do Hub IoT que obteve na secção "Create an IoT hub (Criar um Hub IoT)" e a chave de dispositivo obtida na secção "Create a device identity (Criar uma identidade de dispositivo)".
   
        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";
6. Adicione o seguinte método à classe **Programa**:
   
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
                var message = new Message(Encoding.ASCII.GetBytes(messageString));
                message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");
   
                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);
   
                await Task.Delay(1000);
            }
        }
   
    Este método envia uma nova mensagem dispositivo para nuvem a cada segundo. A mensagem contém um objeto JSON serializado com o ID de dispositivo e números gerados aleatoriamente para simular um sensor de temperatura, e um sensor de humidade.
7. Por fim, adicione as seguintes linhas ao método **Main**:
   
        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);
   
        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();
   
   Por predefinição, o método **Criar** cria uma instância **DeviceClient** que utiliza o protocolo AMQP para comunicar com o IoT Hub. Para utilizar o protocolo MQTT ou HTTP, substitua o método **Criar** que lhe permite especificar o protocolo. Se utilizar o protocolo HTTP, deverá ainda adicionar o pacote NuGet **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o espaço de nomes **System.Net.Http.Formatting**.

Este tutorial guia-o pelos passos para criar uma aplicação do dispositivo simulado pelo Hub IoT. Pode ainda utilizar a extensão do Visual Studio [Serviço Ligado para o Hub IoT do Azure][lnk-connected-service] para adicionar o código necessário à aplicação do seu dispositivo.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.
> 
> 

## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. No Visual Studio, no Explorador de Soluções, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir projetos de Arranque**. Selecione **Vários projetos de arranque** e, em seguida, selecione **Iniciar** como a ação para ambos os projetos **ReadDeviceToCloudMessages** e **SimulatedDevice**.
   
    ![Propriedades do projeto de arranque][41]
2. Prima **F5** para começar a executar ambas as aplicações. O resultado da consola da aplicação **SimulatedDevice** apresenta as mensagens que a aplicação do dispositivo simulado envia ao seu Hub IoT. O resultado da consola da aplicação **ReadDeviceToCloudMessages** apresenta as mensagens que recebe o seu Hub IoT.
   
    ![Resultado da consola das aplicações][42]
3. O mosaico **Utilização** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas ao Hub IoT:
   
    ![Mosaico Utilização do Portal do Azure][43]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidade do hub IoT. Utilizou esta identidade de dispositivo para que a aplicação do dispositivo simulado pudesse enviar mensagens do dispositivo para a cloud ao Hub IoT. Também criou uma aplicação que apresenta as mensagens recebidas pelo Hub IoT. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Ligar o seu dispositivo][lnk-connect-device]
* [Getting started with device management (Introdução à gestão de dispositivos)][lnk-device-management]
* [Introdução ao SDK do Gateway do IoT][lnk-gateway-SDK]

Para saber como expandir a sua solução de IoT e processar mensagens do dispositivo para a cloud em escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial].

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

