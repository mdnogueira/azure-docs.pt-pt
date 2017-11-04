---
title: "Introdução ao duplos de dispositivos do IoT Hub do Azure (nó) | Microsoft Docs"
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilize os SDKs IoT do Azure para Node.js para implementar a aplicação de dispositivo simulada e uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: df49f054b5eb26c3d68f088bc05f5209cf2ebccf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-node"></a>Começar a utilizar dispositivos duplos (nó)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá de duas aplicações de consola do Node.js:

* **AddTagsAndQuery.js**, uma aplicação de back-end Node.js, que adiciona as etiquetas e a consulta dispositivos duplos.
* **TwinSimulatedDevice.js**, uma aplicação de Node.js que simula um dispositivo que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente e reporta a condição de conectividade.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, criar uma aplicação de consola do Node.js que adiciona os metadados de localização para o dispositivo duplo associado **myDeviceId**. Esta consulta, em seguida, os dispositivos duplos armazenados no hub IoT selecionar os dispositivos localizados nos EUA e, em seguida, aqueles que estão a denunciar uma ligação de rede móvel.

1. Criar uma nova pasta vazia designada **addtagsandqueryapp**. No **addtagsandqueryapp** pasta, crie um novo ficheiro Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **addtagsandqueryapp** pasta, execute o seguinte comando para instalar o **azure iothub** pacote:
   
    ```
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie um novo **AddTagsAndQuery.js** ficheiros o **addtagsandqueryapp** pasta.
4. Adicione o seguinte código para o **AddTagsAndQuery.js** de ficheiros e substitua o **{cadeia de ligação do hub iot}** marcador de posição pela cadeia de ligação do IoT Hub que copiou quando criou o seu hub:
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    O **registo** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. O código anterior primeiro inicializa a **registo** objeto, em seguida, obtém o dispositivo duplo para **myDeviceId**e, finalmente, atualiza as etiquetas com as informações de localização pretendida.
   
    Depois de atualizar os sinalizadores chama o **queryTwins** função.
5. Adicione o seguinte código no final do **AddTagsAndQuery.js** para implementar o **queryTwins** função:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    O código anterior executa duas consultas: o primeiro seleciona apenas os dispositivos duplos de dispositivos localizados no **Redmond43** Jacinto e o segundo refines a consulta para selecionar apenas os dispositivos que também estão ligados através da rede celular.
   
    O código anterior, quando cria o **consulta** objeto, especifica um número máximo de documentos devolvidos. O **consulta** objeto contém uma **hasMoreResults** booleana propriedade que pode utilizar para invocar a **nextAsTwin** métodos múltiplas vezes para obter todos os resultados. Um método chamado **seguinte** está disponível para os resultados que são não dispositivos duplos, por exemplo, os resultados de consultas de agregação.
6. Execute a aplicação com:
   
        node AddTagsAndQuery.js
   
    Deverá ver um dispositivo nos resultados para pedir a consulta para todos os dispositivos localizados no **Redmond43** e nenhum para a consulta que restringe os resultados para dispositivos que utilizam uma rede celular.
   
    ![][1]

Na secção seguinte, criar uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo
Nesta secção, pode cria uma aplicação de consola do Node.js que liga ao seu hub como **myDeviceId**e, em seguida, as atualizações do dispositivo duplo do comunicadas propriedades para conter as informações que esteja ligada através de uma rede celular.


1. Criar uma nova pasta vazia designada **reportconnectivity**. No **reportconnectivity** pasta, crie um novo ficheiro Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **reportconnectivity** pasta, execute o seguinte comando para instalar o **azure-iot-device**, e **azure-iot-dispositivo-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo **ReportConnectivity.js** ficheiros o **reportconnectivity** pasta.
4. Adicione o seguinte código para o **ReportConnectivity.js** de ficheiros e substitua o **{cadeia de ligação do dispositivo}** marcador de posição pela cadeia de ligação de dispositivo que copiou quando criou o **myDeviceId** identidade de dispositivo:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código anterior, depois, inicializa a **cliente** objeto, obtém o dispositivo duplo para **myDeviceId** e atualiza a respetiva propriedade comunicada com as informações de conectividade.
5. Executar a aplicação de dispositivo
   
        node ReportConnectivity.js
   
    Deverá ver a mensagem `twin state reported`.
6. Agora que o dispositivo comunicou as respetivas informações de conectividade, deverão ser apresentados em ambas as consultas. Aceda novamente o **addtagsandqueryapp** pasta e volte a executar as consultas:
   
        node AddTagsAndQuery.js
   
    Neste momento **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![][3]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionar metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar estas informações utilizando a linguagem de consulta do SQL Server como o IoT Hub.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* configurar dispositivos utilizando propriedades pretendida do dispositivo duplo com a [utilização pretendida propriedades para configurar dispositivos] [ lnk-twin-how-to-configure] tutorial,
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador), com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
