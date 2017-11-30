---
title: Compreender os pontos finais personalizados IoT Hub do Azure | Microsoft Docs
description: Guia para programadores - utilizando regras de encaminhamento para encaminhar mensagens do dispositivo para nuvem para os pontos finais personalizados.
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
ms.date: 11/29/2017
ms.author: dobett
ms.openlocfilehash: d1e22a4378caf69d2077d79f78682c4d438dbcd2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Utilizar rotas de mensagem e os pontos finais personalizados para mensagens do dispositivo-nuvem

IoT Hub permite-lhe encaminhar [mensagens do dispositivo para nuvem] [ lnk-device-to-cloud] para pontos finais de orientado para o serviço de IoT Hub com base nas propriedades da mensagem. As regras de encaminhamento dão-lhe a flexibilidade para enviar mensagens onde ir sem a necessidade de serviços adicionais para processar mensagens ou escrever código adicional que precisam. Cada regra de encaminhamento que configura tem as seguintes propriedades:

| Propriedade      | Descrição |
| ------------- | ----------- |
| **Nome**      | O nome exclusivo que identifica a regra. |
| **Origem**    | A origem do fluxo de dados para exigirão a tomada. Por exemplo, a telemetria do dispositivo. |
| **Condição** | A expressão de consulta para a regra de encaminhamento que está a executar os cabeçalhos e corpo da mensagem e utilizada para determinar se é uma correspondência para o ponto final. Para obter mais informações sobre a construção de uma condição de rota, consulte o [referência - linguagem de consulta para dispositivos duplos e tarefas][lnk-devguide-query-language]. |
| **Ponto final**  | O nome do ponto final onde o IoT Hub envia mensagens que correspondem à condição. Pontos finais devem estar na mesma região que o IoT hub, caso contrário, poderá cobrada por várias regiões escritas. |

Uma única mensagem pode correspondem à condição em várias regras de encaminhamento, na qual caso IoT Hub disponibiliza a mensagem para o ponto final associado a cada regra de correspondência. IoT Hub deduplicates também automaticamente a entrega de mensagens, pelo que o se corresponder a uma mensagem de várias regras que têm o mesmo destino, que é apenas escrito para esse destino uma vez.

Um IoT hub tem uma predefinição [ponto final incorporado][lnk-built-in]. Pode criar os pontos finais personalizados para encaminhar mensagens ao ligar a outros serviços na sua subscrição ao hub. IoT Hub suporta atualmente os contentores de armazenamento do Azure, Event Hubs, as filas do Service Bus e tópicos do Service Bus como pontos finais personalizados.

Quando utiliza pontos finais de encaminhamento e personalizados, entregues as mensagens são apenas para o ponto final incorporado se estes não correspondem às regras. Para entregar mensagens para o ponto final incorporado, bem como relativamente a um ponto de final personalizado, adicione uma rota que envia mensagens para o **eventos** ponto final.

> [!NOTE]
> IoT Hub apenas suporta a escrita de dados para contentores de armazenamento do Azure como blobs.

> [!WARNING]
> Filas do Service Bus e tópicos com **sessões** ou **duplicado deteção** ativado não são suportados como pontos finais personalizados.

Para obter mais informações sobre a criação de pontos finais personalizados no IoT Hub, consulte [pontos finais de IoT Hub][lnk-devguide-endpoints].

Para obter mais informações sobre a leitura do pontos finais personalizados, consulte:

* Ler a partir de [contentores de armazenamento do Azure][lnk-getstarted-storage].
* Ler a partir de [dos Event Hubs][lnk-getstarted-eh].
* Ler a partir de [filas do Service Bus][lnk-getstarted-queue].
* Ler a partir de [tópicos do Service Bus][lnk-getstarted-topic].

### <a name="next-steps"></a>Passos seguintes

Para mais informações sobre pontos finais de IoT Hub, consulte [pontos finais de IoT Hub][lnk-devguide-endpoints].

Para obter mais informações sobre o idioma de consulta que utiliza para definir regras de encaminhamento, consulte [idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens][lnk-devguide-query-language].

O [mensagens de dispositivo para a nuvem do IoT Hub do processo utilizar rotas] [ lnk-d2c-tutorial] tutorial mostra como utilizar as regras de encaminhamento e os pontos finais personalizados.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
