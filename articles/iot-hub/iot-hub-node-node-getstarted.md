---
title: "Introdução ao Hub IoT do Azure (Node) | Microsoft Docs"
description: "Aprenda a enviar mensagens do dispositivo para a cloud para um hub IoT do Azure com os SDKs para Node.js. Crie um dispositivo simulado e aplicações de serviço para registar o seu dispositivo, enviar mensagens e ler as mensagens do hub IoT."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b360d5a08abed7d65d8c39f7a957412656f7825
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Ligar o dispositivo simulado ao seu hub IoT com o Node

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, tem três aplicações de consola do Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar a sua aplicação de dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que apresenta a telemetria enviada pela sua aplicação de dispositivo simulado.
* **SimulatedDevice.js**, que liga ao seu Hub IoT com a identidade de dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo através do protocolo MQTT.

> [!NOTE]
> O artigo [Azure IoT SDKs (SDKs do Azure IoT)][lnk-hub-sdks] disponibiliza informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end.

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu hub IoT. Tem o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir o resto deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta secção, vai criar uma aplicação de consola do Node.js que cria uma identidade de dispositivo no registo de identidade do seu hub IoT. Só é possível ligar um dispositivo ao hub IoT, se tiver uma entrada no registo de identidade. Para obter mais informações, veja a secção **Identity Registry (Registo de Identidades)** do [Hub IoT developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity]. Execute esta aplicação para gerar o ID e a chave exclusivos que o seu dispositivo utiliza para se identificar a ele próprio quando envia mensagens do dispositivo para a cloud.

1. Crie uma nova pasta vazia com o nome `createdeviceidentity`. Na pasta `createdeviceidentity`, crie um ficheiro package.json com o comando seguinte na sua linha de comandos. Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Na linha de comandos da pasta `createdeviceidentity`, execute o comando abaixo para instalar o pacote SDK do Serviço `azure-iothub`:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Com um editor de texto, crie um ficheiro **CreateDeviceIdentity.js** na pasta `createdeviceidentity`.

4. Adicione a seguinte instrução `require` no início do ficheiro **CreateDeviceIdentity.js**:

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. Adicione o código seguinte ao ficheiro **CreateDeviceIdentity.js**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior:

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o seguinte código para criar uma definição de dispositivo no registo de identidade do dispositivo do seu hub IoT. Este código cria um dispositivo se o ID de dispositivo não existir no registo de identidade. Caso contrário, irá devolver a chave do dispositivo existente:

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. Guarde e feche o ficheiro **CreateDeviceIdentity.js**.

8. Para executar a aplicação `createdeviceidentity`, execute o comando seguinte na linha de comandos na pasta `createdeviceidentity`:

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```

9. Tome note do **ID do Dispositivo** e da **Chave do dispositivo**. Vai precisar destes valores mais tarde quando criar uma aplicação que liga ao Hub IoT como um dispositivo.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity].

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>Receber mensagens dispositivo-nuvem

Nesta secção, vai criar uma aplicação de consola do Node.js que lê mensagens do dispositivo para a nuvem a partir do Hub IoT. Um hub IoT expõe um ponto final compatível com os [Hubs de Eventos][lnk-event-hubs-overview], o que lhe permite ler mensagens do dispositivo para a cloud. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implementação com débito elevado. O tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial] mostra-lhe como processar mensagens do dispositivo para a cloud em escala. O tutorial [Get Started with Event Hubs][lnk-eventhubs-tutorial] (Introdução aos Hubs de Eventos) disponibiliza mais informações que se aplicam aos pontos finais compatíveis com o Hub de Eventos do Hub IoT.

> [!NOTE]
> O ponto final compatível com o Hub de Eventos para a leitura de mensagens do dispositivo para a nuvem utiliza sempre o protocolo AMQP.

1. Crie uma pasta vazia com o nome `readdevicetocloudmessages`. Na pasta `readdevicetocloudmessages`, crie um ficheiro package.json com o comando seguinte na sua linha de comandos. Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Na linha de comandos da pasta `readdevicetocloudmessages`, execute o comando abaixo para instalar o pacote **azure-event-hubs**:

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. Com um editor de texto, crie um ficheiro **ReadDeviceToCloudMessages.js** na pasta `readdevicetocloudmessages`.

4. Adicione as seguintes instruções `require` no início do **ReadDeviceToCloudMessages.js**:

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Adicione a seguinte declaração de variável e substitua o valor do marcador pela cadeia de ligação do Hub IoT para o seu hub:

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```

6. Adicione as seguintes duas funções que imprimem o resultado para a consola:

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Adicione o seguinte código para criar o **EventHubClient**, abra a ligação ao seu Hub IoT e crie um recetor para cada partição. Esta aplicação utiliza um filtro quando cria o recetor . Deste modo, o recetor apenas poderá ler as mensagens enviadas ao IoT Hub depois de o recetor entrar em execução. Este filtro é útil num ambiente de teste para que veja apenas o conjunto atual de mensagens. Num ambiente de produção, deve certificar-se de que o seu código processa todas as mensagens. Para obter mais informações, veja o tutorial [Como processar mensagens do dispositivo para a cloud do Hub IoT][lnk-process-d2c-tutorial]:

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Guarde e feche o ficheiro **ReadDeviceToCloudMessages.js**.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, vai criar uma aplicação de consola do Node.js que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um Hub IoT.

1. Crie uma pasta vazia com o nome `simulateddevice`. Na pasta `simulateddevice`, crie um ficheiro package.json com o comando seguinte na sua linha de comandos. Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Na linha de comandos da pasta `simulateddevice`, execute o comando seguinte para instalar o pacote SDK do Serviço **azure-iot-device** e o pacote **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Com um editor de texto, crie um ficheiro **SimulatedDevice.js** na pasta `simulateddevice`.

4. Adicione as seguintes instruções `require` no início do ficheiro **SimulatedDevice.js**:

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicione uma variável `connectionString` e utilize-a para criar uma instância **Cliente**. Substitua `{youriothostname}` pelo nome do hub IoT que criou na secção *Criar um Hub IoT*. Substitua `{yourdevicekey}` pelo valor da chave do dispositivo que gerou na secção *Criar uma identidade de dispositivo*:

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para apresentar os resultados da aplicação:

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Crie uma chamada de retorno e utilize a função **setInterval** para enviar uma mensagem ao seu IoT Hub a cada segundo:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Abra a ligação ao seu IoT Hub e comece a enviar mensagens:

    ```nodejs
    client.open(connectCallback);
    ```

9. Guarde e feche o ficheiro **SimulatedDevice.js**.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. Numa linha de comandos da pasta `readdevicetocloudmessages`, execute o comando seguinte para começar a monitorizar o seu Hub IoT:

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![Aplicação do serviço Hub IoT Node.js para monitorizar mensagens do dispositivo para a cloud][7]

2. Numa linha de comandos da pasta `simulateddevice`, execute o comando seguinte para começar a enviar dados de telemetria para o seu Hub IoT:

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![Aplicação do dispositivo Hub IoT Node.js para enviar mensagens do dispositivo para a cloud][8]

3. O mosaico **Utilização** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas ao Hub IoT:

    ![Mosaico “Utilização do portal do Azure”, que mostra o número de mensagens enviadas para o Hub IoT][43]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Utilizou esta identidade de dispositivo para que a aplicação do dispositivo simulado pudesse enviar mensagens do dispositivo para a cloud ao Hub IoT. Também criou uma aplicação que apresenta as mensagens recebidas pelo Hub IoT.

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Ligar o seu dispositivo][lnk-connect-device]
* [Getting started with device management (Introdução à gestão de dispositivos)][lnk-device-management]
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iot-edge]

Para saber como expandir a sua solução de IoT e processar mensagens do dispositivo para a cloud em escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
