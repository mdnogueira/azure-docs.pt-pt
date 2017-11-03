---
title: "IoT Hub do Azure direcionar métodos (nó) | Microsoft Docs"
description: "Como utilizar métodos diretos do IoT Hub do Azure. Utilize os SDKs IoT do Azure para Node.js para implementar uma aplicação de dispositivo simulado que inclui um método direto e uma aplicação de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a73c25724a239e56c3ea62a8452bb7c3a2b51be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>Utilize métodos diretos no seu dispositivo IoT com o Node.js
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

No final deste tutorial, tem duas aplicações de consola do Node.js:

* **CallMethodOnDevice.js**, que chama um método na aplicação do dispositivo simulado e mostra a resposta.
* **SimulatedDevice.js**, que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente e responde para o método chamado pela nuvem.

> [!NOTE]
> O artigo [Azure IoT SDKs (SDKs do Azure IoT)][lnk-hub-sdks] disponibiliza informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end.
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, crie uma aplicação de consola do Node.js que responde a um método chamado pela nuvem.

1. Crie uma nova pasta vazia designada **simulateddevice**. Na pasta **simulateddevice**, crie um ficheiro package.json com o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote SDK do Serviço **azure-iot-device** e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo ficheiro **SimulatedDevice.js** na pasta **simulateddevice**.
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
6. Adicione a seguinte função para implementar o método no dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra a ligação ao seu IoT hub e iniciar inicializar a escuta de método:
   
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

## <a name="call-a-method-on-a-device"></a>Chamar um método num dispositivo
Nesta secção, crie uma aplicação de consola do Node.js que chama um método na aplicação do dispositivo simulado e, em seguida, mostra a resposta.

1. Criar uma nova pasta vazia designada **callmethodondevice**. No **callmethodondevice** pasta, crie um ficheiro de Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **callmethodondevice** pasta, execute o seguinte comando para instalar o **azure iothub** pacote:
   
    ```
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie um **CallMethodOnDevice.js** ficheiros o **callmethodondevice** pasta.
4. Adicione o seguinte `require` as instruções no início do **CallMethodOnDevice.js** ficheiro:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Adicione a seguinte declaração de variável e substitua o valor do marcador pela cadeia de ligação do Hub IoT para o seu hub:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Crie o cliente para abrir a ligação ao seu IoT hub.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Adicione a seguinte função para invocar o método de dispositivo e imprimir a resposta de dispositivo para a consola:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Guarde e feche o **CallMethodOnDevice.js** ficheiro.

## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. Numa linha de comandos do **simulateddevice** pasta, execute o seguinte comando para iniciar a deteção para chamadas de método do seu IoT Hub:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Numa linha de comandos do **callmethodondevice** pasta, execute o seguinte comando para começar a monitorizar o seu IoT hub:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Verá o dispositivo reagir para o método pelo imprimindo a mensagem e a aplicação que chamado a apresentação do método a resposta do dispositivo:
   
    ![][9]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Utilizou esta identidade de dispositivo para ativar a aplicação de dispositivo simulado para reagir a métodos invocados pela nuvem. Também criou uma aplicação que invoca métodos no dispositivo e mostra a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Introdução ao IoT Hub]
* [Agenda de tarefas em vários dispositivos][lnk-devguide-jobs]

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao IoT Hub]: iot-hub-node-node-getstarted.md
