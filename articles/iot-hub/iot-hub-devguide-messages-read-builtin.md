---
title: Compreender o ponto de final incorporado do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - descreve como utilizar as mensagens de dispositivo para nuvem toread de ponto final do incorporada, compatível com o Event Hub."
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
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: c9e6aa03e3a1e0592223630c7b81634bcb09add6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens do dispositivo para nuvem a partir do ponto de final incorporado

Por predefinição, as mensagens são encaminhadas para o ponto final orientado para o serviço incorporado (**mensagens/eventos**) que é compatível com [Event Hubs][lnk-event-hubs]. Este ponto final é apenas expostos a utilizar atualmente o [AMQP] [ lnk-amqp] protocolo na porta 5671. Um IoT hub expõe as propriedades seguintes que lhe permite controlar o ponto de final mensagens incorporado do compatível com o Event Hub **mensagens/eventos**.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem da partição** | Defina esta propriedade na criação de definir o número de [partições] [ lnk-event-hub-partitions] para ingestão de eventos de dispositivo para a nuvem. |
| **Período de retenção**  | Esta propriedade especifica o período de tempo em dias mensagens são mantidas pelo IoT Hub. A predefinição é um dia, mas pode ser aumentado para sete dias. |

IoT Hub também permite-lhe gerir os grupos de consumidores do dispositivo para nuvem incorporada recebem o ponto final.

Por predefinição, todas as mensagens que não correspondam a uma regra de encaminhamento de mensagens explicitamente são escritas para o ponto final incorporado. Se desativar esta rota de contingência, mensagens que não correspondem explicitamente quaisquer regras de encaminhamento de mensagens são ignoradas.

Pode modificar o período de retenção, quer através de programação através o [fornecedor de recursos do IoT Hub REST APIs][lnk-resource-provider-apis], ou utilizando o [portal do Azure][lnk-management-portal].

IoT Hub expõe o **mensagens/eventos** ponto final incorporado para os serviços de back-end ler as mensagens do dispositivo para nuvem recebidas pelo seu hub. Este ponto final é evento compatível com o Hub, que lhe permite utilizar qualquer um dos mecanismos de que o serviço de Event Hubs suporte para ler mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Ler a partir do ponto de final incorporado

Quando utiliza o [o SDK .NET do Azure Service Bus] [ lnk-servicebus-sdk] ou o [Hubs de eventos - evento processador anfitrião][lnk-eventprocessorhost], pode utilizar qualquer cadeias de ligação do IoT Hub com as permissões corretas. Em seguida, utilize **mensagens/eventos** como o nome de Hub de eventos.

Ao utilizar SDKs (ou integrações de produto) não tem conhecimento do IoT Hub, tem de obter um ponto de final compatível com o Event Hub e o nome compatível com o Event Hub das definições de hub IoT:

1. Iniciar sessão para o [portal do Azure] [ lnk-management-portal] e navegue até ao seu IoT hub.
1. Clique em **Pontos Finais**.
1. No **pontos finais incorporados** secção, clique em **eventos**. 
1. É aberta uma página de propriedades, que contém os seguintes valores: **endpoint compatível com o Event Hub**, **nome compatível com o Event Hub**, **partições**,  **Período de retenção**, e **grupos de consumidores**.

    ![Definições do dispositivo-nuvem][img-eventhubcompatible]

O SDK do Hub IoT requer o nome de ponto final de IoT Hub, que é **mensagens/eventos** conforme mostrado em **pontos finais**.

Se o SDK que está a utilizar requer um **Hostname** ou **espaço de nomes** valor, remova o esquema do **endpoint compatível com o Event Hub**. Por exemplo, se o ponto final compatível com o Event Hub **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, a **Hostname** seria  **iothub-ns myiothub 1234.servicebus.windows.net**. O **espaço de nomes** seria **iothub-ns myiothub 1234**.

Em seguida, pode utilizar qualquer política de acesso partilhado que tenha o **ServiceConnect** permissões para ligar para o Hub de eventos especificado.

Se precisar de criar uma cadeia de ligação do Hub de eventos, utilizando as informações anteriores, utilize o padrão do seguinte:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Os SDKs e integrações que pode utilizar com pontos finais de compatível com o Event Hub IoT Hub expõe inclui os itens na lista seguinte:

* [Cliente de Java Event Hubs](https://github.com/Azure/azure-event-hubs-java).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Pode ver o [spout origem](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre pontos finais de IoT Hub, consulte [pontos finais de IoT Hub][lnk-endpoints].

O [começar] [ lnk-get-started] os tutoriais mostram como enviar mensagens do dispositivo para nuvem a partir de dispositivos simulados e ler as mensagens a partir do ponto final incorporado. Para obter mais detalhes, consulte o [mensagens de dispositivo para a nuvem do IoT Hub do processo utilizar rotas] [ lnk-d2c-tutorial] tutorial.

Se pretender encaminhar as mensagens do dispositivo para a nuvem para os pontos finais personalizados, consulte [utilizar rotas de mensagem e os pontos finais personalizados para mensagens do dispositivo para nuvem][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
