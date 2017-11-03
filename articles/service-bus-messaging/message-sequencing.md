---
title: "Sequência de mensagem de Service Bus do Azure e carimbos | Microsoft Docs"
description: "Preservar a sequência de mensagem do barramento de serviço e a ordem com carimbos"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: sethm
ms.openlocfilehash: e97bdd645ef2a3efba83e3f87114c998f9a9e344
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="message-sequencing-and-timestamps"></a>Sequência de mensagem e carimbos

Sequenciação e timestamping são duas funcionalidades que são sempre ativadas em todas as entidades do Service Bus e superfície através de [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) e [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) propriedades de recebida ou navegado mensagens.

Para os casos em que é significativo absoluto ordem de mensagens de e/ou em que um consumidor necessita de um identificador exclusivo fidedigno para mensagens, as mensagens de carimbos de Mediador com um intervalo-libertar, aumentar o número de sequência relativo para a fila ou tópico. Para entidades particionadas, o número de sequência é emitido relativo para a partição.

O **SequenceNumber** valor é um número inteiro 64-bit exclusivo atribuído a uma mensagem como é aceite e armazenada pela mediador e funções como o respetivo identificador interno. Para entidades particionadas, os mais superiores 16 bits refletir o identificador de partição. Números de sequência de rollover como zero quando o intervalo de 48/64 bits se esgota.

O número de sequência de mensagens em fila pode ser fidedigno como um identificador exclusivo, uma vez que é atribuído por uma autoridade independente e centralizada e não pelos clientes. Também representa a ordem de chegada verdadeira e é mais precisa, que um carimbo de hora como um critério de ordenação porque carimbos de data / hora não pode ter uma resolução suficientemente elevada às taxas de mensagens Alpine e poderão estar sujeitas a relógio (no entanto mínimo) dissimetrias em situações em que o Mediador transições de propriedade entre nós.

A ordem de chegada absoluto é importante, por exemplo, no negócio cenários em que um número limitado de oferecidas bens são servidos numa base servidos de primeiro provenientes primeiro ao fontes pela última vez; vendas de permissão concert são um exemplo.

A capacidade de tempo-carimbo de data / atua como uma autoridade independente e fidedigna que capture com precisão a hora UTC de chegada de uma mensagem, serão refletida no **EnqueuedTimeUtc** propriedade. O valor é útil se um cenário de negócio depende prazos, por exemplo, se um item de trabalho foi submetido numa determinada data antes de à meia-noite, mas o processamento é muito atrás o registo de segurança da fila.

## <a name="scheduled-messages"></a>Mensagens agendadas

Pode submeter mensagens para uma fila ou um tópico para o processamento atrasado; Por exemplo, para agendar uma tarefa fique disponível para o processamento por um sistema num determinado momento. Esta capacidade realiza um fiável distribuído baseados no tempo do programador.

Mensagens agendadas não materializar na fila até que o tempo de colocar em fila definido. Antes dessa hora agendadas mensagens podem ser canceladas. Cancelamento elimina a mensagem.

Pode agendar mensagens por definir a [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) propriedade ao enviar uma mensagem através do caminho de envio regular ou explicitamente com o [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. A última opção devolve imediatamente a mensagem agendada **SequenceNumber**, que pode utilizar mais tarde para cancelar a mensagem agendada, se necessário. Mensagens agendadas e os respetivos números de sequência podem também ser detetados com [mensagem navegação](message-browsing.md).

O **SequenceNumber** para uma mensagem agendada só é válida enquanto a mensagem está neste estado. Como transições de mensagem para o estado do Active Directory, a mensagem é acrescentada à fila, como se tivesse sido colocados em fila em instantâneas atual, o que inclui a atribuir um novo **SequenceNumber**.

Porque a funcionalidade está ancorada no mensagens individuais e as mensagens podem apenas ser colocados em fila uma vez, o barramento de serviço não suporta agendamentos periódicos para mensagens.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)