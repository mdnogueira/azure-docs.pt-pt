---
title: As filas do Service Bus entregues | Microsoft Docs
description: "Descrição geral de filas do Service Bus do Azure entregues"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: e5070e225387f5d4ae9d49234b4e260a57436291
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Descrição geral de filas do Service Bus entregues

Filas do Service Bus e subscrições proporcionam uma fila secundárias secundária, chamada um *entregues fila* (DLQ). A fila de entregues não precisa de ser explicitamente criado e não pode ser eliminado ou geridos caso contrário, independentes da entidade principal.

Este artigo aborda entregues filas no Service Bus do Azure. Muitas do debate é ilustrada a [exemplo de filas entregues](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila de entregues

O objetivo da fila entregues é reter mensagens que não não possível entregar a qualquer recetor ou mensagens que não foi possível processar. Mensagens de, em seguida, podem ser removidas o DLQ e inspecioná-los. Uma aplicação poderá, com a ajuda de um operador, corrigir problemas e volte a submeter a mensagem, o facto de que ocorreu um erro de registo e tomar uma ação corretiva. 

Perspetiva de um protocolo e a API, o DLQ principalmente é semelhante a qualquer fila, exceto que só podem ser submetidas mensagens através do gesto entregues da entidade principal. Além disso, a time-to-live não é observados, e não é uma mensagem de um DLQ entregues. A fila de entregues suporta totalmente operações transacionais e entrega de bloqueio peek.

Tenha em atenção que não há nenhum limpeza automática do DLQ. Mensagens de permanecerem a DLQ até obter explicitamente da chamada e DLQ [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem entregues.

## <a name="moving-messages-to-the-dlq"></a>Mover as mensagens para o DLQ

Existem várias atividades no Service Bus que fazer com que mensagens para obter enviada para o DLQ de dentro do motor de mensagens em si. Uma aplicação explicitamente também pode mover as mensagens para o DLQ. 

Como a mensagem obtém movida pelo broker, duas propriedades são adicionadas à mensagem como o Mediador de chamadas a respetiva versão interno do [mensagens não entregues](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) método na mensagem: `DeadLetterReason` e `DeadLetterErrorDescription`.

As aplicações podem definir os seus próprios códigos para o `DeadLetterReason` propriedade, mas o sistema define os seguintes valores.

| Condição | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Sempre |HeaderSizeExceeded |A quota de tamanho para esta sequência foi excedida. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing e SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exceção. GetType(). Nome |exceção. Mensagem |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |A mensagem expirou e foi inutilizado lettered. |
| SubscriptionDescription.RequiresSession |Id de sessão é nulo. |Entidade de sessão ativado não permite uma mensagem cujo identificador de sessão é nulo. |
| ! entregues fila |MaxTransferHopCountExceeded |Valor nulo |
| Aplicação explícita lettering inutilizado |Especificado por aplicação |Especificado por aplicação |

## <a name="exceeding-maxdeliverycount"></a>Exceder MaxDeliveryCount
As filas e subscrições têm um [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) propriedade respetivamente; o valor predefinido é 10. Sempre que uma mensagem tem sido entregar sob um bloqueio ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), mas foi um explicitamente abandonado ou o bloqueio tiver expirado, a mensagem [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) é incrementado. Quando [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) excede [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), a mensagem é movida para o DLQ, especificando o `MaxDeliveryCountExceeded` pelo motivo código.

Não é possível desativar este comportamento, mas pode definir [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) para um número muito grande.

## <a name="exceeding-timetolive"></a>TimeToLive excedesse
Quando o [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) propriedade está definida como **true** (a predefinição é **falso**), todas as mensagens de expiração são movidas para DLQ, especificando o `TTLExpiredException` pelo motivo código.

Tenha em atenção que são apenas removidas e, por conseguinte, movidas para o DLQ quando existe, pelo menos, um recetor Active Directory extrair na fila principal ou de subscrição; as mensagens de expirada Se o comportamento é por predefinição.

## <a name="errors-while-processing-subscription-rules"></a>Erros ao processar as regras de subscrição
Quando o [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) propriedade está ativada para uma subscrição, quaisquer erros que ocorrem enquanto executa a regra de filtro do SQL Server de uma subscrição são capturados num DLQ ao longo com a mensagem inválida.

## <a name="application-level-dead-lettering"></a>Ao nível da aplicação mensagens não entregues
Para além das funcionalidades de mensagens não entregues fornecidos pelo sistema, as aplicações podem utilizar o DLQ explicitamente rejeitar inaceitável mensagens. Isto pode incluir as mensagens que não podem ser processadas corretamente devido a algum tipo de problema de sistema, mensagens que contêm payloads com formato incorreto ou mensagens que falham a autenticação quando é utilizada o esquema algumas segurança ao nível da mensagem.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mensagens não entregues nos cenários ForwardTo ou SendVia

As mensagens serão enviadas para a fila de entregues transferência sob as seguintes condições:

- Uma mensagem passa através de mais de 3 filas ou os tópicos [ligadas em cadeia](service-bus-auto-forwarding.md).
- A fila de destino ou um tópico, está desativado ou eliminado.
- A fila de destino ou um tópico excede o tamanho máximo de entidade.

Para obter estas mensagens lettered de mensagens não, pode criar um recetor utilizando o [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) método do utilitário.

## <a name="example"></a>Exemplo
O fragmento de código seguinte cria um recetor de mensagem. Num ciclo de receção para a fila principal, o código obtém a mensagem com [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), que lhe pede o Mediador de forma instantânea devolver qualquer mensagem prontamente disponível, ou para voltar com nenhum resultado. Se o código de receber uma mensagem,-lo imediatamente abandons-, que incrementos o `DeliveryCount`. Depois do sistema move a mensagem para o DLQ, a fila principal está vazia e sai do ciclo, como [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) devolve **nulo**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes para obter mais informações sobre as filas do Service Bus:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [As filas do Azure e o Service Bus filas em comparação comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

