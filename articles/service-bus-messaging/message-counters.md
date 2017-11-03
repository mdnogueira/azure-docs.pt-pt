---
title: Contagem de mensagens do Service Bus do Azure | Microsoft Docs
description: "Obter o número de mensagens do Service Bus do Azure."
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
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: f5d2aa551bbe77a66459907cf5cd1313bb907981
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="message-counters"></a>Contadores de mensagem

Pode obter a contagem de mensagens contidas nas filas e subscrições através da utilização do Azure Resource Manager e o Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs no .NET Framework SDK.

Com o PowerShell, pode obter a contagem da seguinte forma:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalhes de contagem de mensagens

Saber a contagem de mensagens de Active Directory é útil para determinar se uma fila cria um registo de segurança que requer mais recursos para processar a que atualmente implementado. Os seguintes detalhes do contador estão disponíveis no [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) classe:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): mensagens na fila ou a subscrição que se encontrem no ativo estado e pronto para entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): mensagens na fila entregues.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): mensagens no Estado agendada.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): mensagens falha de transferência para outra fila ou um tópico e terem sido movidas para a fila de entregues de transferência.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): mensagens pendentes transferência para outra fila ou tópico.

Se uma aplicação pretende dimensionar recursos com base no comprimento da fila, deve fazer com uma medida muito ritmo. A aquisição de contadores de mensagem é uma operação dispendiosa dentro do Mediador de mensagens e, em execução com frequência diretamente e negativamente tem impacto no desempenho de entidade.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)