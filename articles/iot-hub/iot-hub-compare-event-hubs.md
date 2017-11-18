---
title: Comparar o IoT Hub do Azure para Event Hubs do Azure | Microsoft Docs
description: "Uma comparação dos serviços de IoT Hub e do Azure Hubs de eventos, realçando diferenças funcionais e casos de utilização. A comparação inclui protocolos suportados, gestão de dispositivos, monitorização, e carrega o ficheiro."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b515e05d16dda83c7d865113d5d3578c44be084f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparação do IoT Hub do Azure e Event Hubs do Azure
Um dos casos de utilização principal para o IoT Hub é recolher telemetria dos dispositivos. Por este motivo, o IoT Hub, muitas vezes, é comparado ao [Event Hubs do Azure][Azure Event Hubs]. Como o IoT Hub, os Event Hubs são um serviço que permite a telemetria e eventos de entrada para a nuvem em grande escala, com baixa latência e alta fiabilidade de processamento de eventos.

No entanto, os serviços têm várias diferenças estão descritas na tabela seguinte:

| Área | IoT Hub | Event Hubs |
| --- | --- | --- |
| Padrões de comunicação | Permite [comunicações de dispositivo para nuvem] [ lnk-d2c-guidance] (mensagens de ficheiro carregamentos e propriedades comunicadas) e [comunicações de nuvem para o dispositivo] [ lnk-c2d-guidance] (diretos métodos, propriedades pretendidas, messaging). |Permite apenas a entrada de eventos (normalmente considerada para cenários de dispositivo para a nuvem). |
| Informações de estado do dispositivo | [Dispositivos duplos] [ lnk-twins] pode armazenar e consultar as informações de estado do dispositivo. | Não existem informações de estado do dispositivo podem ser armazenadas. |
| Suporte de protocolos de dispositivo |Suporta MQTT, MQTT através de WebSockets, AMQP, AMQP por WebSockets e HTTPS. Além disso, o IoT Hub funciona com o [gateway de protocolo do Azure IoT][lnk-azure-protocol-gateway], uma implementação do gateway de protocolo personalizável para suportar os protocolos personalizados. |Suporta AMQP, AMQP por WebSockets e HTTPS. |
| Segurança |Fornece identidade por dispositivo e controlo de acesso revocable. Consulte o [secção de segurança do Guia para programadores do IoT Hub]. |Fornece ao nível do Event Hubs [partilhado políticas de acesso][Event Hubs - security], com revogação limitada suportam através de [políticas do publicador][Event Hubs publisher policies]. Soluções de IoT, muitas vezes, são necessários para implementar uma solução personalizada para suportar as credenciais por dispositivos e medidas de spoofing de antimalware. |
| Monitorização de operações |Permite soluções de IoT subscrever um conjunto avançado de identidade gestão e a conectividade de eventos do dispositivo, como erros de autenticação de dispositivo individual, limitação e exceções de formato incorreto. Estes eventos permitem-lhe identificar rapidamente problemas de conectividade ao nível do dispositivo individual. |Expõe apenas agregadas métricas. |
| Escala |Está otimizada para suporte milhões de dispositivos ligados em simultâneo. |Meters as ligações como por [quotas de Event Hubs do Azure][Azure Event Hubs quotas]. Por outro lado, os Event Hubs permitem especificar a partição para cada mensagem enviada. |
| SDKs do dispositivo |Fornece [SDKs do dispositivo] [ Azure IoT SDKs] para uma grande variedade de plataformas e idiomas, além de direta MQTT, AMQP e APIs de HTTPS. |É suportado no .NET, Java e C, além da interfaces de envio AMQP e HTTPS. |
| Carregamento de ficheiros |Permite soluções de IoT carregar ficheiros a partir de dispositivos para a nuvem. Inclui um ponto final da notificação ficheiro para a integração de fluxo de trabalho e a uma categoria para suporte de depuração de monitorização de operações. | Não é suportado. |
| Mensagens de rota para vários pontos finais | Até 10 pontos finais de personalizadas são suportadas. As regras determinam como as mensagens são encaminhadas para os pontos finais personalizados. Para obter mais informações, consulte [enviar e receber mensagens com o IoT Hub][lnk-devguide-messaging]. | Necessita de códigos adicionais para ser escrito e alojados para emissão de mensagem. |

Em resumo, mesmo que esteja o caso de utilização única entrada de telemetria do dispositivo para a nuvem, o IoT Hub fornece um serviço que foi concebido para conectividade de dispositivos de IoT. Este continua a expandir as propostas de valor nestes cenários com funcionalidades específicas do IoT. Hubs de eventos foi concebida para entrada de evento em grande escala, ambos no contexto do Centro de dados inter- e intra-datacenter cenários.

Não é invulgar para utilizar o IoT Hub e Hubs de eventos na mesma solução. IoT Hub processa a comunicação do dispositivo para a nuvem e Event Hubs processa a entrada de evento de fase posterior para motores de processamento em tempo real.

### <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o planeamento da implementação do IoT Hub, consulte [dimensionamento, HA e DR][lnk-scaling].

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[secção de segurança do Guia para programadores do IoT Hub]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
