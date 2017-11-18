---
title: "Mensagens da nuvem para dispositivo IoT hub do Azure (nó) | Microsoft Docs"
description: "Como enviar mensagens da nuvem para o dispositivo a um dispositivo de um hub IoT do Azure com os SDKs IoT do Azure para Node.js. Modificar uma aplicação de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar uma aplicação de back-end para enviar as mensagens da nuvem para o dispositivo."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 80f65e8e7fe562030c1e39787b910e2564969882
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Enviar mensagens da nuvem para o dispositivo com o IoT Hub (nó)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução
IoT Hub do Azure é um serviço completamente gerido que o ajuda a ativar fiáveis e seguras comunicações bidirecionais entre milhões de dispositivos e uma solução de back-end. O [introdução ao IoT Hub] tutorial mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Este tutorial baseia-se [introdução ao IoT Hub]. Mostra-lhe como para:

* Da sua solução de back-end, envie mensagens de nuvem para o dispositivo para um único dispositivo através do IoT Hub.
* Receba mensagens da nuvem para o dispositivo num dispositivo.
* Da sua solução de back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo a partir do IoT Hub.

Pode encontrar mais informações sobre mensagens da nuvem para o dispositivo no [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].

No final deste tutorial, executa duas aplicações de consola do Node.js:

* **SimulatedDevice**, uma versão modificada da aplicação criada na [introdução ao IoT Hub], que liga ao seu IoT hub e recebe mensagens da nuvem para o dispositivo.
* **SendCloudToDeviceMessage**, que envia uma mensagem da nuvem para o dispositivo para a aplicação de dispositivo simulado através do IoT Hub e, em seguida, receber a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para várias plataformas de dispositivos e idiomas (incluindo C, Java e Javascript) através de SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como ligar o seu dispositivo para código neste tutorial e, geralmente, IoT Hub do Azure, consulte o [Centro de programadores do IoT do Azure].
> 
> 

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens na aplicação do dispositivo simulado
Nesta secção, modificar a aplicação de dispositivo simulado que criou no [introdução ao IoT Hub] para receber mensagens da nuvem para dispositivos do IoT hub.

1. Com um editor de texto, abra o ficheiro de SimulatedDevice.js.
2. Modificar o **connectCallback** função para processar as mensagens enviadas a partir do IoT Hub. Neste exemplo, o dispositivo sempre invoca o **concluída** função para notificar o IoT Hub que processou a mensagem. A versão nova do **connectCallback** função aparente ser o seguinte fragmento:
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
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
   
   > [!NOTE]
   > Se utilizar o HTTPS em vez de MQTT ou AMQP como transporte, o **DeviceClient** instância verifica a existência de mensagens a partir do IoT Hub com pouca frequência (inferior a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte MQTT, AMQP e HTTPS e a limitação do IoT Hub, consulte o [guia para programadores do IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo
Nesta secção, crie uma aplicação de consola do Node.js que envia mensagens da nuvem para o dispositivo para a aplicação de dispositivo simulado. É necessário o ID de dispositivo do dispositivo adicionado no [introdução ao IoT Hub] tutorial. Também precisa da cadeia de ligação do IoT Hub para o seu hub que pode encontrar o [portal do Azure].

1. Crie uma pasta vazia designada **sendcloudtodevicemessage**. No **sendcloudtodevicemessage** pasta, crie um ficheiro de Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```shell
    npm init
    ```
2. Na sua linha de comandos a **sendcloudtodevicemessage** pasta, execute o seguinte comando para instalar o **azure iothub** pacote:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie um **SendCloudToDeviceMessage.js** ficheiros o **sendcloudtodevicemessage** pasta.
4. Adicione o seguinte `require` as instruções no início do **SendCloudToDeviceMessage.js** ficheiro:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Adicione o seguinte código para **SendCloudToDeviceMessage.js** ficheiro. Substitua o valor do marcador de posição de "{iot hub cadeia de ligação}" com a cadeia de ligação do IoT Hub hub que criou no [introdução ao IoT Hub] tutorial. Substitua o marcador de posição "{id de dispositivo}" com o ID de dispositivo do dispositivo adicionado no [introdução ao IoT Hub] tutorial:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Adicione a seguinte função para imprimir os resultados da operação para a consola:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Adicione a seguinte função para imprimir mensagens de comentários de entrega para a consola:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Adicione o seguinte código para enviar uma mensagem para o seu dispositivo e processar a mensagem de comentários quando o dispositivo reconhece a mensagem da nuvem para o dispositivo:
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Guarde e feche **SendCloudToDeviceMessage.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. Na linha de comandos no **simulateddevice** pasta, execute o seguinte comando para enviar telemetria ao IoT Hub e escutam as mensagens da nuvem para o dispositivo:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Executar a aplicação de dispositivo simulada][img-simulated-device]
2. Numa linha de comandos do **sendcloudtodevicemessage** pasta, execute o seguinte comando para enviar uma mensagem da nuvem para o dispositivo e a aguardar o comentários de confirmação:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Executar a aplicação para enviar o comando de nuvem para o dispositivo][img-send-command]
   
   > [!NOTE]
   > Para sake do simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como término exponencial), como sugerido no artigo do MSDN [processamento de erros transitórios].
   > 
   > 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a enviar e receber mensagens da nuvem para o dispositivo. 

Para ver os exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, consulte [Azure IoT Suite].

Para obter mais informações sobre como desenvolver soluções de IoT hub, consulte o [guia para programadores do IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[introdução ao IoT Hub]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[guia para programadores do IoT Hub]: iot-hub-devguide.md
[Centro de programadores do IoT do Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[processamento de erros transitórios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portal do Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
