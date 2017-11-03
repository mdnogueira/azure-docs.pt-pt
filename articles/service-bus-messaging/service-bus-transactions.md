---
title: "Descrição geral da transação em processamento no Service Bus do Azure | Microsoft Docs"
description: "Descrição geral das transações atómicas do Service Bus do Azure e envio através de"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: dac0bf117e56c788adf46bc0647f684eccf8cf42
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="overview-of-service-bus-transaction-processing"></a>Descrição geral do processamento de transação do Service Bus
Este artigo aborda as capacidades de transação do Service Bus do Azure. Muitas do debate é ilustrada a [atómico transações com o exemplo de Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Este artigo é limitado a uma descrição geral do processamento de transações e o *enviar através de* funcionalidade do Service Bus, enquanto o exemplo de transações atómico é mais abrangente e mais complexas no âmbito.

## <a name="transactions-in-service-bus"></a>Transações no Service Bus
A [ *transação* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) grupos dois ou mais operações em conjunto para um *âmbito de execução*. Por natureza, essa uma transação tem de garantir que todas as operações que pertençam a um grupo especificado de operações de êxito ou falham jointly. No que esta respeita transações agirem como uma unidade, o que é frequentemente referida como *atomicity*. 

Barramento de serviço é um mediador de mensagens transacionais e assegura a integridade transacional para todas as operações internas contra os arquivos de mensagens. Todas as transferências de mensagens em fila no interior do Service Bus, tais como mover mensagens para um [entregues fila](service-bus-dead-letter-queues.md) ou [reencaminhamento automático](service-bus-auto-forwarding.md) de mensagens entre as entidades, são transacional. Como tal, se o Service Bus aceita uma mensagem, este já foi armazenado e etiqueta com um número de sequência. De ora em diante, quaisquer transferências de mensagem dentro do Service Bus são coordenadas operações em entidades e será nenhum levar a perda (for bem sucedida de origem e destino falha) ou a duplicação (falha de origem e destino é concluída com êxito) da mensagem.

Service Bus suporta operações de agrupamento em relação a uma entidade de mensagens única (fila, tópico, subscrição) dentro do âmbito de uma transação. Por exemplo, pode enviar mensagens de várias para uma fila de dentro de um âmbito de transação e as mensagens apenas serão confirmadas para registo da fila quando a transação concluir com êxito.

## <a name="operations-within-a-transaction-scope"></a>Operações dentro de um âmbito de transação
As operações que podem ser efetuadas dentro de um âmbito de transação serem da seguinte forma:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: enviar, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: concluir o procedimento, CompleteAsync abandono, AbandonAsync, mensagens não entregues, DeadletterAsync, diferir, DeferAsync, RenewLock, RenewLockAsync 

Receber operações não são incluídas, porque é suposto que a aplicação adquire mensagens utilizando o [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) ciclo de recepção de modo, dentro de alguns ou com um [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) chamada de retorno, e Só então é aberto um âmbito de transação para o processamento da mensagem.

Em seguida, ocorre a disposição da mensagem (completa, abandono, entregues, diferir) dentro do âmbito e dependentes na, o resultado da transação global.

## <a name="transfers-and-send-via"></a>Transferências e "enviar através de"
Para ativar handover transacional dos dados de uma fila para um processador e, em seguida, a outra fila, o Service Bus suporta *transferências*. Numa operação de transferência, um remetente começa por enviar uma mensagem para um *fila transferências*, e a fila de transferência imediatamente move-a mensagem para a fila de destino pretendido utilizando a implementação de transferência robusta mesmo que o reencaminhamento automática capacidade baseia-se no. A mensagem nunca é submetida para o registo da fila de transferência de uma forma que torna visível para os consumidores de fila de transferências.

A capacidade desta capacidade transacional torna-se aparente quando a fila de transferências em si é a origem de mensagens de entrada do remetente. Por outras palavras, Service Bus pode transferir a mensagem para a fila de destino "através de" fila de transferências, ao efetuar uma concluída (ou diferir, ou entregues) operação na mensagem de entrada, tudo numa única operação atómica. 

### <a name="see-it-in-code"></a>Vê-lo no código
Para configurar estas transferências, crie um remetente da mensagem que tenha como alvo a fila de destino através da fila de transferência. Também tem um recetor que obtém mensagens de nessa mesma fila. Por exemplo:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Uma transação simple, em seguida, utiliza estes elementos, como no exemplo seguinte:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Passos seguintes

Consulte os artigos seguintes para obter mais informações sobre as filas do Service Bus:

* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Encadeamento de entidades do Service Bus com o reencaminhamento automática](service-bus-auto-forwarding.md)
* [Exemplo de reencaminhamento automática](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Transações atómicas exemplo do Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [As filas do Azure e o Service Bus filas em comparação comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


