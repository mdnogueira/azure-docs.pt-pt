---
title: Simular um dispositivo com o limite de IoT do Azure (Linux) | Microsoft Docs
description: "Como utilizar o Azure IoT Edge no Linux para criar um dispositivo simulado que envia telemetria através de um gateway de IoT a um IoT hub."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: andbuc
ms.openlocfilehash: 5349960373ae6815862c5f79a69dd6d5d9d624ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Utilizar o Azure IoT Edge para enviar mensagens do dispositivo-nuvem com um dispositivo simulado (Linux)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Como executar o exemplo

O script **build.sh** gera o respetivo resultado na pasta **build** na sua cópia local do repositório **iot-edge**. Este resultado inclui os quatro módulos de limite de IoT utilizados neste exemplo.

O script build coloca o:

* **liblogger.SO** no **módulos/compilação/logger** pasta.
* **libiothub.SO** no **módulos/compilação/iothub** pasta.
* **lib\_identidade\_map.so** no **módulos/compilação/identitymap** pasta.
* **libsimulated\_device.so** no **compilação/módulos/simulated\_dispositivo** pasta.

Utilize estes caminhos para o **do módulo caminho** valores conforme mostrado no seguinte ficheiro de definições JSON:

O simulada\_dispositivo\_nuvem\_carregar\_processo de exemplo utiliza o caminho para um ficheiro de configuração JSON como um argumento da linha de comandos. O ficheiro JSON de exemplo seguinte é fornecido no repositório de SDK em **amostras\\simulada\_dispositivo\_nuvem\_carregar\_exemplo\\src\\simulada\_dispositivo\_nuvem\_carregar\_exemplo\_lin.json**. Este ficheiro de configuração funciona conforme é salvo se modificar o script de compilação para colocar os módulos de limite de IoT ou executáveis de exemplo em localizações não predefinidas.

> [!NOTE]
> Os caminhos do módulo são relativos ao diretório de onde executa a simulada\_dispositivo\_nuvem\_carregar\_executável de exemplo, não o diretório onde está localizado o ficheiro executável. O ficheiro de configuração do JSON de exemplo está predefinida para escrever 'deviceCloudUploadGatewaylog.log' no seu diretório de trabalho atual.

Num editor de texto, abra o ficheiro **amostras/simulated\_dispositivo\_nuvem\_carregar\_exemplo/src/simulated\_dispositivo\_nuvem\_carregar\_lin.json** na sua cópia local do **iot edge** repositório. Este ficheiro configura os módulos de limite de IoT no gateway de exemplo:

* O **IoTHub** módulo liga ao seu IoT hub. Configurar para enviar dados para o seu IoT hub. Especificamente, defina o **IoTHubName** valor com o nome do seu IoT hub e defina o **IoTHubSuffix** valor **azure devices.net**. Definir o **transporte** valor a uma das: **HTTP**, **AMQP**, ou **MQTT**. Atualmente, apenas **HTTP** partilha uma ligação de TCP para todas as mensagens do dispositivo. Se definir o valor para **AMQP**, ou **MQTT**, o gateway mantém uma ligação de TCP separada ao IoT Hub para cada dispositivo.
* O **mapeamento** módulo mapeia os endereços MAC dos seus dispositivos simulados para os ids de dispositivo IoT Hub. Certifique-se de que **deviceId** valores correspondem aos ids dos dois dispositivos que adicionou ao seu IoT hub e de que o **deviceKey** valores contêm as chaves dos dois dispositivos.
* O **BLE1** e **BLE2** módulos são os dispositivos simulados. Tenha em atenção a forma como os respetivos endereços MAC correspondem aos endereços de **mapeamento** módulo.
* O **registador** módulo regista a atividade de gateway num ficheiro.
* O **do módulo caminho** valores mostrados no exemplo partem do princípio de que execute o exemplo a **criar** pasta na sua cópia local do **iot edge** repositório.
* O **ligações** matriz na parte inferior do ficheiro JSON liga o **BLE1** e **BLE2** módulos para a **mapeamento** módulo e o **mapeamento** módulo para a **IoTHub** módulo. Também garante que todas as mensagens são registadas pelo **registador** módulo.

```json
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
            }
            },
            "args": {
              "macAddress": "01:01:01:01:01:01"
            }
          },
        {
            "name": "BLE2",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/simulated_device/libsimulated_device.so"
            }
            },
            "args": {
              "macAddress": "02:02:02:02:02:02"
            }
          },
        {
            "name": "Logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

Guarde as alterações efetuadas ao ficheiro de configuração.

Para executar o exemplo:

1. Na sua shell, navegue para o **iot-edge/compilação** pasta.
2. Execute o seguinte comando:
   
    ```sh
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Pode utilizar o [Explorador de dispositivo] [ lnk-device-explorer] ou [iothub explorer] [ lnk-iothub-explorer] ferramenta para monitorizar as mensagens que recebe o IoT hub do gateway. Por exemplo, através do Explorador do iothub pode monitorizar as mensagens do dispositivo para a nuvem utilizando o seguinte comando:

    ```sh
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Passos seguintes

Para obter uma compreensão mais avançada de limite de IoT do Azure e experimente alguns exemplos de código, visite as seguintes tutoriais para programadores e os recursos:

* [Enviar mensagens do dispositivo para nuvem a partir de um dispositivo físico com o Azure IoT Edge][lnk-physical-device]
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
