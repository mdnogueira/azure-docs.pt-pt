---
title: "Atualização de firmware do dispositivo IoT hub do Azure (nó/.NET) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos no IoT Hub do Azure para iniciar uma atualização de firmware do dispositivo. Utilizar o dispositivo IoT do Azure SDK para Node.js para implementar uma aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que aciona a atualização de firmware."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: 157f112869f0042e330e6b281367632ca015e890
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Utilize a gestão de dispositivos para iniciar uma atualização de firmware do dispositivo (.NET/nó)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

No [introdução à gestão de dispositivos] [ lnk-dm-getstarted] tutorial, vimos como utilizar o [dispositivo duplo] [ lnk-devtwin] e [direcionar métodos] [ lnk-c2dmethod] primitivos reiniciar remotamente um dispositivo. Este tutorial utiliza os mesmos primitivos do IoT Hub e mostra como efetuar uma atualização de firmware simulada ponto-a-ponto.  Este padrão é utilizado na implementação de atualização de firmware para o [exemplo de implementação do dispositivo Raspberry Pi][lnk-rpi-implementation].

Este tutorial mostrar-lhe como:

* Crie uma aplicação de consola .NET que chama o método direto firmwareUpdate na aplicação do dispositivo simulado através do seu IoT hub.
* Criar uma aplicação de dispositivo simulado que implementa um **firmwareUpdate** método direto. Este método inicia um processo de fase multi que aguarda para transferir a imagem de firmware, transfere a imagem de firmware e, finalmente, aplica-se a imagem do firmware. Durante cada fase da atualização, o dispositivo utiliza as propriedades que relatados para elaborar relatórios sobre o progresso.

No final deste tutorial, terá de uma aplicação de dispositivo de consola do Node.js e uma aplicação de back-end do .NET (c#) consola:

**dmpatterns_fwupdate_service.js**, que chama um método direto na aplicação do dispositivo simulado, mostra a resposta e periodicamente (cada 500ms) apresenta a atualização comunicado propriedades.

**TriggerFWUpdate**, que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente, recebe um método direto firmwareUpdate, é executada através de um processo com múltiplos estado para simular a atualização de firmware, incluindo: a aguardar que a transferência da imagem, transferir a nova imagem e, finalmente, aplicar a imagem.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Versão do node.js 4.0.x ou posterior <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Siga o [introdução à gestão de dispositivos](iot-hub-csharp-node-device-management-get-started.md) artigo para criar o hub IoT e obter a cadeia de ligação do IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Acionar uma atualização de firmware remota no dispositivo com um método direto
Nesta secção, criar uma aplicação de consola do .NET (utilizando c#) inicia uma atualização de firmware remota num dispositivo. A aplicação utiliza um método direto para iniciar a atualização e utiliza consultas de duplo de dispositivo para obter periodicamente o estado da atualização de firmware do Active Directory.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **TriggerFWUpdate**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createapp]

1. No Explorador de soluções, clique com botão direito do **TriggerFWUpdate** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.

    ![Janela Gestor de Pacote NuGet][img-servicenuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Adicione os seguintes campos à classe **Programa**. Substitua os vários valores de marcador de posição pela cadeia de ligação do IoT Hub para o hub que criou na secção anterior e o Id do seu dispositivo.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Adicione o seguinte método à classe **Programa**:
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Adicione o seguinte método à classe **Programa**:

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

1. Por fim, adicione as seguintes linhas ao método **Main**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. No Explorador de soluções, abra o **definir projetos de arranque...**  e certifique-se a **ação** para **TriggerFWUpdate** projeto é **iniciar**.

1. Compilar a solução.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. Na linha de comandos no **manageddevice** pasta, execute o seguinte comando para começar a escutar o método direta de reinício.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. No Visual Studio, clique com botão direito no **TriggerFWUpdate** projeto, selecione **depurar** e **iniciar nova instância**.

3. Pode ver a resposta de dispositivo para o método direto na consola do.

    ![Firmware atualizada com êxito][img-fwupdate]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, é utilizado um método direto para acionar uma atualização de firmware remota num dispositivo e utilizado as propriedades que relatados para seguir o progresso da atualização de firmware.

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/