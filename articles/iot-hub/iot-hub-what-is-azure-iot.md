---
title: "Soluções Azure para Internet das Coisas (IoT Suite) | Microsoft Docs"
description: "Descrição geral de uma amostra da arquitetura de solução IoT e como se relaciona com dispositivos, o serviço do Hub IoT do Azure, SDKs do dispositivo Azure IoT, SDKs de serviço do Azure IoT e outros serviços do Azure."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 417ca4b6ecc39cbdafd8e12b5360b370d0ce79fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Passos seguintes

O Hub IoT do Azure é um serviço do Azure que permite comunicações bidirecionais seguras e fiáveis entre a sua solução de back-end e milhões de dispositivos. Permite à solução de back-end:

* Receber telemetria à escala dos seus dispositivos.
* Encaminhar dados dos seus dispositivos para um processador de eventos de transmissão.
* Receber carregamentos de ficheiros dos dispositivos.
* Enviar mensagens da cloud para o dispositivo para dispositivos específicos.

Pode utilizar o IoT Hub para implementar a sua própria solução de back-end. Além disso, o Hub IoT inclui um registo de identidade utilizado para aprovisionar dispositivos, as suas credenciais de segurança e os seus direitos de ligação ao Hub IoT. Para saber mais sobre o Hub IoT, veja [What is IoT Hub (O que é o Hub IoT)][lnk-iot-hub].

Para saber como o Hub IoT do Azure permite a gestão de dispositivos baseada em normas para que possa gerir remotamente os seus dispositivos, veja [Overview of device management with IoT Hub (Descrição geral da gestão de dispositivos com o Hub IoT)][lnk-device-management].

Para implementar aplicações de cliente em várias plataformas de hardware e sistemas operativos para dispositivos, pode utilizar os Azure IoT Device SDKs. Os SDKs do dispositivo incluem bibliotecas que facilitam o envio de telemetria para um Hub IoT e a receção de mensagens da cloud para o dispositivo. Quando utiliza os SDKs do dispositivo, pode escolher entre vários protocolos de rede para comunicar com o Hub IoT. Para saber mais, veja as [informações sobre os Device SDKs][lnk-device-sdks].

Para começar a escrever código e executar alguns exemplos, veja o tutorial [Introdução ao Hub IoT][lnk-getstarted].

Também poderá estar interessado na [Azure IoT Suite][lnk-iot-suite], que é uma coleção de soluções pré-configuradas. A IoT Suite oferece-lhe uma introdução rápida e um dimensionamento de projetos IoT para abordar cenários comuns do IoT - tais como a monitorização remota, a gestão de elementos e a manutenção preditiva.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md
