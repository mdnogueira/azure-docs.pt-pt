---
title: "Atualização de firmware do dispositivo IoT hub do Azure (nó) | Microsoft Docs"
description: "Como utilizar a gestão de dispositivos no IoT Hub do Azure para iniciar uma atualização de firmware do dispositivo. Utilize os SDKs IoT do Azure para Node.js para implementar uma aplicação de dispositivo simulada e uma aplicação de serviço que aciona a atualização de firmware."
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
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e169367592b25ea45c3d1017937316a3b3b538b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Utilize a gestão de dispositivos para iniciar uma atualização de firmware do dispositivo (nó/nó)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

No [introdução à gestão de dispositivos] [ lnk-dm-getstarted] tutorial, vimos como utilizar o [dispositivo duplo] [ lnk-devtwin] e [direcionar métodos] [ lnk-c2dmethod] primitivos reiniciar remotamente um dispositivo. Este tutorial utiliza os mesmos primitivos do IoT Hub e fornece orientações e mostra como efetuar uma atualização de firmware simulada ponto-a-ponto.  Este padrão é utilizado na implementação de atualização de firmware para o exemplo de dispositivo Intel Edison.

Este tutorial mostrar-lhe como:

* Crie uma aplicação de consola do Node.js que chama o método direto firmwareUpdate na aplicação do dispositivo simulado através do seu IoT hub.
* Criar uma aplicação de dispositivo simulado que implementa um **firmwareUpdate** método direto. Este método inicia um processo de fase multi que aguarda para transferir a imagem de firmware, transfere a imagem de firmware e, finalmente, aplica-se a imagem do firmware. Durante cada fase da atualização, o dispositivo utiliza as propriedades que relatados para elaborar relatórios sobre o progresso.

No final deste tutorial, tem duas aplicações de consola do Node.js:

**dmpatterns_fwupdate_service.js**, que chama um método direto na aplicação do dispositivo simulado, mostra a resposta e periodicamente (cada 500ms) apresenta a atualização comunicado propriedades.

**dmpatterns_fwupdate_device.js**, que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente, recebe um método direto firmwareUpdate, é executada através de um processo com múltiplos estado para simular a atualização de firmware, incluindo: a aguardar que a transferência da imagem, transferir a nova imagem e, finalmente, aplicar a imagem.

Para concluir este tutorial, precisa do seguinte:

* Versão do node.js 4.0.x ou posterior <br/>  [Preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Siga o [introdução à gestão de dispositivos](iot-hub-node-node-device-management-get-started.md) artigo para criar o hub IoT e obter a cadeia de ligação do IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Acionar uma atualização de firmware remota no dispositivo com um método direto
Nesta secção, crie uma aplicação de consola do Node.js que inicia uma atualização de firmware remota num dispositivo. A aplicação utiliza um método direto para iniciar a atualização e utiliza consultas de duplo de dispositivo para obter periodicamente o estado da atualização de firmware do Active Directory.

1. Crie uma pasta vazia designada **triggerfwupdateondevice**.  No **triggerfwupdateondevice** pasta, crie um ficheiro de Package. JSON utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:
   
    ```
    npm init
    ```
2. Na sua linha de comandos a **triggerfwupdateondevice** pasta, execute o seguinte comando para instalar o **azure-iot-hub** pacote:
   
    ```
    npm install azure-iothub --save
    ```
3. Com um editor de texto, crie um **dmpatterns_getstarted_service.js** ficheiros o **triggerfwupdateondevice** pasta.
4. Adicione o seguinte 'exigir' instruções no início do **dmpatterns_getstarted_service.js** ficheiro:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adicione as seguintes declarações de variável e substitua os valores de marcador de posição:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Adicione a seguinte função para localizar e apresentar o valor da firmwareUpdate comunicado propriedade.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Adicione a seguinte função para invocar o método firmwareUpdate para reiniciar o dispositivo de destino:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Por fim, adicione a seguinte função código para iniciar a sequência de atualização de firmware e iniciar periodicamente que mostra as propriedades que relatados:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Guarde e feche o **dmpatterns_fwupdate_service.js** ficheiro.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Executar as aplicações
Já está pronto para executar as aplicações.

1. Na linha de comandos no **manageddevice** pasta, execute o seguinte comando para começar a escutar o método direta de reinício.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Na linha de comandos no **triggerfwupdateondevice** pasta, execute o seguinte comando para acionar a reiniciar o computador remoto e a consulta para o dispositivo duplo localizar a última hora de reinício.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Pode ver a resposta de dispositivo para o método direto na consola do.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, é utilizado um método direto para acionar uma atualização de firmware remota num dispositivo e utilizado as propriedades que relatados para seguir o progresso da atualização de firmware.

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
