<properties
    pageTitle="IoT Hub do Azure para uma introdução ao Node,js | Microsoft Azure"
    description="IoT Hub do Azure com tutorial de introdução ao Node.js. Utilize o Hub IoT do Azure e o Node.js com os SDKs IoT do Microsoft Azure para implementar uma solução da Internet das Coisas."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# Introdução ao IoT Hub do Azure para Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, terá três aplicações de consola do Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar o seu dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que apresenta a telemetria enviada pelo seu dispositivo simulado.
* **SimulatedDevice.js**, que liga ao seu IoT Hub com a identidade de dispositivo que criou anteriormente e envia uma mensagem de telemetria a cada segundo através do protocolo AMQPS.

> [AZURE.NOTE] O artigo [SDKs do IoT Hub][lnk-hub-sdks] fornece informações sobre os vários SDKs que pode utilizar para criar ambas as aplicações a executar em dispositivos e a sua solução de back-end.

Para concluir este tutorial, irá precisar de:

+ Versão 0.12.x ou posterior do Node.js. <br/> [Preparar o seu ambiente de desenvolvimento][Ink-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou Linux.

+ Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu hub IoT. Tem o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir o resto deste tutorial.

## Criar uma identidade de dispositivo

Nesta secção, irá criar uma aplicação de consola do Node.js que cria uma nova identidade de dispositivo no registo de identidade do seu IoT Hub. Não é possível ligar um dispositivo ao IoT Hub exceto se tiver uma entrada no registo de identidade de dispositivo. Para mais informações, consulte a secção **Registo de Identidade de Dispositivo** do [Guia para Programadores do IoT Hub][lnk-devguide-identity]. Ao executar esta aplicação de consola, é gerado um ID de dispositivo exclusivo e uma chave que o seu dispositivo pode utilizar para se identificar quando enviar mensagens do dispositivo para a nuvem ao Hub IoT.

1. Criar uma nova pasta designada **createdeviceidentity**. Na pasta **createdeviceidentity**, crie um novo ficheiro package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua linha de comandos da pasta **createdeviceidentity**, execute o seguinte comando para instalar o pacote **azure iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Com um editor de texto, crie um novo ficheiro **CreateDeviceIdentity.js** na pasta **createdeviceidentity**.

4. Adicione a seguinte instrução `require` no início do ficheiro **CreateDeviceIdentity.js**:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Adicione o seguinte código ao ficheiro **CreateDeviceIdentity.js** e substitua o valor do marcador de posição pela cadeia de ligação para o Hub IoT que criou na secção anterior: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o seguinte código para criar uma nova definição de dispositivo no registo de identidade do dispositivo do seu IoT Hub. Este código cria um novo dispositivo se o id de dispositivo não existir no registo, caso contrário, irá devolver a chave do dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
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
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Guarde e feche o ficheiro **CreateDeviceIdentity.js**.

8. Para executar a aplicação **createdeviceidentity** execute o seguinte comando na linha de comandos na pasta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Tome note do **Id do Dispositivo** e da **Chave do Dispositivo**. Irá precisar destas informações posteriormente quando criar uma aplicação que liga ao IoT Hub como um dispositivo.

> [AZURE.NOTE] O registo de identidade do IoT Hub apenas armazena identidades de dispositivo para permitir um acesso seguro ao Hub. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, consulte o artigo [Guia para Programadores do IoT Hub][lnk-devguide-identity].

## Receber mensagens dispositivo-nuvem

Nesta secção, irá criar uma aplicação de consola do Node.js que lê mensagens do dispositivo para a nuvem a partir do IoT Hub. Um IoT Hub expõe um ponto final compatível com [Event Hubs][lnk-event-hubs-overview], o que lhe permite ler mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implementação com débito elevado. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra-lhe como processar mensagens do dispositivo para a nuvem à escala. O tutorial [Introdução aos Hubs de Eventos][Ink-eventhubs-tutorial] fornece mais informações sobre como processar mensagens a partir dos Hubs de Eventos e é aplicável aos pontos finais do Hub IoT compatíveis com os Hubs de Eventos.

> [AZURE.NOTE] O ponto final compatível com o Event Hubs para a leitura de mensagens do dispositivo para a nuvem utiliza sempre o protocolo AMQPS.

1. Crie uma nova pasta vazia designada **readdevicetocloudmessages**. Na pasta **readdevicetocloudmessages**, crie um novo ficheiro package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua linha de comandos da pasta **readdevicetocloudmessages**, execute o seguinte comando para instalar o pacote **azure-event-hubs**:

    ```
    npm install azure-event-hubs --save
    ```

3. Com um editor de texto, crie um novo ficheiro **ReadDeviceToCloudMessages.js** na pasta **readdevicetocloudmessages**.

4. Adicione as seguintes instruções `require` no início do **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Adicione a seguinte declaração de variável e substitua o valor do marcador pela cadeia de ligação para o seu hub IoT:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Adicione as seguintes duas funções que imprimem o resultado para a consola:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Adicione o seguinte código para criar o **EventHubClient**, abra a ligação ao seu Hub IoT e crie um recetor para cada partição. Esta aplicação utiliza um filtro quando cria o recetor . Deste modo, o recetor apenas poderá ler as mensagens enviadas ao IoT Hub depois de o recetor entrar em execução. Esta ação torna-se útil num ambiente de teste, de modo a que veja apenas o conjunto atual de mensagens. No entanto, num ambiente de produção, deverá certificar-se de que o seu código processa todas as mensagens. Para mais informações, consulte o tutorial [Como processar mensagens do dispositivo para a nuvem do Hub IoT][Ink-process-d2c-tutorial]:

    ```
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

## Criar uma aplicação de dispositivo simulada

Nesta secção, irá criar uma aplicação de consola do Node.js que simula um dispositivo que envia mensagens do dispositivo para a nuvem a um IoT Hub.

1. Crie uma nova pasta vazia designada **simulateddevice**. Na pasta **simulateddevice**, crie um novo ficheiro package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua linha de comandos da pasta **simulateddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Com um editor de texto, crie um novo ficheiro **SimulatedDevice.js** na pasta **simulateddevice**.

4. Adicione as seguintes instruções `require` no início do ficheiro **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicione uma variável **connectionString** e utilize-a para criar um cliente do dispositivo. Substitua **{youriothostname}** pelo nome do hub IoT que criou na secção *Criar um Hub IoT* e **{yourdevicekey}** pelo valor-chave do dispositivo que gerou na secção *Criar uma identidade de dispositivo*:

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para apresentar os resultados da aplicação:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Crie uma chamada de retorno e utilize a função **setInterval** para enviar uma nova mensagem ao seu IoT Hub a cada segundo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Abra a ligação ao seu IoT Hub e comece a enviar mensagens:

    ```
    client.open(connectCallback);
    ```

9. Guarde e feche o ficheiro **SimulatedDevice.js**.

> [AZURE.NOTE] Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Processamento de Erros Transitórios][Ink-transitório-lento] da MSDN.


## Executar as aplicações

Pode agora executar as aplicações.

1. Numa linha de comandos da pasta **readdevicetocloudmessages**, execute o seguinte comando para começar a monitorizar o seu IoT Hub:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Numa linha de comandos da pasta **simulateddevice**, execute o seguinte comando para começar a enviar dados de telemetria ao seu IoT Hub:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. O mosaico **Utilização** no [Portal do Azure][Ink-portal] mostra o número de mensagens enviadas ao Hub:

    ![][43]

## Passos seguintes

Neste tutorial, configurou um novo IoT Hub no Portal e, de seguida, criou uma identidade de dispositivo no registo de identidade do Hub. Utilizou esta identidade de dispositivo para que a aplicação do dispositivo simulado pudesse enviar mensagens do dispositivo para a nuvem ao Hub. Também criou uma aplicação que apresenta as mensagens recebidas através do Hub. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

- [Ligar o seu dispositivo][lnk-connect-device]
- [Introdução à gestão de dispositivos][lnk-device-management]
- [Introdução ao SDK do Gateway][lnk-gateway-SDK]

Para saber como expandir a sua solução de IoT e processar mensagens do dispositivo para a nuvem à escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a nuvem)][Ink-process-d2c-tutorial].

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[Ink-transitório-lento]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Aug16_HO1-->


