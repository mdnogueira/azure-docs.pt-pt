---
title: Ligar um gateway para o Azure IoT Suite utilizando um NUC Intel | Microsoft Docs
description: "Utilize o Kit de Gateway do comerciais IoT do Microsoft e a solução pré-configurada de monitorização remota. Utilize o gateway do limite de IoT do Azure para ligar a solução de monitorização remota, enviar a telemetria simulada para a nuvem e responder a métodos invocados a partir do dashboard de solução."
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
ms.openlocfilehash: f3b4a80b37b0b869847c90834731d4c23883a961
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Ligar o gateway do limite de IoT do Azure para a solução pré-configurada de monitorização remota e enviar a telemetria simulada

[!INCLUDE [iot-suite-v1-gateway-kit-selector](../../includes/iot-suite-v1-gateway-kit-selector.md)]

Este tutorial mostra como utilizar o Azure IoT Edge para simular dados relativos à temperatura e humidade para enviar para a solução pré-configurada de monitorização remota. O tutorial utiliza:

- Limite de IoT do Azure para implementar um gateway de exemplo.
- O IoT Suite remoto solução pré-configurada de monitorização como o back-end baseado na nuvem.

## <a name="overview"></a>Descrição geral

Neste tutorial, conclua os seguintes passos:

- Implemente uma instância da solução pré-configurada de monitorização remota à sua subscrição do Azure. Este passo automaticamente implementa e configura vários serviços do Azure.
- Configure o dispositivo de gateway Intel NUC para comunicar com o seu computador e a solução de monitorização remota.
- Configure o gateway de IoT Edge para enviar a telemetria simulada que pode ver no dashboard da solução.

[!INCLUDE [iot-suite-v1-gateway-kit-prerequisites](../../includes/iot-suite-v1-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitorização remota aprovisiona um conjunto de serviços do Azure na sua subscrição do Azure. A implementação reflete uma arquitetura de empresas reais. Para evitar custos de consumo do Azure desnecessários, elimine a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado com o mesmo. Se precisar de novamente a solução pré-configurada, pode recriá-lo facilmente. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-gateway-kit-view-solution](../../includes/iot-suite-v1-gateway-kit-view-solution.md)]

Repita os passos anteriores para adicionar um segundo dispositivo com um ID de dispositivo, tal como **device02**. O exemplo envia dados de dois dispositivos simulados no gateway para a solução de monitorização remota.

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-connectivity.md)]

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-software](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Criar o módulo de limite de IoT personalizado

Agora pode compilar o módulo de limite de IoT personalizado que permite que o gateway enviar mensagens para a solução de monitorização remota. Para obter mais informações sobre como configurar um gateway e os módulos de limite de IoT, consulte [conceitos do Azure IoT Edge][lnk-gateway-concepts].

Transferir o código de origem para os módulos de limite de IoT personalizados a partir do GitHub com os seguintes comandos:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Crie o módulo de limite de IoT personalizado utilizando os seguintes comandos:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

O script de compilação coloca o módulo de limite de IoT personalizado libsimulator.so na pasta de compilação.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurar e executar o gateway de IoT Edge

Agora, pode configurar o gateway de IoT Edge para enviar a telemetria simulada para o dashboard de monitorização remota. Para obter mais informações sobre como configurar um gateway e os módulos de limite de IoT, consulte [conceitos do Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> Neste tutorial, utiliza a norma `vi` editor de texto no NUC Intel. Se não tiver utilizado `vi` anteriormente, deve efetuar um tutorial introdutórias, tais como [Unix - o vi Editor Tutorial] [ lnk-vi-tutorial] para se familiarizar deste editor. Em alternativa, pode instalar mais intuitivo [nano](https://www.nano-editor.org/) editor utilizando o comando `smart install nano -y`.

Abra o ficheiro de configuração de exemplo no **vi** editor utilizando o seguinte comando:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
```

Localize as seguintes linhas na configuração para o módulo de IoTHub:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Substitua os valores de marcador de posição com as informações do IoT Hub é criado e guardado no início deste tutorial. O valor para IoTHubName aspeto **yourrmsolution37e08**, e o valor de IoTSuffix é normalmente **azure devices.net**.

Localize as seguintes linhas na configuração para o módulo de mapeamento:

```json
args": [
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Substitua o **deviceID** e **deviceKey** marcadores de posição com os IDs e as chaves para os dois dispositivos na solução de monitorização remota que criou anteriormente.

Guarde as alterações.

Agora, pode executar o gateway de IoT limite utilizando os seguintes comandos:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

O gateway é iniciado num NUC Intel e envia a telemetria simulada para a solução de monitorização remota:

![Gateway de IoT gera a telemetria simulada][img-simulated telemetry]

Prima **Ctrl-C** para sair do programa em qualquer altura.

## <a name="view-the-telemetry"></a>Ver a telemetria

O gateway de IoT está agora a enviar telemetria simulada à solução de monitorização remota. Pode ver a telemetria no dashboard da solução.

- Navegue para o dashboard de solução.
- Selecione um dos dois dispositivos que configurou no gateway no **dispositivo para a vista** pendente.
- Apresenta a telemetria dos dispositivos de gateway no dashboard.

![Apresentar telemetria dos dispositivos simulados gateway][img-telemetry-display]

> [!WARNING]
> Se deixar a solução de monitorização remota em execução na sua conta do Azure, é-lhe faturado durante o período de tempo que é executada. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config]. Elimine a solução pré-configurada da sua conta do Azure quando tiver concluído a utilizá-la.

## <a name="next-steps"></a>Passos seguintes

Visite o [Dev Center do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e documentação no Azure IoT.

[img-simulated telemetry]: ./media/iot-suite-v1-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-v1-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started