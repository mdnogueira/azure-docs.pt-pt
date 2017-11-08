---
title: "Introdução à gestão de dispositivos do IoT Hub do Azure (.NET/.NET) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos do IoT Hub do Azure para iniciar um reinício do dispositivo remoto. Utilizar o dispositivo IoT do Azure SDK para .NET para implementar uma aplicação de dispositivo simulado que inclui um método direto e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que invoca o método direto."
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: v-jamebr
ms.openlocfilehash: 3af7fbfb9740e00d9ff9c2b077cb444a8057b8c3
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="get-started-with-device-management-netnet"></a>Introdução à gestão de dispositivos (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no seu IoT hub.
* Crie uma aplicação de dispositivo simulado que contém um método direto que reinicia nesse dispositivo. Métodos diretos são invocados a partir da nuvem.
* Crie uma aplicação de consola .NET que chama o método direto reinício na aplicação do dispositivo simulado através do seu IoT hub.

No final deste tutorial, tem duas aplicações de consola .NET:

* **SimulateManagedDevice**, que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente, recebe um método direta de reinício, simula reiniciar o computador físico, relatórios e a hora para o último reinício.
* **TriggerReboot**, que chama um método direto na aplicação do dispositivo simulado, mostra a resposta e apresenta a atualização comunicado propriedades.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar um reinício remoto no dispositivo com um método direto
Nesta secção, criar uma aplicação de consola do .NET (utilizando c#) inicia um reinício remoto num dispositivo utilizando um método direto. A aplicação utiliza consultas de duplo de dispositivo para detetar a última vez de reiniciar o computador para que o dispositivo.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows a uma solução nova, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Nomeie o projeto **TriggerReboot**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createserviceapp]

2. No Explorador de soluções, clique com botão direito do **TriggerReboot** projeto e, em seguida, clique em **gerir pacotes NuGet**.
3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.

    ![Janela Gestor de Pacote NuGet][img-servicenuget]
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do IoT Hub para o hub que criou na secção "Criar um IoT hub". 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Adicione o seguinte método para o **programa** classe.  Este código obtém o dispositivo duplo para o dispositivo rebooting e produz as propriedades que relatados.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Adicione o seguinte método para o **programa** classe.  Este código inicia o reinício do dispositivo utilizando um método direto.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Por fim, adicione as seguintes linhas ao método **Main**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Compilar a solução.

> [!NOTE]
> Neste tutorial efetua uma única consulta para propriedades comunicado do dispositivo. No código de produção, recomendamos a consulta para detetar alterações nas propriedades de comunicados.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, irá

* Criar uma aplicação de consola do .NET que responde a um método direto chamado pela nuvem
* Acionar um reinício do dispositivo simulado
* Utilize as propriedades que relatados para ativar as consultas de duplo de dispositivo identificar dispositivos e quando duram reiniciado

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SimulateManagedDevice**.
   
    ![Nova aplicação de dispositivo Visual c# clássico do Windows][img-createdeviceapp]
    
2. No Explorador de soluções, clique com botão direito do **SimulateManagedDevice** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
3. No **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **microsoft.azure.devices.client**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices.Client** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [dispositivos IoT do Azure SDK] [ lnk-nuget-client-sdk] NuGet pacote e as respetivas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet][img-clientnuget]
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Adicione o seguinte para implementar o método direto no dispositivo:

        static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
        {
            // In a production device, you would trigger a reboot scheduled to start after this method returns
            // For this sample, we simulate the reboot by writing to the console and updating the reported properties 
            try
            {
                Console.WriteLine("Rebooting!");

                // Update device twin with reboot time. 
                TwinCollection reportedProperties, reboot, lastReboot;
                lastReboot = new TwinCollection();
                reboot = new TwinCollection();
                reportedProperties = new TwinCollection();
                lastReboot["lastReboot"] = DateTime.Now;
                reboot["reboot"] = lastReboot;
                reportedProperties["iothubDM"] = reboot;
                Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }

            string result = "'Reboot started.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

7. Por fim, adicione o seguinte código para o **Main** método para abrir a ligação ao seu IoT hub e inicializar o serviço de escuta do método:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "reboot" method
            Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
            Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "reboot" handler
            Client.SetMethodHandlerAsync("reboot", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
8. No Visual Studio Explorador de soluções, faça duplo clique sua solução e, em seguida, clique em **definir projetos de arranque...** . Selecione **projeto de arranque único**e, em seguida, selecione o **SimulateManagedDevice** projeto no menu pendente. Compilar a solução.       

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.


## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.
1. Executar a aplicação de dispositivo .NET **SimulateManagedDevice**.  Clique com botão direito do **SimulateManagedDevice** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**. Deve começar a escutar para chamadas de método do seu IoT Hub: 

2. Agora que o dispositivo está ligado e a aguardar para invocações de método, execute o .NET **TriggerReboot** aplicação ao invocar o método de reinício na aplicação do dispositivo simulado. Clique com botão direito do **TriggerReboot** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**. Deverá ver "Rebooting!" escritas no **SimulatedManagedDevice** consola e as propriedades comunicadas do dispositivo, que incluem o último reinício tempo, escrito no **TriggerReboot** consola.
   
    ![Aplicação de serviço e dispositivo executar][img-combinedrun]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
