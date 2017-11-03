---
title: "Espaços de nomes de emparelhar do Service Bus do Azure | Microsoft Docs"
description: "Detalhes de implementação do espaço de nomes emparelhado e os custos"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: sethm
ms.openlocfilehash: bdd4c7948608c03447d1e040a746ed0eb7b0771b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Emparelhado detalhes de implementação do espaço de nomes e as implicações de custos
O [PairNamespaceAsync] [ PairNamespaceAsync] método, utilizando um [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instância, executa tarefas visível no o nome. Porque não existe são custo considerações quando utilizar a funcionalidade, é útil para entender as tarefas necessárias para que espera que o comportamento quando ocorre. A API envolva o seguinte comportamento automático em seu nome:

* Criação de filas de registo de segurança.
* A criação de um [MessageSender] [ MessageSender] objeto sua filas ou tópicos.
* Quando uma entidade de mensagens fica indisponível, envia executar um ping a entidade de mensagens numa tentativa para detetar quando essa entidade fica disponível novamente.
* Opcionalmente, cria um conjunto de "bombas de mensagem" que movam mensagens de filas de registo de segurança para as filas primárias.
* As coordenadas de originar falha fecho/passe do site primário e secundário [MessagingFactory] [ MessagingFactory] instâncias.

Um nível elevado, a funcionalidade funciona da seguinte forma: quando a entidade principal está em bom estada, sem alterações de comportamento ocorrerem. Quando o [FailoverInterval] [ FailoverInterval] decorrer duração e a entidade principal vê bem-sucedida não envia após um não transitório [MessagingException] [ MessagingException] ou um [TimeoutException][TimeoutException], ocorre o seguinte comportamento:

1. Envie operações para a entidade principal estão desativadas e o sistema efetua o ping a entidade principal até pings podem ser fornecidas com êxito.
2. Uma fila de registo de segurança aleatório está selecionada.
3. [BrokeredMessage] [ BrokeredMessage] objetos são encaminhados para a fila de registo de segurança que escolheu.
4. Se uma operação de envio para a fila de registo de segurança que escolheu falhar, essa fila é retirada da rotação e uma nova fila está selecionada. Todos os remetentes no [MessagingFactory] [ MessagingFactory] instância saiba da falha.

As seguintes figuras depict a sequência. Em primeiro lugar, o remetente envia mensagens.

![Espaços de nomes emparelhados][0]

Após a falha ao enviar para a fila principal, o remetente começa a enviar mensagens para uma fila de registo de segurança escolhido aleatoriamente. Em simultâneo, este inicia uma tarefa de ping.

![Espaços de nomes emparelhados][1]

Neste momento, as mensagens ainda estão em fila secundária e não tem sido entregar para a fila principal. Depois da fila principal está em bom estada novamente, pelo menos um processo deve estar a executar o syphon. O syphon fornece as mensagens de todas as filas vários do registo de segurança para as entidades de destino adequada (filas e tópicos).

![Espaços de nomes emparelhados][2]

O resto deste tópico aborda os detalhes específicos do como funcionam estas duas.

## <a name="creation-of-backlog-queues"></a>Criação de filas de registo de segurança
O [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] transmitido um objeto para o [PairNamespaceAsync] [ PairNamespaceAsync] método indica o número de filas de registo de segurança que pretende utilizar. Cada fila de registo de segurança, em seguida, é criada com as seguintes propriedades explicitamente definido (todos os outros valores estão definidos para o [QueueDescription] [ QueueDescription] predefinições):

| Caminho | [espaço de nomes primário] / x-servicebus-transferência / [Índice] em [Índice] é um valor na [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| maxDeliveryCount |Int. MaxValue |
| DefaultMessageTimeToLive |TimeSpan. MaxValue |
| AutoDeleteOnIdle |TimeSpan. MaxValue |
| LockDuration |um minuto |
| EnableDeadLetteringOnMessageExpiration |VERDADEIRO |
| EnableBatchedOperations |VERDADEIRO |

Por exemplo, a fila de registo de segurança primeiro criada para o espaço de nomes **contoso** é denominado `contoso/x-servicebus-transfer/0`.

Ao criar as filas, o código verifica primeiro se existe uma fila deste tipo. Se a fila não existir, a fila é criada. O código não filas "adicional" registo de segurança de limpeza. Especificamente, se a aplicação com o espaço de nomes primário **contoso** pedidos cinco filas de registo de segurança, mas uma fila de registo de segurança com o caminho `contoso/x-servicebus-transfer/7` existe, essa fila de registo de segurança adicional ainda está presente, mas não é utilizada. O sistema permite explicitamente filas de registo de segurança adicional para existe que não deverá ser utilizadas. Como proprietário do espaço de nomes, é responsável por quaisquer filas de registo de segurança não utilizadas/indesejado de limpeza. O motivo para esta decisão é que o barramento de serviço não é possível saber que fins existem para todas as filas no seu espaço de nomes. Além disso, se uma fila existe com o nome fornecido, mas não cumpre o assumida [QueueDescription][QueueDescription], então as razões são as suas próprias para alterar o comportamento predefinido. Sem garantias são efetuadas para que as modificações para as filas de registo de segurança por código. Certifique-se testar cuidadosamente as suas alterações.

## <a name="custom-messagesender"></a>MessageSender personalizado
Quando for enviada, todas as mensagens de passam por um interno [MessageSender] [ MessageSender] objeto comporta-se normalmente quando tudo funciona e redireciona para o registo de segurança de filas quando coisas "break." Ao receber uma falha não transitório, inicia um temporizador. Após um [TimeSpan] [ TimeSpan] período constituídas pelo [FailoverInterval] [ FailoverInterval] durante os quais são enviadas sem mensagens efetuada com êxito, o valor da propriedade a ativação pós-falha é parte. Neste momento, as seguintes ações ocorrem para cada entidade:

* Executa uma tarefa de ping cada [PingPrimaryInterval] [ PingPrimaryInterval] para verificar se a entidade está disponível. Assim que esta tarefa for bem sucedida, todo o código de cliente que utiliza a entidade imediatamente inicia o envio de mensagens de novo para o espaço de nomes primário.
* Pedidos futuros para enviar para essa mesma entidade de quaisquer outros remetentes resultará no [BrokeredMessage] [ BrokeredMessage] enviados de ser modificados para manter-se na fila de registo de segurança. A modificação remove algumas propriedades do [BrokeredMessage] [ BrokeredMessage] de objeto e armazena-os noutro local. As seguintes propriedades são limpo e adicionadas sob um novo alias, permitindo que o barramento de serviço e o SDK processar mensagens de forma uniforme:

| Nome de propriedade antigo | Novo nome de propriedade |
| --- | --- |
| ID de sessão |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

O caminho de destino original também é armazenado dentro a mensagem como uma propriedade chamada x-ms-path. Este design permite que as mensagens para muitas entidades para coexistir numa fila única de registo de segurança. As propriedades são convertidas Retroceder pelo syphon.

Personalizado [MessageSender] [ MessageSender] objeto pode encontrar problemas quando as mensagens de abordam o limite de 256 KB e ativação pós-falha é parte. Personalizado [MessageSender] [ MessageSender] objeto armazena mensagens para todas as filas e tópicos em conjunto nas filas de registo de segurança. Este objeto mistura mensagens de muitos primaries em conjunto dentro das filas de registo de segurança. Para lidar com balanceamento de carga entre muitos clientes que não souber entre si, o SDK seleciona aleatoriamente uma fila de registo de segurança para cada [QueueClient] [ QueueClient] ou [TopicClient] [ TopicClient] criar no código.

## <a name="pings"></a>Pings
Uma mensagem ping está vazio [BrokeredMessage] [ BrokeredMessage] com o respetivo [ContentType] [ ContentType] propriedade definida para a aplicação/vnd.ms-servicebus-ping e um [TimeToLive] [ TimeToLive] valor de 1 segundo. Este ping tem uma característica especial no Service Bus: o servidor nunca oferece um ping quando qualquer chamador solicita um [BrokeredMessage][BrokeredMessage]. Assim, nunca tem de saber como receber e ignorar estas mensagens. Cada entidade (fila exclusiva ou um tópico) por [MessagingFactory] [ MessagingFactory] instância por cliente será possível executar o ping ao que são consideradas fique indisponível. Por predefinição, isto ocorre uma vez por minuto. Mensagens ping são consideradas como sendo regulares mensagens do Service Bus e podem resultar em custos de largura de banda e as mensagens. Assim que os clientes detetarem que o sistema estiver disponível, as mensagens parar.

## <a name="the-syphon"></a>O syphon
Pelo menos um programa executável da aplicação deve ser ativamente em execução a syphon. O syphon efetua uma longa consulta receber que dura 15 minutos. Quando todas as entidades estão disponíveis e tem 10 filas de registo de segurança, a aplicação que aloja o syphon chama a operação de receção 40 vezes por hora, 960 vezes por dia e vezes 28800 no prazo de 30 dias. Quando o syphon é ativamente a mover as mensagens do registo de segurança para a fila primária, cada mensagem experiências os encargos seguintes (padrão custos de largura de banda e o tamanho da mensagem aplicam em todas as fases):

1. Envie para o registo de segurança.
2. Receba o registo de segurança.
3. Envie para o site primário.
4. Receba a partir de principal.

## <a name="closefault-behavior"></a>Comportamento de fecho/falhas
Dentro de uma aplicação que aloja o syphon, uma vez primário ou secundário [MessagingFactory] [ MessagingFactory] falhas ou está fechado sem o respetivo parceiro também a ser falhou ou fechado e o syphon Deteta neste estado, o syphon funciona. Se o outro [MessagingFactory] [ MessagingFactory] não estiver fechada dentro de cinco segundos, o syphon faults o aberto ainda [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Passos seguintes
Consulte [padrões e elevada disponibilidade de mensagens assíncronas] [ Asynchronous messaging patterns and high availability] para um debate detalhado de mensagens assíncronas do Service Bus. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
