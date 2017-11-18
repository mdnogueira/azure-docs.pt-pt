---
title: "Agendar tarefas IoT hub do Azure (nó) | Microsoft Docs"
description: "Como agendar um trabalho do IoT Hub do Azure para invocar um método direto em vários dispositivos. Utilize os SDKs IoT do Azure para Node.js para implementar as aplicações de dispositivo simulada e uma aplicação de serviço para executar a tarefa."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: e607f5db8b4f2a974cb172d4581dadefe7851275
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="schedule-and-broadcast-jobs-node"></a>Tarefas de agendamento e de difusão (nó)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

IoT Hub do Azure é um serviço completamente gerido que permite uma aplicação de back-end criar e controlar as tarefas agendadas e atualizar milhões de dispositivos.  As tarefas podem ser utilizadas para as seguintes ações:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Concecionais, uma tarefa encapsula num wrapper uma destas ações e controla o progresso de execução face a um conjunto de dispositivos, que é definido por uma consulta do dispositivo duplo.  Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método de reinício 10 000 dispositivos, especificado por uma consulta do dispositivo duplo e agendadas num momento futuro.  Essa aplicação, em seguida, pode acompanhar o progresso como cada um desses dispositivos receber e executar o método de reinício.

Saiba mais sobre cada uma destas capacidades nestes artigos:

* Dispositivo duplo e propriedades: [começar a utilizar dispositivos duplos] [ lnk-get-started-twin] e [Tutorial: como utilizar as propriedades do dispositivo duplo][lnk-twin-props]
* direcionar métodos: [guia para programadores do IoT Hub - métodos diretas] [ lnk-dev-methods] e [Tutorial: direcionar métodos][lnk-c2d-methods]

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo simulado de Node.js que tem um método direto, o que lhe permite **lockDoor**, que pode ser chamada pelo solução de back-end.
* Criar uma aplicação de consola do Node.js que chama o **lockDoor** método direto na aplicação do dispositivo simulado utilizando uma tarefa e atualizações as propriedades de pretendido utilizando uma tarefa de dispositivo.

No final deste tutorial, tem duas aplicações Node.js:

**simDevice.js**, que liga ao seu IoT hub com a identidade de dispositivo e recebe um **lockDoor** método direto.

**scheduleJobService.js**, que chama um método direto na aplicação do dispositivo simulado e atualiza o dispositivo duplo pretendido propriedades utilizando uma tarefa.

Para concluir este tutorial, precisa do seguinte:

* Versão do node.js 4.0.x ou posterior <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Nesta secção, criar uma aplicação de consola do Node.js que responde a um método direto chamado pela nuvem, o que aciona um simulada **lockDoor** método.

1. Criar uma nova pasta vazia designada **simDevice**.  No **simDevice** pasta, crie um ficheiro de Package. JSON utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **simDevice** pasta, execute o seguinte comando para instalar o **azure-iot-device** pacote do SDK do dispositivo e **azure-iot-dispositivo-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Com um editor de texto, crie um novo **simDevice.js** ficheiros o **simDevice** pasta.
4. Adicione o seguinte 'exigir' instruções no início do **simDevice.js** ficheiro:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função para processar o **lockDoor** método.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Adicione o seguinte código para registar o processador para o **lockDoor** método.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Guarde e feche o **simDevice.js** ficheiro.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar tarefas para chamar um método direto e atualizar as propriedades de um dispositivo duplo
Nesta secção, criar uma aplicação de consola do Node.js que inicia um remoto **lockDoor** num dispositivo utilizando um método direto e atualizar as propriedades do dispositivo duplo.

1. Criar uma nova pasta vazia designada **scheduleJobService**.  No **scheduleJobService** pasta, crie um ficheiro de Package. JSON utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **scheduleJobService** pasta, execute o seguinte comando para instalar o **azure iothub** pacote do SDK do dispositivo e **azure-iot-dispositivo-mqtt** pacote de:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Com um editor de texto, crie um novo **scheduleJobService.js** ficheiros o **scheduleJobService** pasta.
4. Adicione o seguinte 'exigir' instruções no início do **dmpatterns_gscheduleJobServiceetstarted_service.js** ficheiro:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Adicione as seguintes declarações de variável e substitua os valores de marcador de posição:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Adicione a seguinte função que é utilizada para monitorizar a execução da tarefa:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. Adicione o seguinte código para agendar a tarefa que chama o método de dispositivo:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. Adicione o seguinte código para agendar a tarefa para atualizar o dispositivo duplo:
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. Guarde e feche o **scheduleJobService.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações
Pode agora executar as aplicações.

1. Na linha de comandos no **simDevice** pasta, execute o seguinte comando para começar a escutar o método direta de reinício.
   
    ```
    node simDevice.js
    ```
2. Na linha de comandos no **scheduleJobService** pasta, execute o seguinte comando para acionar as tarefas para bloquear a porta e atualizar o duplo
   
    ```
    node scheduleJobService.js
    ```
3. Pode ver a resposta de dispositivo para o método direto na consola do.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, uma tarefa que utilizou para agendar um método direto para um dispositivo e a atualização das propriedades do dispositivo duplo.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota através da atualização de firmware ondas eletromagnéticas, consulte:

[Tutorial: Como efetuar uma atualização de firmware][lnk-fwupdate]

Para continuar a introdução ao IoT Hub, consulte [introdução ao Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
