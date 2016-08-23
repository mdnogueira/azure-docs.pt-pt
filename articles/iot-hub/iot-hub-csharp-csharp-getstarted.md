<properties
    pageTitle="IoT Hub do Azure para uma introdução à C# | Microsoft Azure"
    description="IoT Hub do Azure com tutorial de introdução à C# Utilize o IoT Hub e a C# do Azure com os SDKs IoT do Microsoft Azure para implementar uma solução Internet das Coisas."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# Introdução ao IoT Hub do Azure para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, terá três aplicações de consola do Windows:

* **CreateDeviceIdentity**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar o seu dispositivo simulado.
* **ReadDeviceToCloudMessages**, que apresenta a telemetria enviada pelo seu dispositivo simulado.
* **SimulatedDevice**, que liga ao seu IoT Hub com a identidade de dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo através do protocolo AMQPS.

> [AZURE.NOTE] Para obter informações sobre os vários SDKs que pode utilizar para criar ambas as aplicações a executar em dispositivos e a solução de back-end, consulte o artigo [SDKs do IoT Hub][Ink-hub-sdks].

Para concluir este tutorial, irá precisar do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu IoT Hub e tem agora o nome de anfitrião e a cadeia de ligação de que precisa para concluir este tutorial.

## Criar uma identidade de dispositivo

Nesta secção, irá criar uma aplicação de consola do Windows que cria uma nova identidade de dispositivo no registo de identidade do seu IoT Hub. Não é possível ligar um dispositivo ao IoT Hub exceto se tiver uma entrada no registo de identidade de dispositivo. Para mais informações, consulte a secção "Registo de identidade de Dispositivo" do [Guia para Programadores do IoT Hub][Ink-devguide-identity]. Ao executar esta aplicação de consola, será gerado um ID de dispositivo único e uma chave que o seu dispositivo pode utilizar para identificar-se quando enviar mensagens do dispositivo para a nuvem ao IoT Hub.

1. No Visual Studio, adicione um novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução utilizando o modelo de projeto **Aplicação de Consola**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **CreateDeviceIdentity** ao projeto.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10]

2. No Explorador de Soluções, clique com o botão direito do rato no projeto **CreateDeviceIdentity** e, em seguida, clique em **Gerir Pacotes de NuGet**.

3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Com esta ação, irá transferir, instalar e adicionar uma referência ao pacote NuGet do [SDK do serviço IoT do Microsoft Azure][Ink-nuget-service-sdk] e às respetivas dependências.

    ![Janela Gestor de Pacote NuGet][11]

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação para o IoT Hub que criou na secção anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

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

    Este método cria uma nova identidade de dispositivo com o ID **myFirstDevice**. (Se o ID desse dispositivo já existir no registo, o código apenas obtém as informações do dispositivo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Irá utilizar esta chave no dispositivo simulado para ligar ao seu IoT Hub.

7. Por fim, adicione as seguintes linhas ao método **Main**:

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Execute esta aplicação e tome nota da chave do dispositivo.

    ![Chave do dispositivo gerada pela aplicação][12]

> [AZURE.NOTE] O registo de identidade do IoT Hub apenas armazena identidades de dispositivo para permitir um acesso seguro ao Hub. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, consulte o artigo [Guia para Programadores do IoT Hub][Ink-devguide-identity].

## Receber mensagens dispositivo-nuvem

Nesta secção, irá criar uma aplicação de consola do Windows que lê mensagens do dispositivo para a nuvem a partir do IoT Hub. Um IoT Hub expõe um ponto final compatível [Event Hubs do Azure][Ink-event-hubs-overview] que lhe permite ler mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implementação com débito elevado. Para saber como processar mensagens do dispositivo para a nuvem à escala, veja o tutorial [Processar mensagens do dispositivo para a nuvem][Ink-process-d2c-tutorial]. Para obter mais informações sobre como processar mensagens a partir do Event Hubs, veja o tutorial [Introdução ao Event Hubs][Ink-eventhubs-tutorial]. (Este tutorial aplica-se aos pontos finais do IoT Hub compatíveis com o Event Hubs.)

> [AZURE.NOTE] O ponto final compatível com o Event Hubs para a leitura de mensagens do dispositivo para a nuvem utiliza sempre o protocolo AMQPS.

1. No Visual Studio, adicione um novo projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução utilizando o modelo de projeto **Aplicação de Consola**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **ReadDeviceToCloudMessages** ao projeto.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10]

2. No Explorador de Soluções, clique com o botão direito do rato no projeto **ReadDeviceToCloudMessages** e, em seguida, clique em **Gerir Pacotes de NuGet**.

3. Na janela **Gestor de Pacote NuGet**, procure **WindowsAzure.ServiceBus**, selecione **Instalar** e aceite os termos de utilização. Esta ação permite transferir, instalar e adicionar uma referência ao [Service Bus do Azure][Ink-servicebus-nuget], com todas as respetivas dependências. Este pacote permite à aplicação estabelecer a ligação ao ponto final compatível com o Event Hubs no seu IoT Hub.

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação para o IoT Hub que criou na secção "Criar um IoT Hub".

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

    Este método utiliza uma instância **EventHubReceiver** para receber mensagens de todas as partições de receção dispositivo para nuvem do IoT Hub. Repare na maneira como transmite um `DateTime.Now` parâmetro ao criar o objeto **EventHubReceiver** de modo a apenas receber mensagens enviadas depois de iniciar. Esta ação torna-se útil num ambiente de teste já que pode ver o conjunto atual de mensagens. No entanto, num ambiente de produção, deverá certificar-se de que o seu código processa todas as mensagens. Para obter mais informações, veja o tutorial [Como processar mensagens do dispositivo para a nuvem do IoT Hub][Ink-process-d2c-tutorial].

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

## Criar uma aplicação de dispositivo simulada

Nesta secção, irá criar uma aplicação de consola do Windows que simula um dispositivo que envia mensagens do dispositivo para a nuvem a um IoT Hub.

1. No Visual Studio, adicione um novo projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução utilizando o modelo de projeto **Aplicação de Consola**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Atribua o nome **SimulatedDevice** ao projeto.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10]

2. No Explorador de Soluções, clique com o botão direito do rato no projeto **SimulatedDevice** e, em seguida, clique em **Gerir Pacotes de NuGet**.

3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **Microsoft.Azure.Devices.Client**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de utilização. Com esta ação, ira transferir, instalar e adicionar uma referência ao [ pacote NuGet do SDK do Dispositivo IoT do Azure][lnk-device-nuget] e às respetivas dependências.

4. Adicione a seguinte declaração `using` na parte superior do ficheiro **Program.cs**:

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Adicione os seguintes campos à classe **Programa**. Substitua os valores do marcador de posição pelo nome de anfitrião do IoT Hub que obteve na secção "Criar um IoT Hub" e a chave de dispositivo obtida na secção "Criar uma identidade de dispositivo".

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Adicione o seguinte método à classe **Programa**:

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Este método envia uma nova mensagem dispositivo para nuvem a cada segundo. A mensagem contém um objeto JSON serializado com o ID de dispositivo e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

7. Por fim, adicione as seguintes linhas ao método **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Por predefinição, o método **Criar** cria uma instância **DeviceClient** que utiliza o protocolo AMQP para comunicar com o IoT Hub. Para utilizar o protocolo HTTPS, substitua o método **Criar** que lhe permite especificar o protocolo. Se utilizar o protocolo HTTPS, deverá ainda adicionar o pacote NuGet **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o espaço de nomes **System.Net.Http.Formatting**.

Este tutorial guia-o pelos passos para criar um dispositivo-cliente do IoT Hub. Pode ainda utilizar a extensão do Visual Studio [Serviço Ligado para o IoT Hub do Azure][Ink-connected-service] para adicionar o código necessário à aplicação do seu dispositivo-cliente.


> [AZURE.NOTE] Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Processamento de Erros Transitórios][Ink-transitório-lento] da MSDN.

## Executar as aplicações

Pode agora executar as aplicações.

1.  No Visual Studio, no Explorador de Soluções, clique com o botão direito do rato na sua solução e, em seguida, clique em **Definir projetos de Arranque**. Selecione **Vários projetos de arranque** e, em seguida, selecione **Iniciar** como a ação para ambos os projetos **ReadDeviceToCloudMessages** e **SimulatedDevice**.

    ![Propriedades do projeto de arranque][41]

2.  Prima **F5** para começar a executar ambas as aplicações. O resultado da consola da aplicação **SimulatedDevice** apresenta as mensagens que o seu dispositivo simulado envia ao seu IoT Hub. O resultado da consola da aplicação **ReadDeviceToCloudMessages** apresenta as mensagens que recebe o seu Hub IoT.

    ![Resultado da consola das aplicações][42]

3. O mosaico **Utilização** no [Portal do Azure][Ink-portal] mostra o número de mensagens enviadas ao Hub:

    ![Mosaico Utilização do Portal do Azure][43]


## Passos seguintes

Neste tutorial, configurou um novo IoT Hub no Portal e, de seguida, criou uma identidade de dispositivo no registo de identidade do Hub. Utilizou esta identidade de dispositivo para que a aplicação do dispositivo simulado pudesse enviar mensagens do dispositivo para a nuvem ao Hub. Também criou uma aplicação que apresenta as mensagens recebidas através do Hub. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

- [Ligar o seu dispositivo][lnk-connect-device]
- [Introdução à gestão de dispositivos][lnk-device-management]
- [Introdução ao SDK do Gateway][lnk-gateway-SDK]

Para saber como expandir a sua solução IoT e processar mensagens do dispositivo para a nuvem à escala, veja o tutorial [Processar mensagens do dispositivo para a nuvem][Ink-process-d2c-tutorial].

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[Ink-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[Ink-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Ink-transitório-lento]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Aug16_HO1-->


