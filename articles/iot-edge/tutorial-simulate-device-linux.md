---
title: Simular o limite de IoT do Azure no Linux | Microsoft Docs
description: "Instalar o runtime do Azure IoT Edge num dispositivo simulado com Linux e implementar a sua primeira módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Implementar o Azure IoT Edge num dispositivo simulado com Linux – pré-visualização

Limite de IoT do Azure permite-lhe efetuar o processamento de dados e de análise nos seus dispositivos, em vez de ter de push de todos os dados para a nuvem. Os tutoriais de limite de IoT demonstram como implementar os diferentes tipos de módulos, criados a partir de serviços do Azure ou código personalizado, mas primeiro tem de um dispositivo para testar. 

Este tutorial explica como criar um dispositivo simulado contorno de IoT, em seguida, implementar um módulo que gera dados de sensor. Saiba como:

![Arquitetura do tutorial][2]

O dispositivo simulado que criar neste tutorial é um monitor que gera temperatura, humidade e pressão dados. Os outros tutoriais de limite de IoT do Azure tirar partido de trabalho que fazer aqui implementando módulos analisam os dados de informações empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que está a utilizar um computador ou máquina virtual com Linux para simular um dispositivo de Internet das coisas. Os seguintes serviços são necessários para implementar com êxito um dispositivo de limite de IoT:

- [Instalar Docker para Linux] [ lnk-docker-ubuntu] e certifique-se de que está a ser executado. 
- A maioria das distribuições de Linux, incluindo Ubuntu, já tem o Python 2.7 instalado. Utilize o seguinte comando para se certificar de que o pip está instalado: `sudo apt-get install python-pip`.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

O tutorial de início ao criar o seu IoT Hub.
![Criar o IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo de limite de IoT

Registe um dispositivo de limite de IoT IoT Hub recentemente criado.
![Registar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do limite de IoT

Instalar e iniciar o tempo de execução do Azure IoT Edge no seu dispositivo. 
![Registar um dispositivo][5]

O tempo de execução do limite de IoT é implementado em todos os dispositivos de limite de IoT. É composto por dois módulos. Em primeiro lugar, o agente de limite de IoT facilita a implementação e monitorização de módulos no dispositivo de limite de IoT. Segundo, o hub IoT Edge gere as comunicações entre os módulos no dispositivo de limite de IoT e entre o dispositivo e o IoT Hub. 

Utilize os seguintes passos para instalar e iniciar o tempo de execução do limite de IoT:

1. No computador onde irá executar o dispositivo de limite de IoT, transferi o script de controlo contorno de IoT.

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. Configure o tempo de execução com a cadeia de ligação do dispositivo de limite de IoT da secção anterior.

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Inicie o tempo de execução.

   ```
   sudo iotedgectl start
   ```

1. Verifique o Docker para ver que o agente de limite de IoT está em execução como um módulo.

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>Implementar um módulo

Gerir o seu dispositivo de limite de IoT do Azure na nuvem para implementar um módulo que irá enviar dados de telemetria ao IoT Hub.
![Registar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Dados de vista gerada

Este guia de introdução, criou um novo dispositivo de limite de IoT e instalado o tempo de execução do limite de IoT. Em seguida, utilizou o portal do Azure para emitir um módulo de limite de IoT para ser executada no dispositivo sem ter de efetuar alterações para o dispositivo propriamente dito. Neste caso, o módulo que tiver feito o Push de cria ambientais dados que pode utilizar para os tutoriais. 

Ver as mensagens a enviar do módulo tempSensor:

```cmd/sh
docker logs -f tempSensor
```

Também pode ver a telemetria que o dispositivo está a enviar ao utilizar o [ferramenta do Explorador do IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um novo dispositivo de limite de IoT e utilizar a interface de nuvem do Azure IoT Edge para implementar o código no dispositivo. Agora, tem um dispositivo simulado, gerando dados não processados sobre o seu ambiente. 

Este tutorial é o pré-requisito para todos os outros tutoriais de limite de IoT. Pode continuar a sessão em qualquer um dos outros tutoriais para saber como limite de IoT do Azure o pode ajudar ative estes dados em informações empresariais no limite.

> [!div class="nextstepaction"]
> [Desenvolver e implementar código c# como um módulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
