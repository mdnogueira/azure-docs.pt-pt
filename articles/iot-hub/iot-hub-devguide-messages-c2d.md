---
title: Compreender as mensagens da nuvem para o dispositivo IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - como utilizar mensagens na nuvem para o dispositivo com o IoT Hub. Inclui informações sobre o ciclo de vida de mensagem e opções de configuração."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2017
ms.author: dobett
ms.openlocfilehash: a3ebda292d16b2a420fb6d586f18201e34efffa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Enviar mensagens de nuvem para o dispositivo a partir do IoT Hub

Para enviar notificações unidirecionais para a aplicação de dispositivos da sua solução de back-end, envie mensagens de nuvem para dispositivos do IoT hub para o seu dispositivo. Para ver um debate de outras opções de nuvem para dispositivos suportados pelo IoT Hub, consulte [orientações de comunicações da nuvem para o dispositivo][lnk-c2d-guidance].

Enviar mensagens da nuvem para o dispositivo através de um ponto final de serviço com acesso à (**/mensagens/devicebound**). Um dispositivo, em seguida, receber mensagens através de um ponto final específico do dispositivo (**/devices/ {"deviceId"} / mensagens/devicebound**).

Para cada mensagem da nuvem para o dispositivo num único dispositivo de destino, o IoT Hub define o **para** propriedade **/devices/ {"deviceId"} / mensagens/devicebound**.

Cada fila de dispositivo detém no máximo 50 mensagens da nuvem para o dispositivo. A tentar enviar mais mensagens para os mesmos resultados de dispositivo num erro.

## <a name="the-cloud-to-device-message-lifecycle"></a>O ciclo de vida da mensagem da nuvem para o dispositivo

Para garantir a entrega de mensagens em-menos-uma vez, o IoT Hub mantém mensagens da nuvem para dispositivo nas filas por dispositivo. Dispositivos explicitamente devem reconhecer *conclusão* para o IoT Hub para removê-los a partir da fila. Esta abordagem garante que a resiliência contra falhas de dispositivo e de conectividade.

O diagrama seguinte mostra o gráfico de estado do ciclo de vida de uma mensagem da nuvem para o dispositivo no IoT Hub.

![Ciclo de vida da mensagem da nuvem para o dispositivo][img-lifecycle]

Quando o serviço de IoT Hub envia uma mensagem para um dispositivo, o serviço define o estado de mensagem **colocados em fila**. Quando um dispositivo pretende *receber* uma mensagem, o IoT Hub *bloqueios* a mensagem (definindo o estado **invisível**), que permite que outros threads no dispositivo para começar a receber outras mensagens. Quando um thread de dispositivo concluir o processamento de uma mensagem, notifica o IoT Hub por *concluir* a mensagem. IoT Hub, em seguida, define o estado **concluído**.

Um dispositivo também pode optar por:

* *Rejeitar* mensagem, que faz com que o IoT Hub para o definir como o **Deadlettered** estado. Os dispositivos que ligam através do protocolo MQTT não podem rejeitar mensagens da nuvem para o dispositivo.
* *Abandonar* mensagem, que faz com que o IoT Hub colocar a mensagem na fila, com o estado definido como **colocados em fila**. Os dispositivos que ligam através do protocolo MQTT não é possível abandonar mensagens da nuvem para o dispositivo.

Um thread pode não conseguir processar uma mensagem sem que seja notificado IoT Hub. Neste caso, as mensagens são automaticamente transitar do **invisível** Estado volta para o **colocados em fila** Estado após um *tempo limite de visibilidade (ou bloqueio)*. O valor predefinido deste tempo limite é de um minuto.

O **máximo de contagem de entrega** propriedade no IoT Hub determina o número máximo de vezes que uma mensagem pode transitar entre o **colocados em fila** e **invisível** Estados. Após esse número de transições, o IoT Hub define o estado da mensagem para **Deadlettered**. Da mesma forma, o IoT Hub define o estado de uma mensagem para **Deadlettered** após a respetiva hora de expiração (consulte [TTL][lnk-ttl]).

O [como enviar mensagens da nuvem para o dispositivo com o IoT Hub] [ lnk-c2d-tutorial] mostra como enviar mensagens de nuvem para o dispositivo a partir da nuvem e recebê-las num dispositivo.

Normalmente, um dispositivo conclui uma mensagem da nuvem para o dispositivo quando a perda da mensagem não afeta a lógica de aplicação. Por exemplo, quando o dispositivo tem de localmente persistente a mensagem de conteúdo ou tem com êxito executar uma operação. A mensagem também pode executar informações transitórias, cuja perda não iria afetar a funcionalidade da aplicação. Por vezes, para tarefas de longa execução, pode:

* Conclua a mensagem da nuvem para o dispositivo após a persistência a descrição da tarefa no armazenamento local.
* Notificar o solução de back-end com uma ou mais mensagens dispositivo-nuvem em várias fases do progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração de mensagem (tempo TTL)

Cada mensagem da nuvem para o dispositivo tem um período de tempo de expiração. Este tempo é definido por uma das:

* O **ExpiryTimeUtc** propriedade no serviço.
* IoT Hub com a predefinição *TTL* especificada como uma propriedade do IoT Hub.

Consulte [opções de configuração da nuvem para o dispositivo][lnk-c2d-configuration].

Uma forma comum para tirar partido da expiração de mensagem e evitar o envio de mensagens para dispositivos desligados, consiste em definir curto período de tempo TTL valores. Esta abordagem alcance o mesmo resultado como manter o estado de ligação do dispositivo, enquanto a ser mais eficiente. Quando solicitar confirmações de mensagem, o IoT Hub notifica-o de que os dispositivos:

* É possível receber mensagens.
* Não estão online ou falharam.

## <a name="message-feedback"></a>Comentários da mensagem

Quando enviar uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega dos comentários per mensagem sobre o estado final dessa mensagem.

| Propriedade de confirmação | Comportamento |
| ------------ | -------- |
| **positivo** | Se a mensagem da nuvem para o dispositivo atingir o **concluído** Estado, o IoT Hub gera uma mensagem de comentários. |
| **negativo** | Se a mensagem da nuvem para o dispositivo atingir o **Deadlettered** Estado, o IoT Hub gera uma mensagem de comentários. |
| **completa**     | IoT Hub gera uma mensagem de comentários em ambos os casos. |

Se **Ack** é **completa**e não receberá uma mensagem de comentários, significa que a mensagem de comentários expirou. O serviço não é possível saber o que aconteceu à mensagem original. Na prática, um serviço deve certificar-se de que consegue processar os comentários antes de expirar. A hora de expiração máximo é dois dias, o que deixa a tempo ao obter o serviço de executar novamente se ocorrer uma falha.

Conforme explicado no [pontos finais][lnk-endpoints], o IoT Hub fornece comentários através de um ponto final de serviço com acesso à (**/messages/servicebound/feedback**) como mensagens. A semântica para receber comentários são iguais às mensagens da nuvem para o dispositivo e ter o mesmo [ciclo de vida de mensagem][lnk-lifecycle]. Sempre que possível, os comentários da mensagem estão em lotes numa única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Timestamp com a indicação de que a mensagem foi criada. |
| ID de utilizador       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma JSON serializado matriz de registos, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Timestamp que indica quando ocorreu o resultado da mensagem. Por exemplo, o dispositivo foi concluída ou a mensagem expirou. |
| originalMessageId  | **MessageId** da mensagem de nuvem para o dispositivo para que estas informações de feedback relacionada com. |
| statusCode         | Cadeia necessária. Utilizado em mensagens de comentários geradas pelo IoT Hub. <br/> 'Êxito' <br/> 'Expirou' <br/> 'DeliveryCountExceeded' <br/> 'Rejeitado' <br/> 'Removidos' |
| Descrição        | A cadeia de valores para **StatusCode**. |
| DeviceId           | **DeviceId** do dispositivo de destino da mensagem de nuvem para o dispositivo para o qual relacionado com esta peça de comentários. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da mensagem de nuvem para o dispositivo para o qual relacionado com esta peça de comentários. |

O serviço tem de especificar um **MessageId** para a mensagem da nuvem para o dispositivo poder correlacionar os seus comentários com a mensagem original.

O exemplo seguinte mostra o corpo de uma mensagem de comentários.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração da nuvem para o dispositivo

Cada IoT hub expõe as seguintes opções de configuração para as mensagens da nuvem para o dispositivo:

| Propriedade                  | Descrição | Intervalo e predefinido |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL predefinido para as mensagens da nuvem para o dispositivo. | Intervalo de ISO_8601 até 2D (no mínimo 1 minuto). Predefinição: 1 hora. |
| maxDeliveryCount          | Número máximo de entrega para filas de nuvem para o dispositivo por dispositivo. | 1 a 100. Predefinição: 10. |
| feedback.ttlAsIso8601     | Período de retenção para mensagens de comentários vinculado ao serviço. | Intervalo de ISO_8601 até 2D (no mínimo 1 minuto). Predefinição: 1 hora. |
| feedback.maxDeliveryCount |Número máximo de entrega de fila de comentários. | 1 a 100. Predefinição: 100. |

Para obter mais informações sobre como definir estas opções de configuração, consulte [os hubs IoT criar][lnk-portal].

## <a name="next-steps"></a>Passos seguintes

Para informações sobre os SDKs que pode utilizar para receber mensagens da nuvem para o dispositivo, consulte [SDKs IoT do Azure][lnk-sdks].

Experimentar a receber mensagens da nuvem para o dispositivo, consulte o [enviar nuvem para o dispositivo] [ lnk-c2d-tutorial] tutorial.

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
