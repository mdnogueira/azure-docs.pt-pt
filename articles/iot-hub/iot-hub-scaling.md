---
title: Dimensionamento IoT Hub do Azure | Microsoft Docs
description: "Como dimensionar o seu IoT hub para suportar o débito de mensagem previsto. Inclui um resumo das opções de fragmentação e o débito suportado para cada camada."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00043293eb57768f0117e912bb67f02d088934f3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="scale-your-iot-hub-solution"></a>Dimensionar a sua solução de hub IoT
IoT Hub do Azure pode suportar até um milhões de dispositivos ligados em simultâneo. Para obter mais informações, consulte [IoT Hub preços][lnk-pricing]. Cada unidade de IoT Hub permite que um determinado número de mensagens diárias.

Dimensionar corretamente a sua solução, considere a utilização do IoT Hub específica. Em particular, considere o débito de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a cloud
* Mensagens da nuvem para dispositivo
* Operações de registo de identidade

Para além desta informação de débito, consulte [quotas do IoT Hub e limitações] [ IoT Hub quotas and throttles] e estruturar a solução em conformidade.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Débito de mensagens do dispositivo para nuvem e da nuvem para o dispositivo
A melhor forma de tamanho de uma solução de IoT Hub consiste em avaliar o tráfego numa base por unidade.

Mensagens do dispositivo para nuvem siga estas diretrizes de débito constante.

| Escalão | Débito constante | Velocidade de envio constante |
| --- | --- | --- |
| S1 |Até 1111 KB por minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de mensagens por minuto 278 por unidade<br/>(um máximo de 400.000 mensagens diário por unidade) |
| S2 |Até 16 MB por minuto por unidade<br/>(22.8 GB/dia/unidade) |Média de mensagens por minuto 4,167 por unidade<br/>(milhões de 6 mensagens diário por unidade) |
| S3 |Até 814 MB por minuto por unidade<br/>(1144.4 GB/dia/unidade) |Média de mensagens por minuto 208,333 por unidade<br/>(milhões de 300 mensagens diário por unidade) |

## <a name="identity-registry-operation-throughput"></a>Débito de operação de registo de identidade
Operações de registo de identidade do IoT Hub são não deverá para ser o tempo de execução de operações, como estas são principalmente relacionadas com aprovisionamento de dispositivos.

Para números de desempenho de rajada específicos, consulte [quotas do IoT Hub e limitações][IoT Hub quotas and throttles].

## <a name="sharding"></a>Fragmentação
Embora um único IoT hub pode dimensionar para milhões de dispositivos, por vezes, a solução requer características de desempenho específica que não pode garantir que um único IoT hub. Nesse caso, é recomendado que partição os seus dispositivos para vários os hubs IoT. Vários aos hubs IoT uniforme bursts de tráfego e obter o débito necessário ou taxas de operação que são necessárias.

## <a name="next-steps"></a>Passos seguintes
Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
