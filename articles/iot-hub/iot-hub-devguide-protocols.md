---
title: "Portas e protocolos de comunicação do IoT Hub do Azure | Microsoft Docs"
description: "Guia para programadores - descreve os protocolos de comunicação suportados para as comunicações de dispositivo para nuvem e da nuvem para o dispositivo e os números de porta tem de estar abertos."
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
ms.date: 08/31/2017
ms.author: dobett
ms.openlocfilehash: 37602bf78f7a43fb8255ddc0aad21f24095cb43c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reference---choose-a-communication-protocol"></a>Referenciar - escolher um protocolo de comunicação

IoT Hub permite que os dispositivos utilizar os protocolos seguintes para comunicações de lado do dispositivo:

* [MQTT][lnk-mqtt]
* MQTT através de WebSockets
* [AMQP][lnk-amqp]
* AMQP através de WebSockets
* HTTPS

Para obter informações sobre como é que estes protocolos suportam funcionalidades específicas do IoT Hub, consulte [documentação de orientação do dispositivo para nuvem comunicações] [ lnk-d2c-guidance] e [orientações de comunicações da nuvem para o dispositivo] [lnk-c2d-guidance].

A tabela seguinte fornece as recomendações de alto nível para à sua escolha de protocolo:

| Protocolo | Quando deve escolher este protocolo |
| --- | --- |
| MQTT <br> MQTT através de WebSocket |Utilizar em todos os dispositivos que não necessitam de ligar vários dispositivos (cada um com as suas próprias credenciais por dispositivo) através da ligação de TLS mesma. |
| AMQP <br> AMQP através de WebSocket |Utilize nos gateways de campo e da nuvem para tirar partido da ligação de multiplexação em todos os dispositivos. |
| HTTPS |Utilize para dispositivos que não é possível suportar outros protocolos. |

Quando escolher o protocolo para as comunicações do lado do dispositivo, considere os seguintes pontos:

* **Padrão de nuvem para o dispositivo**. HTTPS não tem uma forma eficaz de implementar o push de servidor. Como tal, quando estiver a utilizar o HTTPS, os dispositivos consultam o IoT Hub para mensagens da nuvem para o dispositivo. Esta abordagem é ineficaz para o dispositivo e o IoT Hub. Em diretrizes atuais de HTTPS, cada dispositivo deve consultar mensagens cada 25 minutos ou mais. MQTT e AMQP suportam push de servidor ao receber mensagens da nuvem para o dispositivo. Ativar a pushes imediatas de mensagens do IoT Hub para o dispositivo. Se a latência de entrega for uma preocupação, MQTT ou AMQP são os protocolos melhor a utilizar. Para dispositivos ligados raramente, HTTPS funciona bem.
* **Campo gateways**. Ao utilizar MQTT e HTTPS, não é possível ligar vários dispositivos (cada um com as suas próprias credenciais por dispositivo) utilizando a mesma ligação TLS. Para [cenários de gateway de campo] [ lnk-azure-gateway-guidance] que requerem uma ligação de TLS entre o gateway de campo e o IoT Hub para cada dispositivo ligado, estes protocolos são inferior ao ideal.
* **Dispositivos de recursos de baixa**. As bibliotecas MQTT e HTTPS tem um requisitos de espaço de menor do que as bibliotecas AMQP. Como tal, se o dispositivo limitou recursos (por exemplo, menor que 1 MB de RAM), estes protocolos poderão estar a implementação de protocolo apenas disponível.
* **Rede transversal**. O protocolo AMQP padrão utiliza a porta 5671 e MQTT escuta na porta 8883. Utilização destas portas pode causar problemas em redes que estão fechadas para protocolos de não-HTTPS. Utilizam MQTT sobre WebSockets, AMQP através de WebSockets ou HTTPS neste cenário.
* **Tamanho do payload**. MQTT e AMQP são protocolos binários, que resultam em mais compacta payloads de HTTPS.

> [!WARNING]
> Quando utilizar HTTPS, cada dispositivo deve consultar mensagens da nuvem para o dispositivo de cada 25 minutos ou mais. No entanto, durante o desenvolvimento, é aceitável para consultar mais frequentemente do que a cada 25 minutos.

## <a name="port-numbers"></a>Números de porta

Dispositivos podem comunicar com o IoT Hub no Azure utilizando vários protocolos. Normalmente, a escolha do protocolo está condicionada pelos requisitos específicos da solução. A tabela seguinte lista as portas de saída que tem de estar abertas para um dispositivo para poder utilizar um protocolo específico:

| Protocolo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT através de WebSockets |443 |
| AMQP |5671 |
| AMQP através de WebSockets |443 |
| HTTPS |443 |

Assim que tiver criado um IoT hub na região do Azure, o IoT hub mantém o mesmo endereço IP para a duração do que o IoT hub. No entanto, se Microsoft move o hub IoT para uma unidade de escala diferente para manter a qualidade de serviço, em seguida, este é atribuído um novo endereço IP.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como o IoT Hub implementa o protocolo MQTT, consulte o artigo [Communicate com o seu IoT hub, utilizando o protocolo MQTT][lnk-mqtt-support].

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
