---
title: Compreender as mensagens do dispositivo para a nuvem do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - como utilizar mensagens dispositivo-nuvem com o IoT Hub. Inclui informações sobre o envio de dados de telemetria e não telemtry e utilizar o encaminhamento para entregar mensagens."
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
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: 4e346306ecb8f4897a249454c537ce9a1a4c4011
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Enviar mensagens do dispositivo para nuvem ao IoT Hub

Para enviar telemetria séries de tempo e os alertas dos seus dispositivos para a sua solução de back-end, envie mensagens do dispositivo para nuvem do seu dispositivo ao seu IoT hub. Para ver um debate de outras opções de dispositivo para nuvem suportada pelo IoT Hub, consulte [documentação de orientação do dispositivo para nuvem comunicações][lnk-d2c-guidance].

Enviar mensagens do dispositivo para a nuvem através de um ponto final orientado para o dispositivo (**/devices/ {"deviceId"} / mensagens/eventos**). Regras de encaminhamento e encaminhar as mensagens para um dos pontos finais orientado para o serviço no seu IoT hub. As regras de encaminhamento utilizam os cabeçalhos e corpo das mensagens dispositivo-nuvem fluir através do seu hub para determinar onde encaminhá-las. Por predefinição, as mensagens são encaminhadas para o ponto final orientado para o serviço incorporado (**mensagens/eventos**), que é compatível com [Event Hubs][lnk-event-hubs]. Por conseguinte, pode utilizar o padrão [integração com os Event Hubs e SDKs] [ lnk-compatible-endpoint] para receber mensagens dispositivo-nuvem na sua solução de back-end.

IoT Hub implementa utilizando um padrão de mensagens de transmissão em fluxo de mensagens do dispositivo para a nuvem. Mensagens de dispositivo para a nuvem do IoT Hub são mais como [Event Hubs] [ lnk-event-hubs] *eventos* que [Service Bus] [ lnk-servicebus] *mensagens* em que há um grande volume de eventos a transmitir através do serviço que pode ser lida por leitores múltiplos.

Mensagens com o IoT Hub dispositivo-nuvem tem as seguintes características:

* Mensagens do dispositivo para nuvem são retidos na predefinido de um hub IoT e durável **mensagens/eventos** ponto final para sete dias.
* Mensagens do dispositivo para nuvem podem ter um máximo de 256 KB e podem ser agrupadas em lotes para otimizar envia. Lotes podem ter, no máximo, 256 KB.
* Conforme explicado no [controlar o acesso ao IoT Hub] [ lnk-devguide-security] secção, o IoT Hub permite o controlo de acesso e autenticação por dispositivo.
* IoT Hub permite-lhe criar até 10 pontos finais de personalizado. São entregues as mensagens para os pontos finais com base nas rotas configuradas no seu IoT hub. Para obter mais informações, consulte [regras de encaminhamento](#routing-rules).
* IoT Hub permite milhões de dispositivos ligados em simultâneo (consulte [Quotas e limitação][lnk-quotas]).
* IoT Hub não permite a criação de partições arbitrários. Mensagens do dispositivo para nuvem-se partições com base no respetivo origem **deviceId**.

Para obter mais informações sobre as diferenças entre o IoT Hub e a serviços de Event Hubs, consulte [comparação do IoT Hub do Azure e Event Hubs do Azure][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Enviar o tráfego de telemetria não

Muitas vezes, para além de pontos de dados de telemetria, dispositivos as enviam mensagens e os pedidos que necessitam de execução separada e processamento na solução de back-end. Por exemplo, os alertas críticos que devem acionar uma ação específica no back-end. Pode escrever facilmente um [regra encaminhamento] [ lnk-devguide-custom] enviar estes tipos de mensagens para um ponto final dedicado ao respetivo processamento baseado em qualquer um cabeçalho na mensagem ou um valor no corpo da mensagem.

Para mais informações sobre a melhor forma de processar este tipo de mensagem, consulte o [Tutorial: como processar mensagens do dispositivo para a nuvem do IoT Hub] [ lnk-d2c-tutorial] tutorial.

## <a name="route-device-to-cloud-messages"></a>Encaminhar mensagens do dispositivo-nuvem

Tem duas opções para encaminhamento de mensagens dispositivo-nuvem às suas aplicações de back-end:

* Utilize o incorporado [endpoint compatível com o Event Hub] [ lnk-compatible-endpoint] para permitir que as aplicações de back-end ler as mensagens do dispositivo para nuvem recebidas pelo hub. Para saber mais sobre o ponto final compatível com o Event Hub incorporado, consulte [ler mensagens do dispositivo para nuvem a partir do ponto de final incorporado][lnk-devguide-builtin].
* Utilize as regras de encaminhamento para enviar mensagens para os pontos finais personalizados no seu IoT hub. Os pontos finais personalizados permitem as suas aplicações back-end para ler mensagens do dispositivo para a nuvem através dos Event Hubs, de filas do Service Bus ou tópicos do Service Bus. Para saber mais sobre os pontos finais de encaminhamento e personalizados, consulte [utilizar os pontos finais personalizados e regras de encaminhamento para mensagens do dispositivo para nuvem][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Propriedades de spoofing de antimalware

Para evitar o spoofing de nas mensagens do dispositivo para a nuvem, o IoT Hub do dispositivo carimbos todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm o **deviceId** e **generationId** do dispositivo de origem, como por [propriedades de identidade de dispositivo][lnk-device-properties].

O **ConnectionAuthMethod** propriedade contém um objeto JSON serializado, com as seguintes propriedades:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passos seguintes

Para informações sobre os SDKs que pode utilizar para enviar mensagens do dispositivo para a nuvem, consulte [SDKs IoT do Azure][lnk-sdks].

O [começar] [ lnk-get-started] tutoriais mostram-lhe como enviar mensagens do dispositivo para nuvem a partir de dispositivos simulados e físicos. Para obter mais detalhes, consulte o [mensagens de dispositivo para a nuvem do IoT Hub do processo utilizar rotas] [ lnk-d2c-tutorial] tutorial.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
