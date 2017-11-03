---
title: "Introdução à gestão de dispositivos do IoT Hub do Azure (nó/.NET) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos do IoT Hub do Azure para iniciar um reinício do dispositivo remoto. Utilizar o dispositivo IoT do Azure SDK para Node.js para implementar uma aplicação de dispositivo simulado que inclui um método direto e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que invoca o método direto."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: juanpere
ms.openlocfilehash: 5d0b7b1ab5893e55a6e2aa16451b6a9fc1481966
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="get-started-with-device-management-netnode"></a>Introdução à gestão de dispositivos (.NET/nó)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um IoT Hub e criar uma identidade de dispositivo no seu IoT hub.
* Crie uma aplicação de dispositivo simulado que contém um método direto que reinicia nesse dispositivo. Métodos diretos são invocados a partir da nuvem.
* Crie uma aplicação de consola .NET que chama o método direto reinício na aplicação do dispositivo simulado através do seu IoT hub.

No final deste tutorial, terá de uma aplicação de dispositivo de consola do Node.js e uma aplicação de back-end do .NET (c#) consola:

**dmpatterns_getstarted_device.js**, que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente, recebe um método direta de reinício, simula reiniciar o computador físico, relatórios e a hora para o último reinício.

**TriggerReboot**, que chama um método direto na aplicação do dispositivo simulado, mostra a resposta e apresenta a atualização comunicado propriedades.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Versão do node.js 4.0.x ou posterior <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar um reinício remoto no dispositivo com um método direto
Nesta secção, criar uma aplicação de consola do .NET (utilizando c#) inicia um reinício remoto num dispositivo utilizando um método direto. A aplicação utiliza consultas de duplo de dispositivo para detetar a última vez de reiniciar o computador para que o dispositivo.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows a uma solução nova, utilizando o modelo de projeto **Aplicação de Consola (.NET Framework)**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Nomeie o projeto **TriggerReboot**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createapp]

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
        static JobClient jobClient;
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

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, irá

* Criar uma aplicação de consola Node.js que responde a um método direto chamado pela cloud
* Acionar um reinício do dispositivo simulado
* Utilize as propriedades que relatados para ativar as consultas de duplo de dispositivo identificar dispositivos e quando duram reiniciado

1. Criar uma nova pasta vazia designada **manageddevice**.  Na pasta **manageddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **manageddevice** pasta, execute o seguinte comando para instalar o **azure-iot-device** pacote do SDK do dispositivo e **azure-iot-dispositivo-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo **dmpatterns_getstarted_device.js** ficheiros o **manageddevice** pasta.
4. Adicione o seguinte 'exigir' instruções no início do **dmpatterns_getstarted_device.js** ficheiro:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**.  Substitua a cadeia de ligação pela cadeia de ligação do dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função para implementar o método direto no dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Adicione o seguinte código para abrir a ligação ao seu IoT hub e iniciar o serviço de escuta do método direto:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Guarde e feche o **dmpatterns_getstarted_device.js** ficheiro.
   
> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.


## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. Na linha de comandos no **manageddevice** pasta, execute o seguinte comando para começar a escutar o método direta de reinício.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Executar a aplicação de consola c# **TriggerReboot**. Clique com botão direito do **TriggerReboot** projeto, selecione **depurar**e, em seguida, selecione **iniciar nova instância**.

3. Pode ver a resposta de dispositivo para o método direto na consola do.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
