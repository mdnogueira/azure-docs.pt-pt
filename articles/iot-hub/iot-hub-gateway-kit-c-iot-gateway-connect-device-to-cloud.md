---
title: Utilizar um gateway de IoT para ligar um dispositivo ao IoT Hub do Azure | Microsoft Docs
description: Saiba como utilizar Intel NUC como um gateway de IoT para ligar um SensorTag de TI e enviar dados de sensores ao IoT Hub do Azure na nuvem.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: gateway de IOT ligar o dispositivo para a nuvem
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Utilizar o gateway de IoT para ligar coisas para a nuvem - SensorTag ao IoT Hub do Azure

> [!NOTE]
> Antes de começar este tutorial, certifique-se de que concluiu [configurar Intel NUC como um gateway de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). No [configurar Intel NUC como um gateway de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), configurou o dispositivo Intel NUC como um gateway de IoT.

## <a name="what-you-will-learn"></a>O que aprenderá

Irá aprender a utilizar um gateway de IoT para ligar um SensorTag de Instruments Texas (CC2650STK) ao IoT Hub do Azure. O gateway de IoT envia dados relativos à temperatura e humidade recolhidos do SensorTag ao IoT Hub do Azure.

## <a name="what-you-will-do"></a>O que irá fazer

- Crie um hub IoT.
- Registe um dispositivo no IoT hub para o SensorTag.
- Ative a ligação entre o gateway de IoT e o SensorTag.
- Execute uma aplicação de exemplo var para enviar dados de SensorTag ao seu IoT hub.

## <a name="what-you-need"></a>Do que precisa

- Tutorial [configurar Intel NUC como um gateway de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) concluída na qual configurou Intel NUC como um gateway de IoT.
- * Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
- Um cliente SSH que é executado no computador anfitrião. PuTTY recomenda-se no Windows. Linux e macOS já vêm com um cliente SSH.
- O endereço IP e o nome de utilizador e palavra-passe para o gateway de acesso do cliente SSH.
- Uma ligação à Internet.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Aqui, registar este novo dispositivo para a sua SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Ativar a ligação entre o gateway de IoT e o SensorTag

Nesta secção, pode realizar as seguintes tarefas:

- Obter o endereço MAC do SensorTag para ligação Bluetooth.
- Inicie uma ligação Bluetooth do gateway de IoT para o SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Obter o endereço MAC do SensorTag para ligação Bluetooth

1. No computador anfitrião, execute o cliente SSH e ligar ao gateway de IoT.
1. Desbloquear Bluetooth executando o seguinte comando:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Inicie o serviço de Bluetooth no gateway de IoT e introduza uma shell de Bluetooth para configurar o Bluetooth, executando os seguintes comandos:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Ligar o controlador de Bluetooth executando o seguinte comando na shell do Bluetooth:

   ```bash
   power on
   ```

   ![ligar o controlador de Bluetooth no gateway de IoT com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Inicie a análise de dispositivos próximos em Bluetooth executando o seguinte comando:

   ```bash
   scan on
   ```

   ![Analisar próximas dispositivos Bluetooth com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Clique no botão emparelhada no SensorTag. A verde GUIADO nos flashes SensorTag.
1. Na shell do Bluetooth, deverá ver que o SensorTag é encontrado. Anote o endereço MAC do SensorTag. Neste exemplo, é o endereço MAC do SensorTag `24:71:89:C0:7F:82`.
1. Desative a análise, executando o seguinte comando:

   ```bash
   scan off
   ```

   ![Parar a análise próximas dispositivos Bluetooth com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Iniciar uma ligação Bluetooth do gateway de IoT para o SensorTag

1. Liga para o SensorTag executando o seguinte comando:

   ```bash
   connect <MAC address>
   ```

   ![Ligar ao SensorTag com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Desligue o SensorTag e sair da shell de Bluetooth executando os seguintes comandos:

   ```bash
   disconnect
   exit
   ```

   ![Desligue o SensorTag com bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Ativou com êxito a ligação entre o SensorTag e o gateway de IoT.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Executar uma aplicação de exemplo var para enviar dados de SensorTag ao seu IoT hub

A aplicação de exemplo de Bluetooth baixa energia (var) é fornecida por limite de IoT do Azure. A aplicação de exemplo recolhe dados de ligação var e enviar os dados ao IoT hub. Para executar a aplicação de exemplo, tem de:

1. Configure a aplicação de exemplo.
1. Execute a aplicação de exemplo no gateway de IoT.

### <a name="configure-the-sample-application"></a>Configurar a aplicação de exemplo

1. Ir para a pasta da aplicação exemplo executando o seguinte comando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. Abra o ficheiro de configuração executando o seguinte comando:

   ```bash
   vi ble_gateway.json
   ```

1. No ficheiro de configuração, preencha os valores seguintes:

   **IoTHubName**: O nome do seu IoT hub.

   **IoTHubSuffix**: obter IoTHubSuffix a partir da chave primária da cadeia de ligação de dispositivo que anotou para baixo. Certifique-se de que obtém a chave primária da cadeia de ligação do dispositivo, não a chave primária de cadeia de ligação do IoT hub. A chave primária da cadeia de ligação de dispositivo está no formato de `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transporte**: O valor predefinido é `amqp`. Este valor é apresentado o protocolo durante transpotation. É possível `http`, `amqp`, ou `mqtt`.

   **macAddress**: endereço MAC o SensorTag que anotou para baixo.

   **deviceID**: ID do dispositivo que criou no seu IoT hub.

   **deviceKey**: A chave primária da cadeia de ligação de dispositivo.

   ![Concluir o ficheiro de configuração da aplicação de exemplo var](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Prima `ESC` e tipo `:wq` para guardar o ficheiro.

### <a name="run-the-sample-application"></a>Executar a aplicação de exemplo

1. Certifique-se que a SensorTag tem a alimentação ligada.
1. Execute o seguinte comando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Passos seguintes

[Utilize o gateway de IoT para transformação de dados de sensor com limite de IoT do Azure](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
