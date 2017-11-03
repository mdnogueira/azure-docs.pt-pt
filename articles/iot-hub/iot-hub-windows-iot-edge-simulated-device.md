---
title: Simular um dispositivo com o limite de IoT do Azure (Windows) | Microsoft Docs
description: "Como utilizar o Azure IoT Edge no Windows para criar um dispositivo simulado que envia telemetria através de um gateway do Azure IoT limite a um IoT hub."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Utilizar o Azure IoT Edge para enviar mensagens do dispositivo-nuvem com um dispositivo simulado (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Executar o exemplo

O **build.cmd** script gera o respetivo resultado no **criar** pasta na sua cópia local do **iot edge** repositório. Este resultado inclui os quatro módulos de limite de IoT utilizados neste exemplo.

O script de compilação cria os seguintes ficheiros:

* **Logger.dll** no **criar\\módulos\\registador\\depurar** pasta.
* **iothub.dll** no **criar\\módulos\\iothub\\depurar** pasta.
* **identidade\_map.dll** no **criar\\módulos\\identitymap\\depurar** pasta.
* **simulada\_device.dll** no **criar\\módulos\\simulada\_dispositivo\\depurar** pasta.

Utilize estes caminhos para o **do módulo caminho** valores conforme mostrado no simulada\_dispositivo\_nuvem\_carregar\_win o ficheiro de definições JSON.

O simulada\_dispositivo\_nuvem\_processo de exemplo de carregamento demora o caminho para um ficheiro de configuração JSON como um argumento da linha de comandos. O ficheiro JSON de exemplo seguinte é fornecido no repositório de SDK em **amostras\\simulada\_dispositivo\_nuvem\_carregar\_exemplo\\src\\ simulada\_dispositivo\_nuvem\_carregar\_win.json**. Este ficheiro de configuração funciona conforme é salvo se modificar o script de compilação para colocar os módulos de limite de IoT ou executáveis de exemplo em localizações não predefinidas.

> [!NOTE]
> Os caminhos do módulo são relativos ao diretório onde o simulada\_dispositivo\_nuvem\_carregar\_sample.exe está localizado. O ficheiro de configuração do JSON de exemplo está predefinida para escrever 'deviceCloudUploadGatewaylog.log' no seu diretório de trabalho atual.

Num editor de texto, abra o ficheiro **amostras\\simulada\_dispositivo\_nuvem\_carregar\\src\\simulada\_dispositivo\_nuvem\_carregar\_win.json** na sua cópia local do **iot edge** repositório. Este ficheiro configura os módulos de limite de IoT no gateway de exemplo:

* O **IoTHub** módulo liga ao seu IoT hub. Configurar para enviar dados para o seu IoT hub. Especificamente, defina o **IoTHubName** valor com o nome do seu IoT hub e defina o **IoTHubSuffix** valor **azure devices.net**. Definir o **transporte** valor a uma das: **HTTP**, **AMQP**, ou **MQTT**. Atualmente, apenas **HTTP** partilha uma ligação de TCP para todas as mensagens do dispositivo. Se definir o valor para **AMQP**, ou **MQTT**, o gateway mantém uma ligação de TCP separada ao IoT Hub para cada dispositivo.
* O **mapeamento** módulo mapeia os endereços MAC dos seus dispositivos simulados para sua os Ids do dispositivo IoT Hub. Definir o **deviceId** valores para os Ids de dois dispositivos que adicionou ao seu IoT hub. Definir o **deviceKey** valores para as chaves do seu dois dispositivos.
* O **BLE1** e **BLE2** módulos são os dispositivos simulados. Tenha em atenção a forma como os endereços MAC de módulo correspondem aos endereços de **mapeamento** módulo.
* O **registador** módulo regista a atividade de gateway num ficheiro.
* O **do módulo caminho** valores mostrados no exemplo a seguir são relativos ao diretório onde o simulada\_dispositivo\_nuvem\_carregar\_sample.exe está localizado.
* O **ligações** matriz na parte inferior do ficheiro JSON liga o **BLE1** e **BLE2** módulos para a **mapeamento** módulo e o **mapeamento** módulo para a **IoTHub** módulo. Também garante que todas as mensagens são registadas pelo **registador** módulo.

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

Guarde as alterações efetuadas ao ficheiro de configuração.

Para executar o exemplo:

1. Numa linha de comandos, navegue para o **criar** pasta na sua cópia local do **iot edge** repositório.
2. Execute o seguinte comando:
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Pode utilizar o [Explorador de dispositivo] [ lnk-device-explorer] ou [iothub explorer] [ lnk-iothub-explorer] ferramenta para monitorizar as mensagens que recebe o IoT hub do gateway. Por exemplo, através do Explorador do iothub pode monitorizar as mensagens do dispositivo para a nuvem utilizando o seguinte comando:

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Passos seguintes

Para obter uma compreensão mais avançada de limite de IoT e experimente alguns exemplos de código, visite as seguintes tutoriais para programadores e os recursos:

* [Enviar mensagens do dispositivo para nuvem a partir de um dispositivo físico com o limite de IoT][lnk-physical-device]
* [Limite de IoT do Azure][lnk-iot-edge]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Proteger a sua solução de IoT a partir do zero cópias de segurança][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
