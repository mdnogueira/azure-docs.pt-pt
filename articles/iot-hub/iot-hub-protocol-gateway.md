---
title: Gateway de protocolo do Azure IoT | Microsoft Docs
description: "Como utilizar um gateway de protocolo do Azure IoT para expandir o IoT Hub capacidades e o suporte de protocolos para permitir que os dispositivos para ligar ao seu hub utilizando protocolos não suportados nativamente pelo IoT Hub."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 1ed8ec28b95bbc91b731fd7bb7b3f1f6654e7fcf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="support-additional-protocols-for-iot-hub"></a>Suporte de protocolos adicionais para o IoT Hub
IoT Hub do Azure suporta nativamente comunicação através de protocolos MQTT, AMQP e HTTPS. Em alguns casos, dispositivos ou gateways de campo poderão não conseguir utilizar um destes protocolos padrão e exigir adaptation de protocolo. Nestes casos, pode utilizar um gateway personalizado. Um gateway personalizado permite adaptation de protocolo para pontos finais de IoT Hub pelo protocolo de bridge o tráfego de e para o IoT Hub. Pode utilizar o [gateway de protocolo do Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para ativar adaptation de protocolo para o IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Gateway de protocolo do IoT do Azure
O gateway de protocolo do IoT do Azure é uma estrutura para adaptation de protocolo que foi concebido para alta escala, comunicação do dispositivo bidirecional com o IoT Hub. O gateway de protocolo é um componente de pass-through que aceita ligações de dispositivos através de um protocolo específico. O tráfego para o IoT Hub-bridges através de AMQP 1.0. 

Pode implementar o gateway de protocolo no Azure de uma forma altamente dimensionável, utilizando o Azure Service Fabric, as funções de trabalho do Cloud Services do Azure ou máquinas virtuais do Windows. Além disso, o gateway de protocolo pode ser implementado em ambientes no local, tais como gateways de campo.

O gateway de protocolo do IoT do Azure inclui um adaptador de protocolo MQTT permite-lhe personalizar o comportamento de protocolo MQTT, se necessário. Uma vez que o IoT Hub fornece suporte incorporado para o protocolo do MQTT v 3.1.1, só deve considerar utilizar a placa de protocolo MQTT se são necessários as personalizações de protocolo ou requisitos específicos para funcionalidades adicionais.

O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação de gateway de protocolo do Azure IoT permite-lhe plug-in componentes personalizados para o processamento especializado, tais como autenticação personalizada, transformações de mensagem, compressão/descompressão ou encriptação/desencriptação do tráfego entre os dispositivos e o IoT Hub.

Flexibilidade, o gateway de protocolo do Azure IoT e a implementação de MQTT são fornecidos num projeto software open source. Pode utilizar o projeto open source para adicionar suporte para vários protocolos e as versões de protocolo, ou personalizar a implementação para o seu cenário. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o gateway de protocolo do IoT do Azure e como utilizar e implementá-la como parte da solução IoT, consulte:

* [Repositório de gateway do IoT protocolo do Azure no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia do Programador de gateway de protocolo do Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre o planeamento da implementação do IoT Hub, consulte:

* [Comparar com os Event Hubs][lnk-compare]
* [Dimensionamento, elevada disponibilidade e recuperação após desastre][lnk-scaling]
* [Guia para programadores do IoT Hub][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
