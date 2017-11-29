---
title: "Disponibilidade e a consistência no Event Hubs do Azure | Microsoft Docs"
description: "Como fornecer a quantidade máxima de disponibilidade e a consistência com partições de Event Hubs do Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: be1398e9b0a10efcd694e46d6322d5d7b9e7a843
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e a consistência no Event Hubs

## <a name="overview"></a>Descrição geral
Os Hubs de eventos do Azure utiliza um [modelo de criação de partições](event-hubs-features.md#partitions) para melhorar a disponibilidade e parallelization dentro de um hub de eventos único. Por exemplo, se um hub de eventos tem quatro partições e um dessas partições é movido de um servidor para outro numa operação de balanceamento de carga, podem ainda enviar e receber de três outras partições. Além disso, ter mais de partições permite-lhe ter leitores simultâneos mais processar os dados, melhorar o débito agregado. Compreender as implicações de criação de partições e ordenação num sistema distribuído é um aspeto fundamental de design de solução.

Para ajudar a explicar o compromisso entre a ordenação e disponibilidade, consulte o [theorem extremidade](https://en.wikipedia.org/wiki/CAP_theorem), também conhecido como theorem do Brewer. Este theorem aborda a escolha entre consistência, disponibilidade e a tolerância de partição.

Theorem do Brewer define consistência e a disponibilidade da seguinte forma:
* A tolerância de partição: a capacidade de um sistema de processamento de dados para continuar a processar os dados, mesmo se ocorrer uma falha de partição.
* Disponibilidade: um nó de falha não devolve uma resposta razoável dentro de um período de tempo (com sem erros ou tempos limite) razoável.
* Consistência: uma leitura é garantida para devolver a operação de escrita mais recente para um cliente especificado.

## <a name="partition-tolerance"></a>Tolerância de partição
Os Event Hubs é desenvolvida com um modelo de dados particionada. Pode configurar o número de partições do seu hub de eventos durante a configuração, mas não é possível alterar este valor mais tarde. Uma vez que tem de utilizar partições com os Event Hubs, terá de tomar uma decisão sobre a disponibilidade e consistência para a sua aplicação.

## <a name="availability"></a>Disponibilidade
A forma mais simples para começar a utilizar com os Event Hubs é utilizar o comportamento predefinido. Se criar um novo  **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)**  de objeto e utilizar o  **[enviar](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)**  método, os eventos são distribuídos automaticamente por entre partições do seu hub de eventos. Este comportamento permite que a maior quantidade de tempo.

Casos de utilização que requerem o máximo tempo, este modelo é preferencial.

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação de eventos pode ser importante. Por exemplo, poderá pretender que o sistema de back-end para processar um comando de atualização antes de um comando de eliminação. Neste caso, pode definir a chave de partição num evento, ou utilizar um `PartitionSender` objeto para apenas enviar eventos para uma determinada partição. Se o fizer, assegura que, quando estes eventos são lidos a partir da partição, estes são lidos por ordem.

Com esta configuração, tenha em atenção que, se a partição específica para o qual está a enviar não estiver disponível, irá receber uma resposta de erro. Como um ponto de comparação, se não tiver uma afinidade para uma única partição, o serviço de Event Hubs envia o evento para a partição disponível seguinte.

Uma solução possível para garantir a ordenação, enquanto também maximizando tempo, seria para agregar eventos como parte da sua aplicação de processamento de eventos. A forma mais fácil para realizar esta tarefa é o evento do carimbo com uma propriedade de número de sequência personalizado. O código seguinte mostra um exemplo:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

Este exemplo envia o evento para uma das partições de disponíveis no seu hub de eventos e define o número de sequência correspondente a partir da sua aplicação. Esta solução requer o estado para ser mantida pela sua aplicação de processamento, mas fornece os remetentes um ponto final que é mais provável estar disponível.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs serviço](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
