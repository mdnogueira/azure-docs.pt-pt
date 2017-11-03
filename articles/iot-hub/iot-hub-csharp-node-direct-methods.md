---
title: "Utilize métodos diretos do IoT Hub do Azure (nó/.NET) | Microsoft Docs"
description: "Como utilizar métodos diretos do IoT Hub do Azure. Utilizar o dispositivo IoT do Azure SDK para Node.js para implementar uma aplicação de dispositivo simulado que inclui um método direto e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnode"></a>Utilize métodos diretos (.NET/nó)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Neste tutorial, iremos para desenvolver um .NET e uma aplicação de consola do Node.js:

* **CallMethodOnDevice.sln**, uma aplicação de back-end de .NET, o que chama um método na aplicação do dispositivo simulado e mostra a resposta.
* **SimulatedDevice.js**, uma aplicação de Node.js que simula um dispositivo ao ligar ao seu IoT hub com a identidade de dispositivo que criou anteriormente e responde para o método chamado pela nuvem.

> [!NOTE]
> O artigo [Azure IoT SDKs (SDKs do Azure IoT)][lnk-hub-sdks] disponibiliza informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end.
> 
> 

Para concluir este tutorial, precisa de:

* Visual Studio 2015 ou Visual Studio 2017.
* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, crie uma aplicação de consola do Node.js que responde a um método chamado pela solução de back-end.

1. Crie uma nova pasta vazia designada **simulateddevice**. Na pasta **simulateddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **simulateddevice** pasta, execute o seguinte comando para instalar o **azure-iot-device** e **azure-iot-dispositivo-mqtt** pacotes:
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um ficheiro no **simulateddevice** pasta e dê-lhe nome **SimulatedDevice.js**.
4. Adicione as seguintes instruções `require` no início do ficheiro **SimulatedDevice.js**:
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Adicionar um **connectionString** variável e utilize-o para criar um **DeviceClient** instância. Substitua **{cadeia de ligação do dispositivo}** com a ligação do dispositivo de cadeia que gerou no *criar uma identidade de dispositivo* secção:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Adicione a seguinte função para implementar o método direto no dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra a ligação ao seu IoT hub e inicializar o serviço de escuta do método:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Guarde e feche o ficheiro **SimulatedDevice.js**.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como tentativas de ligação), como sugerido no artigo do MSDN [processamento de erros transitórios][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Chamar um método direto num dispositivo
Nesta secção, crie uma aplicação de consola do .NET que chama um método na aplicação do dispositivo simulado e, em seguida, mostra a resposta.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Certifique-se de ter a versão 4.5.1 ou superior do .NET Framework. Nomeie o projeto **CallMethodOnDevice**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][10]
2. No Explorador de soluções, clique com botão direito do **CallMethodOnDevice** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
3. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.
   
    ![Janela Gestor de Pacote NuGet][11]

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

## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. No Visual Studio Explorador de soluções, faça duplo clique sua solução e, em seguida, clique em **definir projetos de arranque...** . Selecione **projeto de arranque único**e, em seguida, selecione o **CallMethodOnDevice** projeto no menu pendente.

2. Numa linha de comandos do **simulateddevice** pasta, execute o seguinte comando para iniciar a deteção para chamadas de método do seu IoT Hub:
   
    ```
    node SimulatedDevice.js
    ```
   Aguarde que o dispositivo simulado abrir:![][7]
3. Agora que o dispositivo está ligado e a aguardar para invocações de método, execute o .NET **CallMethodOnDevice** aplicação ao invocar o método na aplicação do dispositivo simulado. Deverá ver a resposta de dispositivo escrita na consola do.
   
    ![][8]
4. O dispositivo, em seguida, reage para o método por esta mensagem de impressão:
   
    ![][9]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Utilizou esta identidade de dispositivo para ativar a aplicação de dispositivo simulado para reagir a métodos invocados pela nuvem. Também criou uma aplicação que invoca métodos no dispositivo e mostra a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao IoT Hub]
* [Agenda de tarefas em vários dispositivos][lnk-devguide-jobs]

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao IoT Hub]: iot-hub-node-node-getstarted.md
