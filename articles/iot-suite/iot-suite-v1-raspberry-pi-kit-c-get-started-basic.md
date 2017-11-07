---
title: Ligar um Raspberry Pi para o Azure IoT Suite com C sensores reais | Microsoft Docs
description: "Utilize o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 e o Azure IoT Suite. Utilizar C para ligar o seu Raspberry Pi a solução de monitorização remota, enviar telemetria a partir de sensores para a nuvem e responder a métodos invocados a partir do dashboard de solução."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 635eb9d4e85eaf43dc83f2bd5a0d0f7c32620d98
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>Ligar a sua 3 de Pi Raspberry a solução de monitorização remota e enviar telemetria a partir de um sensor real utilizando C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Este tutorial mostra como utilizar o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 para desenvolver um leitor de temperatura e humidade que possa comunicar com a nuvem. O tutorial utiliza:

- SO de Raspbian, a linguagem de programação C e o SDK do Microsoft Azure IoT para C para implementar um dispositivo de exemplo.
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

### <a name="clone-the-repositories"></a>Clonar repositórios do

Se ainda não o fez, clone repositórios do necessários executando os seguintes comandos num terminal no seu Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>Atualizar a cadeia de ligação do dispositivo

Abra o ficheiro de origem de exemplo no **nano** editor utilizando o seguinte comando:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
```

Localize as seguintes linhas:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Substitua os valores de marcador de posição pelo dispositivo e informações de IoT Hub é criado e guardado no início deste tutorial. Guardar as alterações (**Ctrl-O**, **Enter**) e saia do editor (**Ctrl-X**).

## <a name="build-the-sample"></a>Criar a amostra

Instale os pacotes de pré-requisitos para o SDK de dispositivos do IoT do Microsoft Azure para C, executando os comandos seguintes num terminal no Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Agora pode compilar a solução de exemplo atualizado no Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

Pode agora executar o programa de exemplo no Raspberry Pi. Introduza o comando:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

A saída de exemplo seguinte é um exemplo de saída, consulte a linha de comandos no Raspberry Pi:

![Resultado da aplicação Raspberry Pi][img-raspberry-output]

Prima **Ctrl-C** para sair do programa em qualquer altura.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Passos seguintes

Visite o [Dev Center do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e documentação no Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
