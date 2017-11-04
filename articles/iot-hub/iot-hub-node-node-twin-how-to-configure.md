---
title: "Utilize as propriedades do IoT Hub do Azure dispositivo duplo (nó) | Microsoft Docs"
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para configurar dispositivos. Utilize os SDKs IoT do Azure para Node.js para implementar uma aplicação de dispositivo simulada e uma aplicação de serviço que modifica uma configuração de dispositivo utilizando um dispositivo duplo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: 6ff6f1c331d5a77e7ac0a47af6806f5d90fb0fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices-node"></a>Utilize as propriedades do pretendido para configurar dispositivos (nó)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No final deste tutorial, terá de duas aplicações de consola do Node.js:

* **SimulateDeviceConfiguration.js**, uma aplicação de dispositivo simulado que aguarda uma atualização da configuração pretendida e reporta o estado de um processo de atualização da configuração simulada.
* **SetDesiredConfigurationAndQuery.js**, uma aplicação de back-end Node.js, que define a configuração pretendida num dispositivo e consulta o processo de atualização da configuração.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Se seguiu o [começar a utilizar dispositivos duplos] [ lnk-twin-tutorial] tutorial, já tiver um IoT hub e uma identidade de dispositivo chamado **myDeviceId**; e poderá avançar para o [criar a aplicação de dispositivo simulado] [ lnk-how-to-configure-createapp] secção.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>Criar a aplicação de dispositivo simulada
Nesta secção, pode cria uma aplicação de consola do Node.js que liga ao seu hub como **myDeviceId**, tem de aguardar durante uma atualização da configuração pretendida e, em seguida, os relatórios de atualizações no processo de atualização de configuração simulada.

1. Criar uma nova pasta vazia designada **simulatedeviceconfiguration**. No **simulatedeviceconfiguration** pasta, crie um novo ficheiro Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **simulatedeviceconfiguration** pasta, execute o seguinte comando para instalar o **azure-iot-device**, e **azure-iot-dispositivo-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo **SimulateDeviceConfiguration.js** ficheiros o **simulatedeviceconfiguration** pasta.
4. Adicione o seguinte código para o **SimulateDeviceConfiguration.js** de ficheiros e substitua o **{cadeia de ligação do dispositivo}** marcador de posição pela cadeia de ligação de dispositivo que copiou quando criou o **myDeviceId** identidade de dispositivo:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código anterior, depois, inicializa a **cliente** objeto, obtém o dispositivo duplo para **myDeviceId**e anexa um processador para a atualização nos propriedades pretendidas. O processador verifica que existe é um pedido de alteração da configuração real comparando a configIds, em seguida, invoca um método que inicia a alteração da configuração.
   
    Tenha em atenção que, com vista à, simplicidade, do código anterior utiliza uma predefinição hard-coded para a configuração inicial. Uma aplicação real, provavelmente, seria carregar que a configuração de um local de armazenamento.
   
   > [!IMPORTANT]
   > Eventos de alteração de propriedade pretendido sempre são emitidos uma vez na ligação do dispositivo, certifique-se de que verifique se existe uma alteração real nas propriedades de pretendido antes de efetuar qualquer ação.
   > 
   > 
5. Adicione os métodos seguintes antes do `client.open()` invocação:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    O **initConfigChange** método atualiza comunicadas propriedades no objecto de duplo do dispositivo local com o pedido de atualização de configuração e define o estado para **pendente**, em seguida, atualiza o dispositivo duplo no serviço. Depois de atualizar com êxito o dispositivo duplo, simula um processo de execução longa que termina na execução da **completeConfigChange**. Este método atualizações propriedades comunicado o duplo dispositivo local definir o estado para **êxito** e remover o **pendingConfig** objeto. Em seguida, atualiza o dispositivo duplo no serviço.
   
    Tenha em atenção que, para poupar largura de banda, comunicados propriedades são atualizadas ao especificar apenas as propriedades de ser modificados (com o nome **patch** no código acima), em vez de substituir todo o documento.
   
   > [!NOTE]
   > Este tutorial não simular nenhum comportamento de atualizações de configuração em simultâneo. Alguns processos de atualização de configuração poderão acomodar as alterações de configuração de destino enquanto a atualização está em execução, outros poderão ter de fila-los e outros rejeitá-las com uma condição de erro. Certifique-se a ter em consideração o comportamento pretendido para o processo de configuração específica e adicione a lógica adequada antes de iniciar a alteração da configuração.
   > 
   > 
6. Execute a aplicação de dispositivo:
   
        node SimulateDeviceConfiguration.js
   
    Deverá ver a mensagem `retrieved device twin`. Manter a aplicação em execução.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, irá criar uma aplicação de consola do Node.js que atualiza o *pretendido propriedades* no dispositivo duplo associado **myDeviceId** com um novo objeto de configuração de telemetria. Em seguida, consulta os dispositivos duplos armazenados no IoT hub e mostra a diferença entre as configurações desejadas e comunicadas do dispositivo.

1. Criar uma nova pasta vazia designada **setdesiredandqueryapp**. No **setdesiredandqueryapp** pasta, crie um novo ficheiro Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **setdesiredandqueryapp** pasta, execute o seguinte comando para instalar o **azure iothub** pacote:
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. Com um editor de texto, crie um novo **SetDesiredAndQuery.js** ficheiros o **setdesiredandqueryapp** pasta.
4. Adicione o seguinte código para o **SetDesiredAndQuery.js** de ficheiros e substitua o **{cadeia de ligação do hub iot}** marcador de posição pela cadeia de ligação do IoT Hub que copiou quando criou o seu hub:
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    O **registo** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. O código anterior, depois, inicializa a **registo** objeto, obtém o dispositivo duplo para **myDeviceId**e atualiza as respetivas propriedades pretendidas com um novo objeto de configuração de telemetria. Depois disso, aquele invoca o **queryTwins** funcionar eventos 10 segundos.

    > [!IMPORTANT]
    > Esta aplicação consulta IoT Hub a cada 10 segundos para fins de ilustrativa. Utilize consultas para gerar relatórios de destinada ao utilizador entre vários dispositivos e não para detetar as alterações. Se a sua solução requer notificações em tempo real de eventos do dispositivo, utilize [duplo notificações][lnk-twin-notifications].
    > 
    >.

1. Adicione o direito de código seguintes antes do `registry.getDeviceTwin()` invocação para implementar o **queryTwins** função:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    As consultas de código anterior dispositivos duplos armazenados no IoT hub e imprime as configurações de telemetria pretendido e comunicados. Consulte o [idioma de consulta do IoT Hub] [ lnk-query] para saber como gerar relatórios avançados em todos os seus dispositivos.
2. Com **SimulateDeviceConfiguration.js** em execução, execute a aplicação com:
   
        node SetDesiredAndQuery.js 5m
   
    Deverá ver a alteração de configuração comunicada **êxito** para **pendente** para **êxito** novamente com o novo ativo de enviar uma frequência de cinco minutos em vez de 24 horas.
   
   > [!IMPORTANT]
   > Não há um atraso de até um minuto entre a operação de relatório do dispositivo e o resultado da consulta. Isto é permitir que a infraestrutura de consulta para trabalhar em grande escala. Para obter vistas consistentes de utilização de duplo um único dispositivo de **getDeviceTwin** método o **registo** classe.
   > 
   > 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou uma configuração desejada como *pretendido propriedades* a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para detetar essa alteração e simular um processo de atualização de vários passos reporting o estado dele como *comunicadas propriedades* para o dispositivo duplo.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* agendar ou efetuar operações em grandes conjuntos de dispositivos Consulte o [agenda e as tarefas de difusão] [ lnk-schedule-jobs] tutorial.
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador), com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
