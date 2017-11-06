---
title: "Carregar ficheiros de dispositivos para o Azure IoT Hub com nó | Microsoft Docs"
description: "Como carregar ficheiros a partir de um dispositivo para a nuvem com dispositivos IoT do Azure SDK para Node.js. Ficheiros carregados são armazenados num contentor do blob storage do Azure."
services: iot-hub
documentationcenter: nodejs
author: msebolt
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo
ms.openlocfilehash: 047dfd35cfef53d323774508121e22fbf47b2acf
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar ficheiros do seu dispositivo para a nuvem com o IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se sobre o código no [enviar mensagens da nuvem para o dispositivo com o IoT Hub](iot-hub-node-node-c2d.md) tutorial para lhe mostrar como utilizar o [ficheiro capacidades de carregamento do IoT Hub](iot-hub-devguide-file-upload.md) para carregar um ficheiro para [BLOBs do Azure armazenamento](../storage/index.yml). O tutorial mostra como para:

- Fornecer em segurança um dispositivo com um Azure blob URI para carregar um ficheiro.
- Utilize as notificações de carregamento de ficheiros do IoT Hub para acionar a processar o ficheiro na sua aplicação de back-end.

O [introdução ao IoT Hub](iot-hub-node-node-getstarted.md) e [enviar mensagens da nuvem para o dispositivo com o IoT Hub](iot-hub-node-node-c2d.md) os tutoriais mostram a dispositivo para nuvem e da nuvem para o dispositivo mensagens funcionalidades básicas do IoT Hub. No entanto, em alguns cenários não pode facilmente mapear os dados que os dispositivos as enviam para as mensagens dispositivo-nuvem relativamente pequenas, que aceita o IoT Hub. Por exemplo:

* Ficheiros grandes que contenham imagens
* Vídeos
* Dados vibration amostragem alta frequência
* Algum tipo de dados pré-processados.

Estes ficheiros são, normalmente, batch processado na nuvem através de ferramentas, como [do Azure Data Factory](../data-factory/introduction.md) ou [Hadoop](../hdinsight/index.md) pilha. Quando for necessário upland ficheiros de um dispositivo, pode continuar a utilizar a segurança e fiabilidade do IoT Hub.

No final deste tutorial, executar duas aplicações de consola do Node.js:

* **SimulatedDevice.js**, que carrega um ficheiro para o armazenamento através de um URI de SAS fornecida pelo seu IoT hub.
* **ReadFileUploadNotification.js**, que recebe notificações de carregamento de ficheiros do seu IoT hub.

> [!NOTE]
> IoT Hub suporta várias plataformas de dispositivos e idiomas (incluindo C, .NET, Javascript, Python e Java) através de SDKs do dispositivo IoT do Azure. Consulte o [Centro de programadores do IoT do Azure] para obter instruções passo a passo sobre como ligar o seu dispositivo ao IoT Hub do Azure.

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar um [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, vai criar a aplicação de dispositivo para carregar um ficheiro ao IoT hub.

1. Crie uma pasta vazia com o nome ```simulateddevice```.  Na pasta ```simulateddevice```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

1. Na linha de comandos da pasta ```simulateddevice```, execute o comando seguinte para instalar o pacote SDK do Serviço **azure-iot-device** e o pacote **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Com um editor de texto, crie um ficheiro **SimulatedDevice.js** na pasta ```simulateddevice```.

1. Adicione as seguintes instruções ```require``` no início do ficheiro **SimulatedDevice.js**:

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Adicione uma variável ```deviceconnectionstring``` e utilize-a para criar uma instância **Cliente**.  Substitua ```{deviceconnectionstring}``` com o nome do dispositivo que criou no _criar um IoT Hub_ secção:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Com vista à, simplicidade a cadeia de ligação está incluída no código: não é uma prática recomendada e consoante o caso de utilização e a arquitetura, convém considerar formas mais seguras de armazenar este segredo.

1. Adicione o seguinte código para ligar cliente:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Crie uma chamada de retorno e utilize o **uploadToBlob** função carregar o ficheiro.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Guarde e feche o ficheiro **SimulatedDevice.js**.

1. Copiar um ficheiro de imagem para o `simulateddevice` pasta e renomeie- `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento de ficheiros

Nesta secção, crie uma aplicação de consola do Node.js que recebe mensagens de notificação de carregamento de ficheiros a partir do IoT Hub.

Pode utilizar o **iothubowner** cadeia de ligação do seu IoT Hub para concluir esta secção. Poderá encontrar a cadeia de ligação no [portal do Azure](https://portal.azure.com/) no **política de acesso partilhado** painel.

1. Crie uma pasta vazia com o nome ```fileuploadnotification```.  Na pasta ```fileuploadnotification```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

1. Na sua linha de comandos a ```fileuploadnotification``` pasta, execute o seguinte comando para instalar o **azure iothub** pacote do SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Com um editor de texto, crie um **FileUploadNotification.js** ficheiros o ```fileuploadnotification``` pasta.

1. Adicione o seguinte ```require``` as instruções no início do **FileUploadNotification.js** ficheiro:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Adicione uma variável ```iothubconnectionstring``` e utilize-a para criar uma instância **Cliente**.  Substitua ```{iothubconnectionstring}``` com a cadeia de ligação ao IoT hub que criou no _criar um IoT Hub_ secção:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Com vista à, simplicidade a cadeia de ligação está incluída no código: não é uma prática recomendada e consoante o caso de utilização e a arquitetura, convém considerar formas mais seguras de armazenar este segredo.

1. Adicione o seguinte código para ligar cliente:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Abra o cliente e utilizar o **getFileNotificationReceiver** função para receber atualizações de estado.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Guarde e feche o **FileUploadNotification.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Numa linha de comandos do `fileuploadnotification` pasta, execute o seguinte comando:

```cmd/sh
node FileUploadNotification.js
```

Numa linha de comandos do `simulateddevice` pasta, execute o seguinte comando:

```cmd/sh
node SimulatedDevice.js
```

A seguinte captura de ecrã mostra a saída do **SimulatedDevice** aplicação:

![Resultado da aplicação simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

A seguinte captura de ecrã mostra a saída do **FileUploadNotification** aplicação:

![Resultado da aplicação de ler ficheiro-carregamento de notificação](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Pode utilizar o portal para ver o ficheiro carregado no contentor de armazenamento que configurou:

![Ficheiro foi carregado](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de carregamento de ficheiros do IoT Hub para simplificar os carregamentos de ficheiros a partir de dispositivos. Pode continuar a explorar as funcionalidades do IoT hub e cenários nos seguintes artigos:

* [Criar um hub IoT através de programação][lnk-create-hub]
* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs IoT do Azure][lnk-sdks]

<!-- Links -->
[Centro de programadores do IoT do Azure]: http://www.azure.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
