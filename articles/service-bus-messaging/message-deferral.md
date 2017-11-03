---
title: Diferimento por mensagens do Service Bus do Azure | Microsoft Docs
description: Diferir entrega de mensagens do Service Bus
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
ms.date: 09/29/2017
ms.author: sethm
ms.openlocfilehash: f84b870de4b79399d5edc90284c9c56222156b5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="message-deferral"></a>Diferimento por da mensagem

Quando um cliente de fila ou a subscrição recebe uma mensagem que está disposto a processar, mas para que o processamento não está atualmente possível devido a circunstâncias especiais dentro da aplicação, tem a opção de "diferir" obtenção da mensagem para um ponto de posterior. A mensagem permanece na fila ou subscrição, mas está definido reservados.

Diferimento por é uma funcionalidade criada especificamente para cenários de processamento de fluxo de trabalho. Estruturas de fluxo de trabalho podem necessitar de determinadas operações para ser processado por uma ordem específica e poderão ter de adiar o processamento de algumas mensagens recebidas até previsto trabalho anterior que é informado por outras mensagens foi concluído.

Um exemplo simples ilustrativa para isto é uma ordem de processamento de sequência na qual pagamento de um fornecedor de pagamento externo é apresentada uma notificação num sistema antes da nota de encomenda correspondente foi propagada da frente arquivo para o sistema de cumprimento. Nesse caso, o sistema de cumprimento poderão diferir processar a notificação de pagamento enquanto houver uma ordem com o qual pretende associá-la. Em cenários de encontro, onde as mensagens de diferentes origens unidade reencaminhar um fluxo de trabalho, a ordem de execução em tempo real, de facto, poderá ser correta, mas as mensagens ao refletir os resultados podem chegarem fora de ordem.

Em última análise, diferimento por ajuda na ordenação novamente mensagens da ordem de chegada na ordem em que estes podem ser processados, mantendo essas mensagens com segurança no arquivo de mensagens para o quais necessidades de processamento ser adiada.

## <a name="message-deferral-apis"></a>Diferimento por APIs de mensagens

A API está [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente do .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente do .NET padrão, e **mesageReceiver.defer** ou **messageReceiver.deferSync** no cliente de Java. 

Mensagens diferidas permanecem na fila de principal, juntamente com todas as outras mensagens Active Directory (ao contrário de mensagens entregues que em direto numa fila secundárias), mas já não pode ser recebidos com as funções de receção/ReceiveAsync regulares. Mensagens diferidas podem ser detetadas através de [mensagem navegação](message-browsing.md) se perder a uma aplicação controlar deles.

Para obter uma mensagem diferida, o "proprietário" é responsável por tendo em conta o [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) conforme-defers-lo. Qualquer recetor que sabe o **SequenceNumber** de uma mensagem diferida mais tarde pode receber a mensagem explicitamente com Receive(sequenceNumber).

Se uma mensagem não pode ser processada porque um recurso específico para processar essa mensagem está temporariamente indisponível, mas o processamento da mensagem não deve ser suspensas summarily, uma forma elegante colocar essa mensagem no lado de alguns minutos é de memorizar o **SequenceNumber** num [mensagem agendada](message-sequencing.md) publicados dentro de alguns minutos e novamente obter a mensagem diferida quando chega a mensagem agendada. Tenha em atenção que se um processador de mensagens depende de uma base de dados para todas as operações e a base de dados está temporariamente indisponível, este deve não utilizar diferimento por, mas em vez disso suspender a receção de mensagens Folders até que a base de dados estiver novamente disponível.

Diferir o mensagens não afeta a expiração da mensagem, que significa que diferidas e as mensagens ainda expirarem na hora agendada inicialmente, em seguida, são movidas para a fila de entregues, se configurados para o fazer.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)