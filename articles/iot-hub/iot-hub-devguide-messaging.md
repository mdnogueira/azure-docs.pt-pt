---
title: Compreender as mensagens do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - dispositivo-nuvem e nuvem para o dispositivo de mensagens com o IoT Hub. Inclui informações sobre os formatos de mensagem e os protocolos de comunicações suportados."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: dobett
ms.openlocfilehash: 4a7f545f1af2904f47808fe8b1d7d3aab3afff84
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Dispositivo para nuvem e da nuvem para o dispositivo de mensagens com o IoT Hub

Utilize o IoT Hub para comunicar com os seus dispositivos através de mensagens:

* Enviar [dispositivo para nuvem] [ lnk-d2c] mensagens dos seus dispositivos para a sua solução de back-end.
* Enviar [nuvem para o dispositivo] [ lnk-c2d] mensagens da solução de back-end para os seus dispositivos.

Propriedades de núcleos da funcionalidade de mensagens do IoT Hub são a fiabilidade e o durabilidade de mensagens em fila. Estas propriedades permitem maior resiliência às conectividade intermitente no lado do dispositivo e carregar picos de eventos de processamento do lado da nuvem. IoT Hub implementa *, pelo menos, uma vez* garantias de entrega de mensagens do dispositivo para a nuvem e nuvem para o dispositivo.

Para uma introdução às funcionalidades do IoT Hub, consulte os artigos [Azure e Internet das coisas] [ lnk-azure-iot] e [descrição geral do serviço do Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Quando utilizar o IoT Hub mensagens

Utilize mensagens do dispositivo para a nuvem para enviar telemetria séries de tempo e alertas da sua aplicação de dispositivo e as mensagens da nuvem para o dispositivo para unidirecionais notificações para a sua aplicação de dispositivo.

* Consulte [orientações de comunicação do dispositivo para nuvem] [ lnk-d2c-guidance] se em dúvida entre a utilização de mensagens do dispositivo para nuvem, propriedades comunicadas ou carregamento do ficheiro.
* Consulte [orientações de comunicação de nuvem para o dispositivo] [ lnk-c2d-guidance] se em dúvida entre a utilização de mensagens da nuvem para o dispositivo, propriedades pretendidas ou métodos diretos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o IoT Hub [mensagens dispositivo-nuvem][lnk-d2c].
* Saiba mais sobre o IoT Hub [mensagens da nuvem para dispositivo][lnk-c2d].

[lnk-azure-iot]: iot-hub-what-is-azure-iot.md
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md