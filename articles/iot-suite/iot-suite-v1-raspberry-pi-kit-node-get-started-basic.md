---
title: Ligar um Raspberry Pi para o Azure IoT Suite com o Node.js com sensores reais | Microsoft Docs
description: "Utilize o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 e o Azure IoT Suite. Utilize Node.js para ligar o seu Raspberry Pi a solução de monitorização remota, enviar telemetria a partir de sensores para a nuvem e responder a métodos invocados a partir do dashboard de solução."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bb4f62b1a3de68ce8796b60fe76cd97b9ab9ade
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>Ligar a sua 3 de Pi Raspberry a solução de monitorização remota e enviar telemetria a partir de um sensor real com o Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Este tutorial mostra como utilizar o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 para desenvolver um leitor de temperatura e humidade que possa comunicar com a nuvem. O tutorial utiliza:

- SO de Raspbian, a linguagem de programação do Node.js e o Microsoft Azure IoT SDK para Node.js para implementar um dispositivo de exemplo.
- O IoT Suite remoto solução pré-configurada de monitorização como o back-end baseado na nuvem.

## <a name="overview"></a>Descrição geral

Neste tutorial, conclua os seguintes passos:

- Implemente uma instância da solução pré-configurada de monitorização remota à sua subscrição do Azure. Este passo automaticamente implementa e configura vários serviços do Azure.
- Configure os dispositivos e os sensores para comunicar com o seu computador e a solução de monitorização remota.
- Atualize o código de dispositivo de exemplo para ligar a solução de monitorização remota e enviar telemetria que pode ver no dashboard da solução.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitorização remota aprovisiona um conjunto de serviços do Azure na sua subscrição do Azure. A implementação reflete uma arquitetura de empresas reais. Para evitar custos de consumo do Azure desnecessários, elimine a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado com o mesmo. Se precisar de novamente a solução pré-configurada, pode recriá-lo facilmente. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Transferir e configurar o exemplo

Agora pode transferir e configurar a aplicação de cliente de monitorização remoto no seu Raspberry Pi.

### <a name="install-nodejs"></a>Instalar o Node.js

Instale o Node.js no seu Raspberry Pi. O SDK de IoT para Node.js requer a versão 0.11.5 do Node.js ou posterior. Os passos seguintes mostram como instalar o Node.js v6.10.2 no seu Raspberry Pi:

1. Utilize o seguinte comando para atualizar o seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Utilize o seguinte comando para transferir os binários de Node.js para a sua Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Utilize o seguinte comando para instalar os binários de:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Utilize o seguinte comando para verificar que instalou o Node.js v6.10.2 com êxito:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Clonar repositórios do

Se ainda não o fez, clone repositórios do necessários executando os seguintes comandos no seu Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`
```

### <a name="update-the-device-connection-string"></a>Atualizar a cadeia de ligação do dispositivo

Abra o ficheiro de origem de exemplo no **nano** editor utilizando o seguinte comando:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Localize a linha:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Substitua os valores de marcador de posição pelo dispositivo e informações de IoT Hub é criado e guardado no início deste tutorial. Guardar as alterações (**Ctrl-O**, **Enter**) e saia do editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Executar o exemplo

Execute os seguintes comandos para instalar os pacotes de pré-requisitos para o exemplo:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic
npm install
```

Pode agora executar o programa de exemplo no Raspberry Pi. Introduza o comando:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

A saída de exemplo seguinte é um exemplo de saída, consulte a linha de comandos no Raspberry Pi:

![Resultado da aplicação Raspberry Pi][img-raspberry-output]

Prima **Ctrl-C** para sair do programa em qualquer altura.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Passos seguintes

Visite o [Dev Center do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e documentação no Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
