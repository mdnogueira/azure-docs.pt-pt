---
title: "Transferências de mensagem, bloqueios e settlement Service Bus do Azure | Microsoft Docs"
description: "Descrição geral das transferências de mensagem do barramento de serviço e operações settlement"
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
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: edb6e207852fa59d5828906c891693f367739c9c
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="message-transfers-locks-and-settlement"></a>Transferências de mensagem, bloqueios e settlement

A capacidade central mais de um mediador de mensagens, tais como o Service Bus é aceite mensagens para um tópico ou uma fila e mantenha premido-los disponíveis para obtenção posterior. *Enviar* é o termo que é geralmente utilizado para a transferência de uma mensagem para o Mediador de mensagens. *Receber* é o termo normalmente utilizado para a transferência de uma mensagem para um cliente ao obter.

Quando um cliente envia uma mensagem, normalmente, pretende saber se a mensagem foi corretamente transferida para e aceite pelo broker ou se Ocorreu algum tipo de erro. Esta confirmação positivo ou negativa assenta o cliente e o Mediador de compreender sobre o estado de transferência da mensagem e, por conseguinte, é referida como *settlement*.

Da mesma forma, quando o mediador transfere uma mensagem para um cliente, o mediador e o cliente pretendem estabelecer uma compreensão dos se a mensagem foi processada com êxito e, por conseguinte, pode ser removida ou se a entrega de mensagens ou processamento falhou e, consequentemente, o mensagem poderá ter de ser entregue novamente.

## <a name="settling-send-operations"></a>Settling operações de envio

Utilizar qualquer um dos clientes de API do Service Bus suportados, enviar operações para o Service Bus sempre explicitamente são settled, o que significa que a operação de API aguarda que um resultado de aceitação de Service Bus chegam e, em seguida, concluir a operação de envio.

Se a mensagem será rejeitada pelo Service Bus, a rejeição contém um indicador de erro e o texto com um "-id de controlo" dentro do mesmo. A rejeição também inclui informações sobre se pode repetir a operação com as expectativas de sucesso. No cliente, esta informação é transformada uma exceção e gerada para o autor da chamada da operação de envio. Se a mensagem foi aceite, a operação for concluída silenciosamente.

Ao utilizar o protocolo AMQP, que é o protocolo exclusivo para o cliente do .NET padrão e o cliente de Java e [que é uma opção para o cliente do .NET Framework](service-bus-amqp-dotnet.md), transferências de mensagem e settlements são encaminhados e completamente assíncrona, e é recomendado que utilize os variantes de API de modelo programação assíncronas.

Um remetente pode colocar várias mensagens na rede rápida sucessivamente sem ter de aguardar para cada mensagem a ser confirmada, como de outra forma seriam as maiúsculas e minúsculas com o protocolo SBMP ou com HTTP 1.1. Essas operações de envio assíncrono concluir conforme as respetivas mensagens são aceites e armazenadas no entidades particionadas ou quando a operação de envio para a sobreposição de entidades diferentes. As conclusões também poderão ocorrer fora de ordem de envio original.

A estratégia para processar o resultado das operações de envio pode ter impacto do desempenho de imediato e significativa para a sua aplicação. Os exemplos nesta secção são escritos em c# e aplicam-se ao equivalently Futures de Java.

Se a aplicação produz bursts de mensagens, ilustrado aqui com um ciclo simples e foram aguardar a conclusão de cada enviar antes de enviar a mensagem seguinte, síncrona ou assíncronas formas de API igual, apenas enviar mensagens de 10 concluída após 10 sequenciais completa ida e volta para settlement.

Com um milissegundo de 70 assumida distância de latência de ida e volta do TCP de um site no local ao Service Bus e conceder apenas de 10 ms do Service Bus para aceitar e armazene cada mensagem, o seguinte ciclo ocupa, pelo menos, 8 segundos, sem contar tempo de transferência do payload ou potencial efeitos de congestionamento de rota:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Se a aplicação é iniciado 10 operações de envio assíncrono sucessivamente imediata e awaits os seus respetivo conclusão em separado, se o tempo de ida e volta para essas 10 operações de envio. As 10 mensagens são transferidas sucessivamente imediata, potencialmente, mesmo partilha frames Jumbo TCP, e a duração de transferência geral depende em grande medida relacionadas com a rede tempo para obter as mensagens transferidas para o mediador.

Tornar os mesmos pressupostos idêntica do ciclo anterior, o tempo de execução sobreposto total para o seguinte ciclo poderão permanecer bem num segundo:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

É importante ter em atenção que todos os modelos de programação assíncronos utilizam alguma forma de fila de trabalho baseado em memória, oculto, que contém operações pendentes. Quando [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (c#) ou **enviar** é colocada na cópia de segurança de fila de retorno (Java), a tarefa de envio na fila trabalho, mas o gesto de protocolo apenas é iniciada assim que desative a tarefa para ser executada. Para o código que tende a push bursts de mensagens e onde fiabilidade for uma preocupação, deve ter cuidado que não existem demasiadas mensagens são colocadas "em trânsito" de uma só vez, porque todas as mensagens enviadas demorar memória até factually ter sido put para a rede.

Semaphores, conforme mostrado no seguinte fragmento de código em c#, são objetos de sincronização que ativar essa limitação ao nível da aplicação quando necessário. Esta utilização de um semáforo permite no máximo 10 de mensagens em trânsito em simultâneo. Um dos 10 bloqueios semáforo disponível é executado antes de enviar e for lançada como o envio estiver concluída. 11th passar a ciclo aguarda pelo menos uma das antes do envia foi concluída e, em seguida, faz com que o respetivo bloqueio disponíveis:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

As aplicações devem **nunca** iniciar uma operação de envio assíncrono de forma "acionados e se esqueça" sem obter o resultado da operação. Se o fizer, pode carregar a fila de tarefas internas e invisível até esgotamento de memória e impedir que a aplicação detetar erros de envio:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Com um cliente AMQP de baixo nível, o Service Bus também aceita transferências "previamente settled". Uma transferência previamente settled é uma operação de fire e esquecido de mensagens em fila para o qual o resultado, qualquer forma, não foi reportado novamente para o cliente e a mensagem é considerada settled quando enviada. A falta de comentários do cliente também não significa que não existe nenhum dados disponíveis para diagnóstico, o que significa que este modo não se qualificam para obter ajuda através de suporte do Azure.

## <a name="settling-receive-operations"></a>Settling receber operações

Para receber operações, os clientes de API do Service Bus ativar dois modos diferentes de explícitos: *receber e eliminar* e *bloqueio observar*.

O [receber e eliminar](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo indica o mediador a ter em consideração todas as mensagens, envia ao cliente recetor como settled quando enviada. Isto significa que a mensagem é considerada consumido logo que o mediador tem colocá-la para a rede. Se a transferência de mensagens falhar, a mensagem é perdida.

Upside deste modo é que o recetor não necessário efetuar qualquer ação adicional na mensagem e também não é abrandado por a aguardar que o resultado a settlement. Se os dados contidos em mensagens individuais têm valor baixo e/ou são apenas significativos para muito curto período de tempo, este modo é uma opção de razoável.

O [bloqueio observar](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo indica o Mediador de que o cliente receber pretende liquidar os mensagens recebidas explicitamente. A mensagem é disponibilizada para o recetor processar, enquanto contido num bloqueio exclusivo no serviço para que outros, concorrentes recetores não é possível vê-lo. A duração do bloqueio inicialmente é definida ao nível da fila ou a subscrição e pode ser alargada ao cliente o proprietário do bloqueio, através de [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) operação.

Quando uma mensagem está bloqueada, outros clientes receber da mesma fila ou subscrição podem desempenhar bloqueios e obter as mensagens disponíveis seguintes não em bloqueio Active Directory. Quando o bloqueio de uma mensagem for lançado explicitamente ou quando o bloqueio expira, faz aparecer a mensagem de cópia de segurança ou perto do início da ordem de obtenção de redelivery.

Quando a mensagem é repetidamente lançada pela recetores ou permitem o bloqueio de decorrer para um determinado número de vezes ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), a mensagem é automaticamente removida da fila ou de subscrição e colocada associada fila de entregues.

O cliente receber inicia settlement de uma mensagem recebida com uma confirmação positiva quando chama [concluída](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) ao nível da API. Esta definição indica para o Mediador de que a mensagem foi processada com êxito e a mensagem é removida da fila ou de subscrição. O mediador responde com uma resposta que indica se o settlement foi possível efetuar a intenção de settlement o recetor.

Quando o cliente recetora não consegue processar uma mensagem, mas quiser ter a mensagem a ser reenviada, explicitamente pode pedir para a mensagem a ser libertado e desbloqueado instantaneamente chamando [abandonar](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) ou pode fazer nada e permitir que o bloqueio decorrer.

Se um cliente recetora não consegue processar uma mensagem e sabe que a mensagem redelivering e não irá ajudar a repetir a operação, pode rejeitar a mensagem, o qual move-o na fila entregues chamando [mensagens não entregues](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), que também permite definir uma propriedade personalizada, incluindo um código de motivo que pode ser obtido com a mensagem da fila entregues.

Num caso especial de settlement é diferimento por que é abordado num artigo separado.

O **concluída** ou **mensagens não entregues** operações, bem como o **RenewLock** operações podem falhar devido a problemas de rede, se o bloqueio realizado expirou ou se existem outros condições do lado do serviço que impedem settlement. Dos cenários de última, o serviço envia uma confirmação negativa que analisa como uma exceção nos clientes de API. Se o motivo é uma ligação de rede quebrada, o bloqueio é ignorado, uma vez que o barramento de serviço não suporta a recuperação de ligações AMQP existentes numa ligação diferente.

Se **concluída** falhar, que ocorre normalmente no final muito de processamento de mensagens e, em alguns casos depois de minutos de trabalho de processamento, a aplicação recetora pode decidir se preserva o estado do trabalho e ignora o mesmo mensagem quando é entregue uma segunda vez ou se tosses terminar o resultado de trabalho e repete as tentativas como a mensagem é reenviada.

É o mecanismo de típico para identificar o entregas mensagem duplicada ao verificar o-id de mensagem, que pode e deve ser definido pelo remetente para um valor exclusivo, possivelmente alinhado com um identificador do processo de origem. Um programador de tarefas, provavelmente, iria definir o id de mensagem para o identificador da tarefa está a tentar atribuir a uma função de trabalho com o trabalho especificado e o trabalho seria ignorar a segunda ocorrência da atribuição de tarefas, se essa tarefa já é concluída.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
