---
title: Utilize as propriedades do IoT Hub do Azure dispositivo duplo (.NET/.NET) | Microsoft Docs
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para configurar dispositivos. Utilizar o dispositivo IoT do Azure SDK para .NET para implementar uma aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que modifica uma configuração de dispositivo utilizando um dispositivo duplo."
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 0bb18f1b289dd01866842a5193437dabdb5fd20b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Utilize as propriedades do pretendido para configurar dispositivos
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No final deste tutorial, terá de duas aplicações de consola .NET:

* **SimulateDeviceConfiguration**, uma aplicação de dispositivo simulado que aguarda uma atualização da configuração pretendida e reporta o estado de um processo de atualização da configuração simulada.
* **SetDesiredConfigurationAndQuery**, uma aplicação de back-end, que define a configuração pretendida num dispositivo e consulta o processo de atualização da configuração.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

Se seguiu o [começar a utilizar dispositivos duplos] [ lnk-twin-tutorial] tutorial, já tiver um IoT hub e uma identidade de dispositivo chamado **myDeviceId**. Nesse caso, pode avançar para o [criar a aplicação de dispositivo simulado] [ lnk-how-to-configure-createapp] secção.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Criar a aplicação de dispositivo simulada
Nesta secção, vai criar uma aplicação de consola de .NET que liga ao seu hub como **myDeviceId**, tem de aguardar durante uma atualização da configuração pretendida e, em seguida, os relatórios de atualizações no processo de atualização de configuração simulada.

1. No Visual Studio, crie um novo projeto do Visual c# clássico ambiente de trabalho Windows utilizando o **aplicação de consola** modelo de projeto. Nomeie o projeto **SimulateDeviceConfiguration**.
   
    ![Nova aplicação de dispositivo Visual c# clássico do Windows][img-createdeviceapp]

1. No Explorador de soluções, clique com botão direito do **SimulateDeviceConfiguration** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. No **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **microsoft.azure.devices.client**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices.Client** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [dispositivos IoT do Azure SDK] [ lnk-nuget-client-sdk] NuGet pacote e as respetivas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet][img-clientnuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. Adicione o seguinte método à classe **Programa**:
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código mostrado acima, inicializa a **cliente** objeto e, em seguida, obtém o dispositivo duplo para **myDeviceId**.

1. Adicione o seguinte método para o **programa** classe. Este método define os valores iniciais de telemetria no dispositivo local e, em seguida, atualiza o dispositivo duplo.

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Adicione o seguinte método para o **programa** classe. Esta é uma chamada de retorno que irá detetar uma alteração na *pretendido propriedades* no dispositivo duplo.

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Este método atualiza as propriedades comunicadas no objeto de duplo dispositivo local com o pedido de atualização de configuração e define o estado para **pendente**, em seguida, atualiza o dispositivo duplo no serviço. Depois de atualizar com êxito o dispositivo duplo, conclui a alteração de configuração ao chamar o método `CompleteConfigChange` descrito no próximo ponto.

1. Adicione o seguinte método para o **programa** classe. Este método simula uma reposição do dispositivo, em seguida, as atualizações locais comunicadas propriedades a definir o estado para **êxito** e remove o **pendingConfig** elemento. Em seguida, atualiza o dispositivo duplo no serviço. 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Por fim, adicione as seguintes linhas à **Main** método:

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > Este tutorial não simular nenhum comportamento de atualizações de configuração em simultâneo. Alguns processos de atualização de configuração poderão acomodar as alterações de configuração de destino enquanto a atualização está a ser executado, alguns poderão ter de fila-los e algumas rejeitá-las com uma condição de erro. Certifique-se a ter em consideração o comportamento pretendido para o processo de configuração específica e adicione a lógica adequada antes de iniciar a alteração da configuração.
   > 
   > 
1. Compilar a solução e, em seguida, execute a aplicação de dispositivo a partir do Visual Studio ao clicar em **F5**. Na consola do resultado, deverá ver as mensagens com a indicação de que o seu dispositivo simulado está a obter o dispositivo duplo, configurar a telemetria e a aguardar a alteração de propriedade pretendido. Manter a aplicação em execução.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, irá criar uma aplicação de consola .NET que atualiza o *pretendido propriedades* no dispositivo duplo associado **myDeviceId** com um novo objeto de configuração de telemetria. Em seguida, consulta os dispositivos duplos armazenados no IoT hub e mostra a diferença entre as configurações desejadas e comunicadas do dispositivo.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SetDesiredConfigurationAndQuery**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createapp]
1. No Explorador de soluções, clique com botão direito do **SetDesiredConfigurationAndQuery** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.
   
    ![Janela Gestor de Pacote NuGet][img-servicenuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adicione o seguinte método à classe **Programa**:
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    O **registo** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. Este código inicializa a **registo** objeto, obtém o dispositivo duplo para **myDeviceId**e, em seguida, atualiza as respetivas propriedades pretendidas com um novo objeto de configuração de telemetria.
    Depois disso, este consulta os dispositivos duplos armazenados no IoT hub a cada 10 segundos e imprime as configurações de telemetria pretendido e comunicados. Consulte o [idioma de consulta do IoT Hub] [ lnk-query] para saber como gerar relatórios avançados em todos os seus dispositivos.
   
   > [!IMPORTANT]
   > Esta aplicação consulta IoT Hub a cada 10 segundos para fins de ilustrativa. Utilize consultas para gerar relatórios de destinada ao utilizador entre vários dispositivos e não para detetar as alterações. Se a sua solução requer notificações em tempo real de eventos do dispositivo, utilize [duplo notificações][lnk-twin-notifications].
   > 
   > 
1. Por fim, adicione as seguintes linhas ao método **Main**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. No Explorador de soluções, abra o **definir projetos de arranque...**  e certifique-se a **ação** para **SetDesiredConfigurationAndQuery** projeto é **iniciar**. Compilar a solução.
1. Com **SimulateDeviceConfiguration** execução de aplicações do dispositivo, executar a aplicação de serviço do Visual Studio com **F5**. Deverá ver a alteração de configuração comunicada **pendente** para **êxito** com o Active Directory novo enviar uma frequência de cinco minutos em vez de 24 horas.

 ![Dispositivos configurados com êxito][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Não há um atraso de até um minuto entre a operação de relatório do dispositivo e o resultado da consulta. Isto é permitir que a infraestrutura de consulta para trabalhar em grande escala. Para obter vistas consistentes de utilização de duplo um único dispositivo de **getDeviceTwin** método o **registo** classe.
   > 
   > 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou uma configuração desejada como *pretendido propriedades* da solução de back-end e escreveu uma aplicação de dispositivo para detetar essa alteração e simular um processo de atualização de vários passos comunicar o seu estado através as propriedades que relatados.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* agendar ou efetuar operações em grandes conjuntos de dispositivos Consulte o [agenda e as tarefas de difusão] [ lnk-schedule-jobs] tutorial.
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador), com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
