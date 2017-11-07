---
title: "Ligar um Raspberry Pi para com o Node.js para suportar atualizações de firmware do Azure IoT Suite | Microsoft Docs"
description: "Utilize o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 e o Azure IoT Suite. Utilize Node.js para ligar o seu Raspberry Pi a solução de monitorização remota, enviar telemetria a partir de sensores para a nuvem e efetuar uma atualização de firmware remoto."
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
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Ligar a 3 de Pi Raspberry a solução de monitorização remota e ativar as atualizações de firmware remoto com o Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Este tutorial mostra como utilizar o Microsoft Azure IoT Starter Kit para Raspberry Pi 3 para:

* Desenvolva um leitor de temperatura e humidade que possa comunicar com a nuvem.
* Ativar e executar um firmware remoto atualização para atualizar a aplicação de cliente no Raspberry Pi.

O tutorial utiliza:

- SO de Raspbian, a linguagem de programação do Node.js e o Microsoft Azure IoT SDK para Node.js para implementar um dispositivo de exemplo.
- O IoT Suite remoto solução pré-configurada de monitorização como o back-end baseado na nuvem.

## <a name="overview"></a>Descrição geral

Neste tutorial, conclua os seguintes passos:

- Implemente uma instância da solução pré-configurada de monitorização remota à sua subscrição do Azure. Este passo automaticamente implementa e configura vários serviços do Azure.
- Configure os dispositivos e os sensores para comunicar com o seu computador e a solução de monitorização remota.
- Atualize o código de dispositivo de exemplo para ligar a solução de monitorização remota e enviar telemetria que pode ver no dashboard da solução.
- Utilize o código de dispositivo de exemplo para atualizar a aplicação de cliente.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A solução de monitorização remota aprovisiona um conjunto de serviços do Azure na sua subscrição do Azure. A implementação reflete uma arquitetura de empresas reais. Para evitar custos de consumo do Azure desnecessários, elimine a instância da solução pré-configurada em azureiotsuite.com quando tiver terminado com o mesmo. Se precisar de novamente a solução pré-configurada, pode recriá-lo facilmente. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Transferir e configurar o exemplo

Agora pode transferir e configurar a aplicação de cliente de monitorização remoto no seu Raspberry Pi.

### <a name="install-nodejs"></a>Instalar o Node.js

Se não tiver o feito, instale o Node.js no seu Raspberry Pi. O SDK de IoT para Node.js requer a versão 0.11.5 do Node.js ou posterior. Os passos seguintes mostram como instalar o Node.js v6.10.2 no seu Raspberry Pi:

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

Se não tiver o feito, clone repositórios do necessários executando os seguintes comandos no seu Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Atualizar a cadeia de ligação do dispositivo

Abra o ficheiro de configuração de exemplo no **nano** editor utilizando o seguinte comando:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Substitua os valores de marcador de posição com o id de dispositivo e informações de IoT Hub é criado e guardado no início deste tutorial.

Quando tiver terminado, o conteúdo do ficheiro deviceinfo deverá ter um aspeto semelhante ao seguinte exemplo:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Guardar as alterações (**Ctrl-O**, **Enter**) e saia do editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Executar o exemplo

Execute os seguintes comandos para instalar os pacotes de pré-requisitos para o exemplo:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Pode agora executar o programa de exemplo no Raspberry Pi. Introduza o comando:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

A saída de exemplo seguinte é um exemplo de saída, consulte a linha de comandos no Raspberry Pi:

![Resultado da aplicação Raspberry Pi][img-raspberry-output]

Prima **Ctrl-C** para sair do programa em qualquer altura.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. No dashboard de solução, clique em **dispositivos** para visitar o **dispositivos** página. Selecione o seu Raspberry Pi no **lista de dispositivos**. Em seguida, escolha **métodos**:

    ![Lista de dispositivos no dashboard][img-list-devices]

1. No **invocar método** página, escolha **InitiateFirmwareUpdate** no **método** pendente.

1. No **FWPackageURI** campo, introduza **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Este ficheiro contém a implementação da versão 2.0 do firmware.

1. Escolha **InvokeMethod**. A aplicação no Raspberry Pi envie uma confirmação novamente para o dashboard de solução. Em seguida, inicia o processo de atualização de firmware, transferindo-a nova versão do firmware:

    ![Mostrar histórico de método][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Observar o processo de atualização de firmware

Pode observar o firmware do processo de atualização é executada no dispositivo e visualizando as propriedades comunicadas no dashboard de solução:

1. Pode ver o progresso do processo de atualização no Raspberry Pi:

    ![Mostrar Progresso da atualização][img-update-progress]

    > [!NOTE]
    > A aplicação de monitorização remota reinicia automaticamente quando a atualização estiver concluída. Utilize o comando `ps -ef` para verificar se encontra em execução. Se pretende terminar o processo, utilize o `kill` comando com o id de processo.

1. Pode ver o estado da atualização de firmware, conforme comunicado pelo dispositivo, no portal de solução. A seguinte captura de ecrã mostra o estado e a duração de cada fase do processo de atualização e a nova versão de firmware:

    ![Mostrar estado da tarefa][img-job-status]

    Se navegar para o dashboard, pode verificar que o dispositivo ainda está a enviar telemetria a atualização de firmware a seguir.

> [!WARNING]
> Se deixar a solução de monitorização remota em execução na sua conta do Azure, é-lhe faturado durante o período de tempo que é executada. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config]. Elimine a solução pré-configurada da sua conta do Azure quando tiver concluído a utilizá-la.

## <a name="next-steps"></a>Passos seguintes

Visite o [Dev Center do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e documentação no Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
