---
title: Auto-reencaminhamento entidades de mensagens de Service Bus do Azure | Microsoft Docs
description: "Como encadear uma fila do Service Bus ou uma subscrição para outra fila ou tópico."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: sethm
ms.openlocfilehash: 6c92acee9d7609f4fedcddd40563b1a55fa08fac
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Encadeamento de entidades do Service Bus com o reencaminhamento automática

O Service Bus *automática reencaminhamento* funcionalidade permite-lhe estar ligados uma fila ou a subscrição para outra fila ou um tópico que faz parte do mesmo espaço de nomes. Quando o reencaminhamento automático está ativado, o Service Bus é automaticamente remove as mensagens que são colocadas em fila ou a subscrição (origem) primeiro e coloca-as na segunda fila ou tópico (destino). Tenha em atenção que é possível enviar uma mensagem para a entidade de destino diretamente. Além disso, não é possível encadear uma subfila, por exemplo, uma fila de mensagens não entregues, para outra fila ou tópico.

## <a name="using-auto-forwarding"></a>Utilizar o reencaminhamento automática
Pode ativar o reencaminhamento automática, definindo o [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] ou [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] as propriedades de [QueueDescription] [ QueueDescription] ou [SubscriptionDescription] [ SubscriptionDescription] objetos para a origem, como no exemplo seguinte.

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino tem de existir no momento que a entidade de origem é criada. Se a entidade de destino não existe, o Service Bus devolve uma exceção quando lhe for pedido para criar a entidade de origem.

Pode utilizar o reencaminhamento automático para aumentar horizontalmente um tópico individuais. Limites de barramento de serviço a [número de subscrições sobre um determinado tópico](service-bus-quotas.md) para 2000. Pode acomodar subscrições adicionais através da criação de tópicos de segundo nível. Tenha em atenção que, mesmo que não estão vinculados pela limitação do Service Bus no número de subscrições, adicionar um segundo nível de tópicos pode melhorar o débito global do seu tópico.

![Cenário de reencaminhamento automática][0]

Também pode utilizar o reencaminhamento de automática de desemparelhar os remetentes de mensagens de recetores. Por exemplo, considere um sistema ERP que consiste em três módulos: de ordem de processamento, a gestão de inventário e a gestão de relações do cliente. Cada um destes módulos gera mensagens que são colocados em fila para um tópico correspondente. Alice Bernardo, sendo representantes de vendas que estão interessadas em todas as mensagens que referem-se aos respetivos clientes. Para receber as mensagens, de Alice e Bernardo criar uma fila pessoal e uma subscrição em cada um dos tópicos ERP que reencaminhar automaticamente todas as mensagens da sua fila.

![Cenário de reencaminhamento automática][1]

Se a Alice passa férias, a fila pessoal em vez do tópico ERP, é preenchida. Neste cenário, porque um representante não recebeu quaisquer mensagens, nenhum dos tópicos ERP alguma vez atingir quota.

## <a name="auto-forwarding-considerations"></a>Considerações sobre o reencaminhamento automática

Se a entidade de destino acumula demasiadas mensagens e excede a quota, ou a entidade de destino está desativada, a entidade de origem adiciona as mensagens ao respetivo [entregues fila](service-bus-dead-letter-queues.md) até que o espaço de destino (ou entidade seja ativado novamente). Essas mensagens irão continuar a em direto da fila de entregues, pelo que tem de ser receber e processá-los a partir da fila entregues explicitamente.

Quando o encadeamento em conjunto individuais tópicos para obter um tópico composto com várias subscrições, recomenda-se que tem um número moderado de subscrições num tópico primeiro nível e várias subscrições de tópicos de segundo nível. Por exemplo, um tópico de primeiro nível com 20 subscrições, cada um deles ligados a um tópico de segundo nível com 200 subscrições, permite débito mais elevado do que um tópico de primeiro nível com 200 subscrições, cada um ligado a um tópico de segundo nível com 20 subscrições.

Uma operação para cada mensagem reencaminhada do Service Bus faturas. Por exemplo, enviar uma mensagem para um tópico com 20 subscrições, cada um deles configurado para mensagens de reencaminhamento automática para outra fila ou um tópico, é faturada como 21 operações se todas as subscrições de primeiro nível recebem uma cópia da mensagem.

Para criar uma subscrição que seja ligada a outra fila ou um tópico, o criador da subscrição tem de ter **gerir** permissões na origem e a entidade de destino. Apenas enviar mensagens para o tópico de origem requer **enviar** permissões sobre o tópico de origem.

## <a name="next-steps"></a>Passos seguintes

Para obter informações detalhadas sobre o reencaminhamento de automática, consulte os seguintes tópicos de referência:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Para obter mais informações sobre melhorias de desempenho do Service Bus, consulte 

* [Melhores práticas para melhorias de desempenho através de mensagens do Service Bus](service-bus-performance-improvements.md)
* [Entidades de mensagens particionadas][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
