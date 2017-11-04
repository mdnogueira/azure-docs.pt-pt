---
title: "Compreender as quotas do IoT Hub do Azure e limitação | Microsoft Docs"
description: "Guia para programadores - descrição das quotas que se aplicam ao IoT Hub e o comportamento de limitação esperado."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: dobett
ms.openlocfilehash: 8ffe25f1950f8535983c2c344b5c4331b7157869
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referência - quotas do IoT Hub e a limitação

## <a name="quotas-and-throttling"></a>Quotas e limitação
Cada subscrição do Azure pode ter no máximo 10 os hubs IoT e, no máximo, 1 hub gratuito.

Cada IoT hub está aprovisionado com um determinado número de unidades de um SKU específico (para obter mais informações, consulte [preços do Azure IoT Hub][lnk-pricing]). O SKU e o número de unidades de determinam a quota máxima de diária de mensagens em fila que possa ser enviado.

O SKU também determina os limites de limitação do IoT Hub impõe em todas as operações.

## <a name="operation-throttles"></a>Limitações de operação
Limitações de operação são limitações de velocidade a que são aplicadas nos intervalos minutos e destinam-se para evitar abuso. IoT Hub tenta evitar a devolver erros sempre que possível, mas o mesmo inicia exceções a devolver se a limitação é violada há demasiado tempo.

A tabela seguinte mostra as limitações impostas. Consulte os valores para um hub individuais.

| Limitação | Livre e S1 hubs | Hubs de S2 | Hubs de S3 | 
| -------- | ------- | ------- | ------- |
| Operações de registo de identidade (criar, obter, listar, atualizar, eliminar) | 1.67/SEC/Unit (min/100/unidade) | 1.67/SEC/Unit (min/100/unidade) | 83.33/SEC/Unit (5000/mínimo/unidade) |
| Ligações do dispositivo | Mais de 100 por segundo ou seg/12/unidade <br/> Por exemplo, duas unidades de S1: 2\*12 = 24/seg, mas tem de ter, pelo menos, 100/seg nas suas unidades. Com unidades de S1 nove, tiver 108/seg (9\*12) entre as unidades. | 120/seg/unidade | 6000/seg/unidade |
| Envios do dispositivo para a cloud | Mais de 100 por segundo ou seg/12/unidade <br/> Por exemplo, duas unidades de S1: 2\*12 = 24/seg, mas tem de ter, pelo menos, 100/seg nas suas unidades. Com unidades de S1 nove, tiver 108/seg (9\*12) entre as unidades. | 120/seg/unidade | 6000/seg/unidade |
| Envios da cloud para o dispositivo | 1.67/SEC/Unit (min/100/unidade) | 1.67/SEC/Unit (min/100/unidade) | 83.33/SEC/Unit (5000/mínimo/unidade) |
| Receções da cloud para o dispositivo <br/> (apenas ao dispositivo utiliza HTTPS)| 16.67/SEC/Unit (1000/mínimo/unidade) | 16.67/SEC/Unit (1000/mínimo/unidade) | 833.33/SEC/Unit (50000/mínimo/unidade) |
| Carregamento de ficheiros | ficheiro 1.67 carregamento notificações/seg/unidade (min/100/unidade) | ficheiro 1.67 carregamento notificações/seg/unidade (min/100/unidade) | ficheiro 83.33 carregamento notificações/seg/unidade (5000/mínimo/unidade) |
| Métodos diretos | 20/seg/unidade | 60/seg/unidade | 3000/seg/unidade | 
| Leituras de dispositivo duplo | 10/seg | Mais de 10 por segundo ou seg/1/unidade | 50/seg/unidade |
| Atualizações de dispositivo duplo | 10/seg | Mais de 10 por segundo ou seg/1/unidade | 50/seg/unidade |
| Operações de tarefas <br/> (criar, atualizar, listar, eliminar) | 1.67/SEC/Unit (min/100/unidade) | 1.67/SEC/Unit (min/100/unidade) | 83.33/SEC/Unit (5000/mínimo/unidade) |
| Débito de operação tarefas por dispositivo | 10/seg | Mais de 10 por segundo ou seg/1/unidade | 50/seg/unidade |

É importante esclarecer que o *ligações de dispositivos* limitação regulam a velocidade a que seja possível estabelecer novas ligações de dispositivo com um IoT hub. O *ligações de dispositivos* limitação não governar o número máximo de dispositivos ligados em simultâneo. A limitação depende o número de unidades que sejam aprovisionados para o IoT hub.

Por exemplo, se comprar uma única unidade S1, receberá uma limitação de ligações de 100 por segundo. Por conseguinte, para estabelecer a ligação de 100 000 dispositivos, demora menos de 1000 segundos (cerca de 16 minutos). No entanto, pode ter como vários dispositivos ligados em simultâneo que tenham dispositivos registados no registo de identidade.

Para um debate aprofundado do IoT Hub limitação comportamento, consulte a mensagem de blogue [limitação do IoT Hub e][lnk-throttle-blog].

> [!NOTE]
> Em qualquer momento, é possível aumentar quotas ou limites de limitação de aumento do número de unidades aprovisionados num IoT hub.
> 
> [!IMPORTANT]
> Operações de registo de identidade destinam-se a utilização de tempo de execução em cenários de aprovisionamento e gestão de dispositivos. Ler ou atualizar um grande número de identidades de dispositivo é suportada através de [importar e exportar tarefas][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Outros limites

IoT Hub impõe outros limites operacionais:

| Operação | Limite |
| --------- | ----- |
| Os URIs de carregamento de ficheiros | 10000 SAS URIs podem ser enviados para uma conta do storage em simultâneo. <br/> 10 SAS URIs/dispositivo podem ficar fora de uma só vez. |
| Tarefas | Histórico da tarefa é retido cópias de segurança para 30 dias <br/> Tarefas simultâneas máximas é 1 (para gratuito) e S1, 5 (para S2), 10 (para S3). |
| Pontos finais adicionais | Paga SKU hubs podem ter pontos finais adicionais 10. Hubs SKU livres podem ter um ponto final adicional. |
| As regras de encaminhamento de mensagens | Paga SKU hubs podem ter 100 regras de encaminhamento. Hubs SKU livres podem ter regras de encaminhamento cinco. |
| Mensagens do dispositivo-nuvem | 256 KB de tamanho da mensagem máxima |
| Mensagens da nuvem para dispositivo | 64 KB de tamanho da mensagem máxima |
| Mensagens da nuvem para dispositivo | Máximo de mensagens para a entrega pendentes é 50 |

> [!NOTE]
> Atualmente, o número máximo de dispositivos que pode ligar a um IoT hub único é 500 000. Se pretende aumentar este limite, contacte [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Latência
IoT Hub strives fornecer a latência baixa para todas as operações. No entanto, devido a condições de rede e outros fatores imprevisíveis, não pode garantir uma latência máxima. Ao conceber a sua solução, deve:

* Evite efetuar qualquer pressupostos sobre a latência máxima de todas as operações de IoT Hub.
* Aprovisione o seu IoT hub na região do Azure mais próxima para os seus dispositivos.
* Considere a utilização do Azure IoT Edge para efetuar operações de sensíveis à latência no dispositivo ou num gateway perto do dispositivo.

Várias unidades de IoT Hub afetam a limitação, tal como descrito anteriormente, mas não fornece quaisquer benefícios de latência adicionais ou garantias.
Se vir inesperados aumentos de latência da operação, contacte [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes
Outros tópicos de referência neste guia para programadores do IoT Hub incluem:

* [Pontos finais de IoT Hub][lnk-devguide-endpoints]
* [Idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens][lnk-devguide-query]
* [Suporte de MQTT do IoT Hub][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
