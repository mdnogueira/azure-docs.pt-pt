---
title: Gerir o dispositivo de nuvem do IoT Hub do Azure mensagens com o Explorador de iothub | Microsoft Docs
description: Saiba como utilizar a ferramenta CLI do Explorador do iothub monitor dispositivo para nuvem mensagens (D2C) e enviar nuvem para mensagens do dispositivo (C2D) no IoT Hub do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: Explorador de iothub, nuvem dispositivo mensagens na nuvem do hub iot para o dispositivo, nuvem para mensagens do dispositivo
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 30151b7bdc544bc36e959cc3528d37897198fc7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utilize o Explorador de iothub para enviar e receber mensagens entre o dispositivo e o IoT Hub

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Explorador de iothub](https://github.com/azure/iothub-explorer) tem alguns dos comandos que facilita a gestão do IoT Hub. Este tutorial centra-se sobre como utilizar o Explorador do iothub para enviar e receber mensagens entre o dispositivo e o seu IoT hub.

## <a name="what-you-will-learn"></a>O que aprenderá

Saiba como utilizar o Explorador do iothub para monitorizar as mensagens do dispositivo para nuvem e para enviar mensagens da nuvem para o dispositivo. Mensagens do dispositivo para nuvem podem ser dados de sensores que o dispositivo recolhe e, em seguida, envia ao seu IoT hub. Mensagens da nuvem para o dispositivo pode ser comandos que o seu hub IoT envia para o seu dispositivo para blink um LED que está ligada ao seu dispositivo.

## <a name="what-you-will-do"></a>O que irá fazer

- Utilize o Explorador do iothub para monitorizar as mensagens do dispositivo para a nuvem.
- Utilize o Explorador do iothub para enviar mensagens da nuvem para o dispositivo.

## <a name="what-you-need"></a>Do que precisa

- Tutorial [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluir que inclui os seguintes requisitos:
  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure na sua subscrição.
  - Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.
- Explorador de iothub. ([Instalar iothub explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Monitorizar as mensagens do dispositivo-nuvem

Para monitorizar as mensagens que são enviadas do seu dispositivo ao seu IoT hub, siga estes passos:

1. Abra uma janela da consola.
1. Execute o seguinte comando:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Obter `<device-id>` e `<IoTHubConnectionString>` do seu IoT hub. Certifique-se de que concluiu o tutorial anterior. Ou pode tentar utilizar `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` se tiver `HostName`, `SharedAccessKeyName` e `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem a partir do seu IoT hub para o seu dispositivo, siga estes passos:

1. Abra uma janela da consola.
1. Inicie uma sessão no seu IoT hub, executando o seguinte comando:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Envie uma mensagem para o seu dispositivo, executando o seguinte comando:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

O comando fica intermitente LED que está ligada ao seu dispositivo e envia a mensagem para o seu dispositivo.

> [!Note]
> Não é necessário para o dispositivo enviar um comando de confirmação separado volta ao seu IoT hub ao receber a mensagem.

## <a name="next-steps"></a>Passos seguintes

Aprendeu como monitorizar as mensagens do dispositivo para nuvem e enviar mensagens da nuvem para o dispositivo entre os dispositivos de IoT e o IoT Hub do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
