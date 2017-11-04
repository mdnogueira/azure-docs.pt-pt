---
title: "Mensagens de obtenção prévia do Service Bus do Azure | Microsoft Docs"
description: Melhore o desempenho ao prefetching mensagens do Service Bus do Azure.
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
ms.date: 10/04/2017
ms.author: sethm
ms.openlocfilehash: 4a4a06f90c2c48d35d836f0be89fec9cc47f32c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prefetch-azure-service-bus-messages"></a>Prefetch mensagens do Service Bus do Azure

Quando *obtenção prévia* está ativada em nenhum dos clientes oficiais do Service Bus, o recetor silenciosamente adquire mais mensagens, até o [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) limite, para além de que a aplicação inicialmente pedidas.

Um único inicial [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) ou [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) chamada, por conseguinte, adquire uma mensagem para o consumo de imediato que é devolvido como brevemente como disponível. O cliente adquire, em seguida, o mais mensagens de em segundo plano, para preencher a memória intermédia de obtenção prévia.

## <a name="enable-prefetch"></a>Ativar obtenção prévia

No .NET, é possível ativar a funcionalidade de obtenção prévia definindo a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) propriedade de um **MessageReceiver**, **QueueClient**, ou **SubscriptionClient**  para um número maior que zero. Definir o valor como zero activa desativar obtenção prévia.

Pode facilmente adicionar esta definição para o lado de receção do [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) ou [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) definições dos exemplos para ver o efeito nesses contextos.

Enquanto que as mensagens estão disponíveis na obtenção prévia memória intermédia, qualquer subsequente **Receive**/**ReceiveAsync** chamadas são imediatamente cumpridas da memória intermédia e a memória intermédia é replenished no em segundo plano como espaço fica disponível. Se não existem mensagens estão disponíveis para entrega, a operação de receção esvazia a memória intermédia e, em seguida, aguarda ou blocos, conforme esperado.

Obtenção prévia também funciona da mesma forma com o [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) APIs.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Se for mais rápida, por que motivo obtenção prévia não é a opção predefinida?

Obtenção prévia acelera o fluxo de mensagens, fazendo com que uma mensagem prontamente disponível para obtenção local quando e antes da aplicação pede-lhe um. Este ganhos de débito é o resultado de uma decisão de compromisso que o autor da aplicação tem de se explicitamente:

Com o [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete) receber modo, todas as mensagens que são adquiridas para a memória intermédia de obtenção prévia já não estão disponíveis na fila e apenas residem na memória intermédia de obtenção prévia na memória até serem recebidas para a aplicação através de **receber**/**ReceiveAsync** ou **OnMessage**/**OnMessageAsync** APIs. Se a aplicação concluir antes das mensagens são recebidas na aplicação, essas mensagens perdem irrevogavelmente.

No [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) receber modo, mensagens alcançadas para a memória intermédia de obtenção prévia são adquiridas para a memória intermédia no estado bloqueado e tem o relógio do tempo limite para o ticking de bloqueio. Se a memória intermédia de obtenção prévia é grande e processamento demora tempo essa mensagem bloqueios expirarem ao que reside na memória intermédia de obtenção prévia ou mesmo enquanto a aplicação está a processar a mensagem, poderão existir alguns eventos confusas para a aplicação processar.

A aplicação poderá adquirir uma mensagem com um bloqueio expiraram ou imminently expirar. Se assim for, a aplicação pode processar a mensagem, mas, em seguida, localize a que esta não é possível concluí-la devido à expiração do bloqueio. A aplicação pode verificar o [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) propriedade (que está sujeita a relógio dissimetrias entre o mediador e o relógio do computador local). Se o bloqueio de mensagem tiver expirado, a aplicação tem de ignorar a mensagem; não deve ser efetuada nenhuma chamada de API no ou com a mensagem. Se a mensagem não expirou, mas expiração imminent, o bloqueio de ser renovado e expandido por outro período de bloqueio predefinido ao chamar [mensagem. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Se o bloqueio expirar automaticamente na memória intermédia de obtenção prévia, a mensagem é tratada como abandonado e novamente é disponibilizada para obtenção da fila. Que pode fazer com que a ser alcançadas para a memória intermédia de obtenção prévia; colocados no fim. Se a memória intermédia de obtenção prévia não pode normalmente ser funcionada através de durante a expiração de mensagem, isto faz com que mensagens repetidamente prefetched mas nunca eficazmente entregue no Estado (válida bloqueado) utilizável e, eventualmente, são movidos para a fila de entregues uma vez a excedido o número máximo de entrega.

Se precisa de um nível elevado de fiabilidade para processamento de mensagens e processamento demora um trabalho considerável e a hora, recomenda-se que utilize a funcionalidade de obtenção prévia moderadamente ou não de todo.

Se precisa de alto ao longo e o processamento da mensagem é normalmente cheap, obtenção prévia gera vantagens significativas de débito.

A contagem de obtenção prévia máximo e a duração do bloqueio configurada na fila ou a subscrição tem de ser com balanceamento de forma a que o tempo limite de bloqueio, pelo menos, excede a cumulativa tempo para o tamanho máximo da memória intermédia de obtenção prévia de processamento de mensagens esperadas, além de uma mensagem. Ao mesmo tempo, o tempo limite de bloqueio aparece não deve ser muito, por isso, de que as mensagens podem exceder as respetivas máximo [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) quando acidentalmente serem largados, assim que necessitam de bloqueio de expirar antes de a ser reenviada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
