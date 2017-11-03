---
title: "Introdução ao ligar a dispositivos físicos para o IoT Hub do Azure | Microsoft Docs"
description: "Saiba como ligar dispositivos físicos e quadros de ao IoT Hub do Azure. Os seus dispositivos podem enviar telemetria ao IoT Hub e do IoT Hub pode monitorizar e gerir os seus dispositivos."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: tutorial de hub iot do Azure
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: f4128b6b049aa876e170c56dcf2e40720644dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Introdução ao IoT Hub do Azure tutoriais de dispositivos físicos

Estes tutoriais apresentam-o para o IoT Hub do Azure e os SDKs do dispositivo. Os tutoriais abrangem os cenários comuns do IoT para demonstrar as capacidades do IoT Hub. Os tutoriais mostram também como combinar o IoT Hub com outros serviços do Azure e ferramentas para criar soluções de IoT mais poderosas. Os tutoriais listados na tabela a seguir mostram como criar dispositivos de IoT físicos.

| Dispositivo IoT                       | Linguagem de programação |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                      | [Arduino no VSCode][DevKit]     |
| Intel Edison                    | [NODE.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 coisa Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

Além disso, pode utilizar um gateway de IoT para permitir que os dispositivos ligar ao seu IoT hub.

| Dispositivo de gateway               | Linguagem de programação | Plataforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modelo DE3815TYKE) | C                    | [Vento River Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
