---
title: Ligar um Raspberry Pi para o Azure IoT Suite | Microsoft Docs
description: "Tutoriais utilizando Node.js ou C para o ajudar a saber como utilizar o Microsoft Azure IoT Starter Kit para o 3 do Raspberry Pi e a solução de monitorização remota do IoT Suite. Pode escolher um tutorial que simula telemetria, que utiliza sensores reais ou o que permite que as atualizações de firmware remoto."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d9e737fcaaabd2088d2920b69fca96d6058f4b4a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>Ligar o Microsoft Azure IoT Raspberry Pi 3 Starter Kit a solução de monitorização remota

Os tutoriais nesta secção ajudam a saber como ligar um dispositivo Raspberry Pi 3 a solução de monitorização remota. Escolha o tutorial adequado para a linguagem de programação preferencial e que o se tiver o hardware de sensor disponível para utilização com o Raspberry Pi.

## <a name="the-tutorials"></a>Os tutoriais

| Tutorial | Notas | Linguagens |
| -------- | ----- | --------- |
| Telemetria simulada (básico)| Simula dados de sensores. Utiliza uma autónoma Raspberry Pi. | [C][lnk-c-simulator], [Node.js][lnk-node-simulator] |
| Sensor de real (intermédio) | Utiliza os dados a partir de um sensor BME280 ligado ao seu Raspberry Pi. | [C][lnk-c-basic], [Node.js][lnk-node-basic] |
| Implementar a atualização de firmware (avançado)| Utiliza os dados a partir de um sensor BME280 ligado ao seu Raspberry Pi. Permite que as atualizações de firmware remoto na sua Raspberry Pi. | [C][lnk-c-advanced], [Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>Passos seguintes

Visite o [Dev Center do Azure IoT](https://azure.microsoft.com/develop/iot/) para obter mais exemplos e documentação no Azure IoT.

[lnk-node-simulator]: iot-suite-v1-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-v1-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-v1-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-v1-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-v1-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-v1-raspberry-pi-kit-c-get-started-advanced.md