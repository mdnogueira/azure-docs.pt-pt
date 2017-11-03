---
title: "Utilize métodos diretos do IoT Hub do Azure (.NET/.NET) | Microsoft Docs"
description: "Como utilizar métodos diretos do IoT Hub do Azure. Utilizar o dispositivo IoT do Azure SDK para .NET para implementar uma aplicação de dispositivo simulado que inclui um método direto e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnet"></a>Utilize métodos diretos (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Neste tutorial, iremos desenvolver duas aplicações de consola .NET:

* **CallMethodOnDevice**, uma aplicação de back-end que chama um método na aplicação do dispositivo simulado e mostra a resposta.
* **SimulateDeviceMethods**, uma aplicação de consola que simula um dispositivo ao ligar ao seu IoT hub com a identidade de dispositivo que criou anteriormente e responde ao método chamado pela nuvem.

> [!NOTE]
> O artigo [Azure IoT SDKs (SDKs do Azure IoT)][lnk-hub-sdks] disponibiliza informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end.
> 
> 

Para concluir este tutorial, precisa de:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se pretender criar a identidade do dispositivo através de programação em vez disso, leia a secção correspondente do [ligar o seu dispositivo simulado ao seu IoT hub através do .NET] [ lnk-device-identity-csharp] artigo.


## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, crie uma aplicação de consola .NET que responde a um método chamado pela solução de back-end.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SimulateDeviceMethods**.
   
    ![Nova aplicação de dispositivo Visual c# clássico do Windows][img-createdeviceapp]
    
1. No Explorador de soluções, clique com botão direito do **SimulateDeviceMethods** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. No **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **microsoft.azure.devices.client**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices.Client** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [dispositivos IoT do Azure SDK] [ lnk-nuget-client-sdk] NuGet pacote e as respetivas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet][img-clientnuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Adicione o seguinte para implementar o método direto no dispositivo:

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. Por fim, adicione o seguinte código para o **Main** método para abrir a ligação ao seu IoT hub e inicializar o serviço de escuta do método:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. No Visual Studio Explorador de soluções, faça duplo clique sua solução e, em seguida, clique em **definir projetos de arranque...** . Selecione **projeto de arranque único**e, em seguida, selecione o **SimulateDeviceMethods** projeto no menu pendente.        

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como tentativas de ligação), como sugerido no artigo do MSDN [processamento de erros transitórios][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Chamar um método direto num dispositivo
Nesta secção, crie uma aplicação de consola do .NET que chama um método na aplicação do dispositivo simulado e, em seguida, mostra a resposta.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Nomeie o projeto **CallMethodOnDevice**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createserviceapp]
2. No Explorador de soluções, clique com botão direito do **CallMethodOnDevice** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.
   
    ![Janela Gestor de Pacote NuGet][img-servicenuget]

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa**:
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Este método invoca um método direto com o nome `writeLine` no `myDeviceId` dispositivo. Em seguida, escreve a resposta fornecida pelo dispositivo na consola. Tenha em atenção a como é possível especificar um valor de tempo limite para o dispositivo responder.
7. Por fim, adicione as seguintes linhas ao método **Main**:
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. No Visual Studio Explorador de soluções, faça duplo clique sua solução e, em seguida, clique em **definir projetos de arranque...** . Selecione **projeto de arranque único**e, em seguida, selecione o **CallMethodOnDevice** projeto no menu pendente.

## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. Executar a aplicação de dispositivo .NET **SimulateDeviceMethods**. Deve começar a escutar para chamadas de método do seu IoT Hub: 

    ![Aplicação de dispositivo a executar][img-deviceapprun]
1. Agora que o dispositivo está ligado e a aguardar para invocações de método, execute o .NET **CallMethodOnDevice** aplicação ao invocar o método na aplicação do dispositivo simulado. Deverá ver a resposta de dispositivo escrita na consola do.
   
    ![Aplicação de serviço, executar][img-serviceapprun]
1. O dispositivo, em seguida, reage para o método por esta mensagem de impressão:
   
    ![Método direto invocado no dispositivo][img-directmethodinvoked]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Utilizou esta identidade de dispositivo para ativar a aplicação de dispositivo simulado para reagir a métodos invocados pela nuvem. Também criou uma aplicação que invoca métodos no dispositivo e mostra a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao IoT Hub]
* [Agenda de tarefas em vários dispositivos][lnk-devguide-jobs]

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[Introdução ao IoT Hub]: iot-hub-node-node-getstarted.md
