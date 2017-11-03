---
title: "Expiração de mensagens do Service Bus do Azure | Microsoft Docs"
description: "Expiração e TTL de mensagens do Service Bus do Azure"
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
ms.openlocfilehash: 504010a39a4012b9a9edb60bb9a5b33ac20499c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="message-expiration-time-to-live"></a>Expiração de mensagem (TTL de)

O payload dentro de uma mensagem, ou um comando ou consulta que transmite uma mensagem para um recetor, quase sempre está sujeita a alguma forma de prazo de expiração de nível de aplicação. Após o prazo, o conteúdo já não é entregue ou já não executar a operação pedida.

Para desenvolvimento e ambientes de teste na qual as filas e tópicos, muitas vezes, são utilizados no contexto de execuções parciais de aplicações ou partes de aplicação, também é aconselhável teste stranded mensagens automaticamente libertados para que execute o seguinte teste pode Inicie limpa.

A expiração de quaisquer mensagens individuais pode ser controlada através da definição de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) propriedade de sistema, que especifica um período de tempo relativo. A expiração fica instante absoluto quando a mensagem é colocados em fila para a entidade. Nessa altura, o [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) propriedade demora no valor [ **EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Últimos o **ExpiresAtUtc** instantâneas, mensagens de se tornar elegível para obtenção. A expiração não afeta as mensagens que estão bloqueadas atualmente para entrega; essas mensagens ainda são processadas normalmente. Se o bloqueio expira ou a mensagem é abandonada, a expiração vigor imediata.

Enquanto a mensagem está em bloqueio, a aplicação pode estar na posse de uma mensagem que nominally expirou. Indica se a aplicação é disposta em avançar com processamento ou optar por abandonar a mensagem é até o implementador.

## <a name="entity-level-expiration"></a>Expiração de nível de entidade

Todas as mensagens enviadas para uma fila ou um tópico estão sujeitos a uma expiração de predefinição está definida na entidade nível com o [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) propriedade e que também pode ser definido no portal do durante a criação e tendo em conta mais tarde. A expiração de predefinido é utilizada para todas as mensagens enviadas para a entidade onde [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) não for explicitamente definida. A expiração de predefinição também funciona como um limite para o **TimeToLive** valor. As mensagens com uma maior **TimeToLive** expiração que o valor predefinido são automaticamente tendo em conta o **defaultMessageTimeToLive** valor antes de ser colocados em fila.

Mensagens expiradas, opcionalmente, podem ser movidas para um [entregues fila](service-bus-dead-letter-queues.md) definindo a [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) propriedade ou a verificar a respetiva caixa no portal do. Se a opção for deixada desativada, as mensagens de expiradas são removidas. Mensagens expiradas movidas para a fila de entregues podem ser distinguidas das outras mensagens lettered de mensagens não através da avaliação de [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) propriedade que armazena o mediador na secção de propriedades do utilizador; o valor é [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) neste caso.

No caso de acima mencionado no qual a mensagem está protegida da expiração em bloqueio e se o sinalizador está definido na entidade, a mensagem é movida para a fila de entregues como o bloqueio é abandonado ou expira. No entanto, não é movido se a mensagem é com êxito settled, que, em seguida, parte do princípio de que a aplicação com êxito processou, mantenha apesar da expiração nominal.

A combinação de [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) e automáticas (e transacional) mensagens não entregues no expiração são uma ferramenta valiosa para estabelecer a confiança no se uma tarefa para um processador ou um grupo de processadores de um prazo é obtida para processamento como o prazo for atingido.

Por exemplo, considere um web site que tem de forma fiável executar tarefas de um back-end restrita de escala e que ocasionalmente experiências tráfego picos ou pretende insulated contra episodes de disponibilidade desse back-end. No caso de regular, o processador do lado do servidor para os dados de utilizador submetido envia as informações para uma fila e, subsequentemente, recebe uma resposta confirmar o processamento da transação para uma fila de resposta com êxito. Se existir um pico de pedidos de tráfego e o processador de back-end não é possível processar os seus itens de registo de segurança no tempo, as tarefas expiradas são devolvidas na fila entregues. O utilizador interativo pode notificado de que a operação pedida irá demorar um pouco mais do que o habitual e o pedido pode, em seguida, ser colocado numa fila diferente para um caminho de processamento em que o resultado de uma eventual processamento é enviado ao utilizador por correio eletrónico. 

## <a name="temporary-entities"></a>Entidades temporárias

Filas do Service Bus, tópicos e subscrições podem ser criadas como entidades temporárias, que são automaticamente removidas quando estes não foram utilizados durante um período de tempo especificado.
 
A limpeza automática é útil em cenários de desenvolvimento e teste em que as entidades são criadas dinamicamente em não limpa após a utilização, devido ao teste ou de depuração execução ter sido interrompidos. Também é útil quando uma aplicação cria dinâmicas entidades, como uma fila de resposta, para a receção de respostas de volta para um processo de servidor web, ou para outro objeto relativamente curta duração em que é difícil de forma fiável limpar esses entidades quando o objeto instância desaparece.

A funcionalidade está ativada utilizando o [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) propriedade, o que está definida para a duração para que uma entidade tem de estar inativo (não utilizado) antes de ser eliminado automaticamente. A duração mínima é de 5 minutos.
 
A propriedade tem de ser definida através de uma operação do Gestor de recursos do Azure ou através do cliente do .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs. Não é possível definir através do portal.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)