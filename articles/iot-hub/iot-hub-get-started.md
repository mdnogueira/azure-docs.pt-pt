---
title: "IoT Hub do Azure - introdução ao ligar a dispositivos de IoT à nuvem | Microsoft Docs"
description: Saiba como ligar o seu IoT quadros e kits de arranque ao IoT Hub do Azure. Os seus dispositivos podem enviar telemetria ao IoT Hub e do IoT Hub pode monitorizar e gerir os seus dispositivos.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: tutorial de hub iot do Azure
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: 764c6bd3c2466ceb93e33d03bece1c9036c1b1d9
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Azure IoT Hub introdução tutoriais

Pode utilizar o IoT Hub do Azure e os SDKs do dispositivo IoT do Azure para criar soluções de Internet das coisas (IoT):

* IoT Hub do Azure é um serviço completamente gerido na nuvem que estabelece ligação com segurança, monitoriza e gere os dispositivos de IoT. Utilize os SDKs de dispositivos de IoT do Azure para implementar os seus dispositivos de IoT.
* Utilize um gateway de IoT cenários mais complexos de IoT. Por exemplo, onde tem de considerar os fatores, tais como dispositivos legados, os custos de largura de banda, políticas de segurança e privacidade ou processamento de dados de limite. Nestes cenários, utilizar o limite de IoT do Azure para implementar um gateway que liga dispositivos ao seu IoT hub.

## <a name="what-the-tutorials-cover"></a>O que abrangem os tutoriais

Estes tutoriais apresentam-o para o IoT Hub do Azure e os SDKs do dispositivo. Os tutoriais abrangem os cenários comuns do IoT para demonstrar as capacidades do IoT Hub. Os tutoriais mostram também como combinar o IoT Hub com outros serviços do Azure e ferramentas para criar soluções de IoT mais poderosas. Nos tutoriais, pode optar por utilizar dispositivos de IoT simulados ou reais. Além disso, pode saber como utilizar um gateway para permitir que os dispositivos ligar ao seu IoT hub.

## <a name="set-up-your-device"></a>Configurar o dispositivo

Ligar um dispositivo IoT ou gateway ao IoT Hub do Azure. Pode escolher um dispositivo físico ou simulado para começar a utilizar:

| Dispositivo IoT                       | Linguagem de programação |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino no VSCode][DevKit]     |
| Intel Edison                     | [NODE.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 coisa Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Dispositivo simulado no PC           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
| Simulador de dispositivo online         | [Raspberry Pi (Node.js)][Ol_Sim] |

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
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
