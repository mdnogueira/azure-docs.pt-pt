---
title: "Atualização de firmware do dispositivo com o Azure IoT Hub (.NET/.NET) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos no IoT Hub do Azure para iniciar uma atualização de firmware do dispositivo. Utilizar o dispositivo IoT do Azure SDK para .NET para implementar uma aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que aciona a atualização de firmware."
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: bd0a227861d75dc66af8fb4865a17a3b6d0f70ba
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Utilize a gestão de dispositivos para iniciar uma atualização de firmware do dispositivo (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introdução
No [introdução à gestão de dispositivos] [ lnk-dm-getstarted] tutorial, vimos como utilizar o [dispositivo duplo] [ lnk-devtwin] e [direcionar métodos] [ lnk-c2dmethod] primitivos reiniciar remotamente um dispositivo. Este tutorial utiliza os mesmos primitivos do IoT Hub e mostra como efetuar uma atualização de firmware simulada ponto-a-ponto.  Este padrão é utilizado na implementação de atualização de firmware para o [exemplo de implementação do dispositivo Raspberry Pi][lnk-rpi-implementation].

Este tutorial mostrar-lhe como:

* Criar uma aplicação de consola .NET que chama o **firmwareUpdate** método direto na aplicação do dispositivo simulado através do seu IoT hub.
* Criar uma aplicação de dispositivo simulado que implementa um **firmwareUpdate** método direto. Este método inicia um processo de fase multi que aguarda para transferir a imagem de firmware, transfere a imagem de firmware e, finalmente, aplica-se a imagem do firmware. Durante cada fase da atualização, o dispositivo utiliza as propriedades que relatados para elaborar relatórios sobre o progresso.

No final deste tutorial, terá de uma aplicação de consola .NET (c#) do dispositivo e uma aplicação de back-end do .NET (c#) consola:

* **SimulatedDeviceFwUpdate**, que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente, recebe o **firmwareUpdate** direcionar o método, é executada através de um processo com múltiplos estado para simular uma atualização de firmware incluindo: a aguardar que a transferência da imagem, transferir a nova imagem e, finalmente, aplicar a imagem.

* **TriggerFWUpdate**, que utiliza o SDK do serviço para invocar remotamente o **firmwareUpdate** direta método no dispositivo simulado, mostra a resposta e periodicamente (cada 500ms) apresenta a atualização comunicado Propriedades.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Siga o [introdução à gestão de dispositivos](iot-hub-csharp-csharp-device-management-get-started.md) artigo para criar o hub IoT e obter a cadeia de ligação do IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Acionar uma atualização de firmware remota no dispositivo com um método direto
Nesta secção, criar uma aplicação de consola do .NET (utilizando c#) inicia uma atualização de firmware remota num dispositivo. A aplicação utiliza um método direto para iniciar a atualização e utiliza consultas de duplo de dispositivo para obter periodicamente o estado da atualização de firmware do Active Directory.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **TriggerFWUpdate**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createserviceapp]

2. No Explorador de soluções, clique com botão direito do **TriggerFWUpdate** projeto e, em seguida, clique em **gerir pacotes NuGet**.
3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.

    ![Janela Gestor de Pacote NuGet][img-servicenuget]
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua os vários valores de marcador de posição pela cadeia de ligação do IoT Hub para o hub que criou na secção anterior e o ID do seu dispositivo.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Adicione o seguinte método para o **programa** classe. Este método consulta o dispositivo duplo para o estado atualizado a cada 500 milissegundos. Escreve na consola apenas quando o estado, na verdade, foi alterada. Para este exemplo, para impedir a consumir mensagens de IoT Hub adicionais na sua subscrição, consulta interrompe quando o dispositivo comunica um Estado de **applyComplete** ou um erro.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Adicione o seguinte método à classe **Programa**:

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Por fim, adicione as seguintes linhas à **Main** método. Esta ação cria um registo gestor para ler o dispositivo duplo com inicia a tarefa consulta um thread de trabalho e, em seguida, aciona a atualização de firmware.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Compilar a solução.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, pode:

* Criar uma aplicação de consola do .NET que responde a um método direto chamado pela nuvem
* Simular uma atualização de firmware acionada por um serviço de back-end através de um método direto
* Utilize as propriedades comunicadas para ativar as consultas de dispositivo duplo, de forma a identificar os dispositivos e quando concluírem uma atualização de firmware pela última vez

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SimulateDeviceFWUpdate**.
   
    ![Nova aplicação de dispositivo Visual c# clássico do Windows][img-createdeviceapp]
    
2. No Explorador de soluções, clique com botão direito do **SimulateDeviceFWUpdate** projeto e, em seguida, clique em **gerir pacotes NuGet**.
3. No **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **microsoft.azure.devices.client**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices.Client** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [dispositivos IoT do Azure SDK] [ lnk-nuget-client-sdk] NuGet pacote e as respetivas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet][img-clientnuget]
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou no **criar uma identidade de dispositivo** secção.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Adicione o seguinte método para comunicar o estado anterior para a nuvem através do dispositivo duplo: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Adicione o seguinte método para simular a transferência da imagem de firmware:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Adicione o seguinte método para simular a aplicar a imagem do firmware para o dispositivo:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Adicione o seguinte método para simular a aguardar para transferir a imagem do firmware. Atualizar o estado para **a aguardar** e desmarque outras propriedades de atualização de firmware no duplo. Estas propriedades estão desmarcadas para remover quaisquer valores existentes de atualizações de firmware anterior. Isto é necessário porque comunicadas propriedades são enviadas como uma operação de PATCH (um delta) e não uma operação PUT (um conjunto completo de propriedades que substitui todos os valores anteriores). Normalmente, os dispositivos são informados sobre uma atualização disponível e um administrador define as causas de política para o dispositivo começar a transferir e aplicar a atualização. Esta função é onde deve ser executada a lógica para ativar essa política. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Adicione o seguinte método para efetuar a transferência. Atualiza o estado **transferir** através do dispositivo duplo, chama o método de transferência simular e comunica um Estado de **downloadComplete** ou **downloadFailed** através de duplo consoante os resultados da operação de transferência. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Adicione o seguinte método para aplicar a imagem. Atualiza o estado **aplicar** através do dispositivo duplo, chamadas de simular aplicar imagem de método e o estado das atualizações para **applyComplete** ou **applyFailed** através do duplo consoante os resultados da operação aplicar. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Adicione o seguinte método para sequenciar a operação de atualização de firmware de espera para transferir a imagem através de aplicar a imagem para o dispositivo:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Adicione o seguinte método para processar o **updateFirmware** direcionar o método da nuvem. Extrai o URL para a atualização de firmware de payload da mensagem e passa-o para o **doUpdate** tarefa, que começa no outro thread threadpool. Em seguida, imediatamente devolve a resposta de método para a nuvem.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Este método aciona a atualização simulada para ser executado como um **tarefas** e responde imediatamente para a chamada de método informar o serviço que a atualização de firmware foi iniciada. Estado da atualização e de conclusão serão enviadas para o serviço através de comunicado propriedades do dispositivo duplo. Iremos responder para a chamada de método quando iniciar a atualização, em vez após a respetiva conclusão, porque:
> * Um processo de atualização real é muito provável que demorar mais do que o tempo limite de chamada de método.
> * Um processo de atualização real é muito provável que requerem um reinício, o qual seria reinicialização a efetuar esta aplicação a **MetodRequest** objeto indisponível. (Atualizar propriedades comunicadas, no entanto, é possível, mesmo após um reinício.) 

14. Por fim, adicione o seguinte código para o **Main** método para abrir a ligação ao seu IoT hub e inicializar o serviço de escuta do método:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Compilar a solução.       

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.


## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.
1. Executar a aplicação de dispositivo .NET **SimulatedDeviceFWUpdate**.  Clique com botão direito do **SimulatedDeviceFWUpdate** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**. Deve começar a escutar para chamadas de método do seu IoT Hub: 

2. Depois do dispositivo está ligado e a aguardar para invocações de método, execute o .NET **TriggerFWUpdate** aplicação ao invocar o método de atualização de firmware na aplicação do dispositivo simulado. Clique com botão direito do **TriggerFWUpdate** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**. Deverá ver atualizar sequência escrita no **SimulatedDeviceFWUpdate** consola e também a sequência comunicadas através as propriedades do dispositivo no comunicado o **TriggerFWUpdate** consola. Tenha em atenção que o processo demora vários segundos a concluir. 
   
    ![Aplicação de serviço e dispositivo executar][img-combinedrun]


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, é utilizado um método direto para acionar uma atualização de firmware remota num dispositivo e utilizado as propriedades que relatados para seguir o progresso da atualização de firmware.

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/