---
title: Simular o limite de IoT do Azure no Windows | Microsoft Docs
description: "Instalar o runtime do Azure IoT Edge num dispositivo simulado no Windows e implementar o seu módulo primeiro"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Implementar o Azure IoT Edge num dispositivo simulado Windows – pré-visualização

Limite de IoT do Azure move a capacidade da nuvem para os seus dispositivos Internet das coisas (IoT). Este tutorial explica como criar um dispositivo simulado de limite de IoT que gera dados de sensores. Saiba como:

![Arquitetura do tutorial][2]

O dispositivo simulado que criar neste tutorial é um monitor de um turbine vento que gera temperatura, humidade e pressão dados. Está interessado nestes dados porque os turbines efetuar de diferentes níveis de eficiência consoante as condições de Meteorologia. Os outros tutoriais de limite de IoT do Azure tirar partido de trabalho que fazer aqui implementando módulos analisam os dados de informações empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que está a utilizar um computador ou máquina virtual com o Windows para simular um dispositivo de Internet das coisas. 

>[!TIP]
>Se estiver a executar o Windows numa máquina virtual, ative [virtualização aninhada] [ lnk-nested] e atribua, pelo menos, 2 GB de memória. 

1. Certifique-se de que está a utilizar uma versão suportada do Windows:
   * Windows 10 
   * Windows Server
2. Instalar [Docker para Windows] [ lnk-docker] e certifique-se de que está a ser executado.
3. Instalar [Python 2.7 no Windows] [ lnk-python] e certifique-se de que pode utilizar o comando do pip.
4. Execute o seguinte comando para transferir o script de controlo contorno de IoT.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Limite de IoT do Azure pode executar contentores do Windows ou Linux contentores. Utilizar contentores do Windows, tem de executar:
>    * Atualizar do Windows 10 criadores de reversão, ou
>    * Windows Server 1709 (criar 16299), ou
>    * Windows IoT Core (criar 16299) num dispositivo baseado em x64
>
> Para Windows IoT Core, siga as instruções em [instalar o runtime de limite de IoT no Windows IoT Core][lnk-install-iotcore]. Caso contrário, basta [configurar Docker utilizar contentores Windows][lnk-docker-containers]e, opcionalmente, confirme os pré-requisitos com o seguinte comando do powershell:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Criar um hub IoT

O tutorial de início ao criar o seu IoT Hub.
![Criar o IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo de limite de IoT

Registe um dispositivo de limite de IoT IoT Hub recentemente criado.
![Registar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do limite de IoT

Instalar e iniciar o tempo de execução do Azure IoT Edge no seu dispositivo. 
![Registar um dispositivo][5]

O tempo de execução do limite de IoT é implementado em todos os dispositivos de limite de IoT. É composto por dois módulos. Em primeiro lugar, o agente de limite de IoT facilita a implementação e monitorização de módulos no dispositivo de limite de IoT. Segundo, o hub IoT Edge gere as comunicações entre os módulos no dispositivo de limite de IoT e entre o dispositivo e o IoT Hub. 


Utilize os seguintes passos para instalar e iniciar o tempo de execução do limite de IoT:

1. Configure o tempo de execução com a cadeia de ligação do dispositivo de limite de IoT da secção anterior.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Inicie o tempo de execução.

   ```
   iotedgectl start
   ```

1. Verifique o Docker para ver que o agente de limite de IoT está em execução como um módulo.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>Implementar um módulo

Gerir o seu dispositivo de limite de IoT do Azure na nuvem para implementar um módulo que irá enviar dados de telemetria ao IoT Hub.
![Registar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Dados de vista gerada

Este guia de introdução, criou um novo dispositivo de limite de IoT e instalado o tempo de execução do limite de IoT. Em seguida, utilizou o portal do Azure para emitir um módulo de limite de IoT para ser executada no dispositivo sem ter de efetuar alterações para o dispositivo propriamente dito. Neste caso, o módulo que tiver feito o Push de cria ambientais dados que pode utilizar para os tutoriais. 

Ver as mensagens a enviar do módulo tempSensor:

```cmd/sh
sudo docker logs -f tempSensor
```

Também pode ver a telemetria que o dispositivo está a enviar ao utilizar o [ferramenta do Explorador do IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um novo dispositivo de limite de IoT e utilizar a interface de nuvem do Azure IoT Edge para implementar o código no dispositivo. Agora, tem um dispositivo simulado, gerando dados não processados sobre o seu ambiente. 

Este tutorial é o pré-requisito para todos os outros tutoriais de limite de IoT. Pode continuar a sessão em qualquer um dos outros tutoriais para saber como limite de IoT do Azure o pode ajudar ative estes dados em informações empresariais no limite.

> [!div class="nextstepaction"]
> [Desenvolver e implementar código c# como um módulo](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md