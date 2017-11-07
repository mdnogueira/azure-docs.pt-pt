---
title: Ligar um gateway para o Azure IoT Suite utilizando um NUC Intel | Microsoft Docs
description: "Utilize o Kit de Gateway do comerciais IoT do Microsoft e a solução pré-configurada de monitorização remota. Utilize o gateway do limite de IoT do Azure para ativar um dispositivos SensorTag ligar a solução de monitorização remota, enviar telemetria para a nuvem e responder a métodos invocados a partir do dashboard de solução."
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
ms.openlocfilehash: 85b9ed0000c8c5ff2c7d6cc8fa4a051e0b27d6c2
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Ligar o gateway do limite de IoT do Azure para a solução pré-configurada de monitorização remota e enviar telemetria a partir de um SensorTag

[!INCLUDE [iot-suite-v1-gateway-kit-selector](../../includes/iot-suite-v1-gateway-kit-selector.md)]

Este tutorial mostra como utilizar o Azure IoT Edge para enviar dados relativos à temperatura e humidade de dispositivos SensorTag para a solução pré-configurada de monitorização remota. O SensorTag liga-se ao gateway Intel NUC utilizando o Bluetooth. O tutorial utiliza:

- Limite de IoT do Azure para implementar um gateway de exemplo.
- O IoT Suite remoto solução pré-configurada de monitorização como o back-end baseado na nuvem.

## <a name="overview"></a>Descrição geral

Neste tutorial, conclua os seguintes passos:

- Implemente uma instância da solução pré-configurada de monitorização remota à sua subscrição do Azure. Este passo automaticamente implementa e configura vários serviços do Azure.
- Configure o dispositivo de gateway Intel NUC para comunicar com o seu computador e a solução de monitorização remota.
- Configure o gateway de Intel NUC para receber telemetria a partir de um dispositivo SensorTag e enviá-lo para o dashboard de monitorização remota.

[!INCLUDE [iot-suite-v1-gateway-kit-prerequisites](../../includes/iot-suite-v1-gateway-kit-prerequisites.md)]

[Texas Instruments var SensorTag][lnk-sensortag]. Este tutorial obtém dados de telemetria sobre o Bluetooth do dispositivo SensorTag.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitorização remota aprovisiona um conjunto de serviços do Azure na sua subscrição do Azure. A implementação reflete uma arquitetura de empresas reais. Para evitar custos de consumo do Azure desnecessários, elimine a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado com o mesmo. Se precisar de novamente a solução pré-configurada, pode recriá-lo facilmente. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-gateway-kit-view-solution](../../includes/iot-suite-v1-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Configurar a conectividade do Bluetooth

Configure o Bluetooth o NUC Intel para ativar o dispositivo SensorTag ligar e enviar telemetria.

### <a name="find-the-mac-address-of-the-sensortag"></a>Localizar o endereço MAC do SensorTag

1. Na shell do NUC da Intel, execute o seguinte comando para desbloquear o serviço de Bluetooth:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Execute os seguintes comandos para iniciar o serviço de Bluetooth em NUC o Intel e introduza a shell de Bluetooth:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Execute o seguinte comando para power no controlador de Bluetooth:

    ```bash
    power on
    ```

    Quando o controlador está ativado, verá uma mensagem **a alteração de energia no foi concluída com êxito**.

1. Execute o seguinte comando para procurar dispositivos Bluetooth próximos em:

    ```bash
    scan on
    ```

1. Prima o botão de energia no SensorTag para torná-lo Detetáveis. O LED verde está intermitente.

1. Quando vir uma mensagem na shell do que o controlador tiver detetado o SensorTag, anote o endereço MAC do dispositivo. O endereço MAC é semelhante **A0:E6:F8:B5:F6:00**. Tem o endereço MAC mais tarde no tutorial quando configurar o gateway.

1. Execute o seguinte comando para desativar a análise de Bluetooth:

    ```bash
    scan off
    ```

1. Execute o seguinte comando para verificar se consegue ligar ao dispositivo SensorTag:

    ```bash
    connect <SensorTag MAC address>
    ```

    Se ligar com êxito, a shell mostra a mensagem **ligação com êxito** e imprime informações sobre o dispositivo SensorTag. Se não conseguir ligar, verifique o SensorTag ainda está ligado.

1. Pode agora desligar-se a SensorTag e sair da shell de Bluetooth executando os seguintes comandos:

    ```bash
    disconnect
    exit
    ```

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
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

O script de compilação coloca o módulo de limite de IoT personalizado libsensor2remotemonitoring.so na pasta de compilação.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurar e executar o gateway de IoT Edge

Agora, pode configurar o gateway de IoT Edge para enviar telemetria a partir do dispositivo SensorTag para o dashboard de monitorização remota. Para obter mais informações sobre como configurar um gateway e os módulos de limite de IoT, consulte [conceitos do Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> Neste tutorial, utiliza a norma `vi` editor de texto no NUC Intel. Se não tiver utilizado `vi` anteriormente, deve efetuar um tutorial introdutórias, tais como [Unix - o vi Editor Tutorial] [ lnk-vi-tutorial] para se familiarizar deste editor. Em alternativa, pode instalar mais intuitivo [nano](https://www.nano-editor.org/) editor utilizando o comando `smart install nano -y`.

Abra o ficheiro de configuração de exemplo no **vi** editor utilizando o seguinte comando:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
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
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Substitua o **macAddress** marcador de posição com o endereço MAC do seu SensorTag que apontou anteriormente. Substitua o **deviceID** e **deviceKey** marcadores de posição com os IDs e as chaves para os dois dispositivos na solução de monitorização remota que criou anteriormente.

Localize as seguintes linhas na configuração para o módulo de SensorTag:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Substitua o **dispositivo\_mac\_endereço** marcador de posição com o endereço MAC do seu SensorTag que apontou anteriormente.

Guarde as alterações.

Agora, pode executar o gateway com os seguintes comandos:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

O gateway de IoT é iniciado num NUC Intel e envia a telemetria do SensorTag à solução de monitorização remota:

![Gateway de IoT envia a telemetria do SensorTag][img-telemetry]

Prima **Ctrl-C** para sair do programa em qualquer altura.

## <a name="view-the-telemetry"></a>Ver a telemetria

O gateway está agora a enviar telemetria do dispositivo SensorTag à solução de monitorização remota. Pode ver a telemetria no dashboard da solução. Também pode enviar comandos para o seu dispositivo SensorTag através do gateway a partir do dashboard de solução.

- Navegue para o dashboard de solução.
- Selecione o dispositivo que configurou no gateway que representa o SensorTag no **dispositivo para a vista** pendente.
- Apresenta a telemetria do dispositivo SensorTag no dashboard.

![Apresentar telemetria dos dispositivos SensorTag][img-telemetry-display]

> [!WARNING]
> Se deixar a solução de monitorização remota em execução na sua conta do Azure, é-lhe faturado durante o período de tempo que é executada. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config]. Elimine a solução pré-configurada da sua conta do Azure quando tiver concluído a utilizá-la.


## <a name="next-steps"></a>Passos seguintes

Visite o [Dev Center do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e documentação no Azure IoT.

[img-telemetry]: ./media/iot-suite-v1-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-v1-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started