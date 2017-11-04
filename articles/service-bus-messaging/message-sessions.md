---
title: "Sessões de mensagens do Service Bus do Azure | Microsoft Docs"
description: "Processe as sequências de mensagens do Service Bus do Azure com sessões."
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
ms.openlocfilehash: c6441d2119518e89a869ee52e5f0b80450ae2bbe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="message-sessions--first-in-first-out-fifo"></a>Sessões de mensagens / pela primeira vez, primeiro out (FIFO) 

Sessões de Service Bus permitem processamento conjunta e ordenado de unbounded sequências de mensagens relacionadas. Para tirar partido uma garantia FIFO no Service Bus tem de utilizar sessões. Barramento de serviço não é prescritiva sobre a natureza da relação entre as mensagens e também não definir um modelo particular para determinar em que uma sequência de mensagem inicia ou termina.

Qualquer remetente pode criar uma sessão quando enviar mensagens para um tópico ou uma fila definindo a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) Mediador de propriedade para algumas Identificador definido pela aplicação que é exclusiva para a sessão. Ao nível do protocolo AMQP 1.0, este valor é mapeado para o *id do grupo* propriedade.

Em filas com suporte para a sessão ou subscrições, sessões entrem em existência quando existe, pelo menos, uma mensagem com a sessão [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Quando existe uma sessão, não há nenhum tempo definido ou a API para quando a sessão expirar ou desaparece. Teoria, pode ser recebida uma mensagem para uma sessão de hoje em dia, a mensagem seguinte na hora de um ano e se o **SessionId** corresponder, a sessão é o mesmo da perspetiva de Service Bus.

Normalmente, no entanto, uma aplicação tiver uma noção limpar de onde um conjunto de mensagens relacionadas inicia e termina; mas barramento de serviço não define quaisquer regras específicas.

Um exemplo de como delinear uma sequência para transferir um ficheiro é para definir o **etiqueta** propriedade para a primeira mensagem para **iniciar**, mensagens intermédio para **conteúdo**, e para a última mensagem para **final**. A posição relativa das mensagens de conteúdo pode ser calculada como mensagem atual *SequenceNumber* delta do **iniciar** da mensagem *SequenceNumber*.

A funcionalidade de sessão ativa de Service Bus específico receber operação, sob a forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) na c# e APIs de Java. Ativar a funcionalidade definindo a [Requersessão](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) propriedade a fila ou a subscrição através do Azure Resource Manager, ou ao definir o sinalizador no portal. Isto é necessário antes de tentar a utilizar as operações de API relacionadas.

No portal, o sinalizador está definido com a caixa de verificação seguinte:

![][2]

As APIs para sessões existem em clientes de fila e a subscrição. Há um modelo imperativo onde pode controlar quando as sessões e as mensagens são recebidas e um baseado no processador de modelo, semelhante à *OnMessage*, que oculta a complexidade de gerir a receção cíclicas.

## <a name="session-features"></a>Funcionalidades de sessão

Sessões fornecem demultiplexing em simultâneo de fluxos de mensagens intercalado ao preservar e guaranteeing entrega ordenada.

![][1]

A [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) recetor é criado pelo cliente de aceitação de uma sessão. Imperatively, as chamadas de cliente [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) em c#. O modelo de chamada de retorno reativa,-regista um processador de sessão, tal como explicado mais tarde.

Quando o [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceite e enquanto esta está retida por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com nessa sessão [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) existentes na fila ou subscrição bem como em todas as mensagens com que **SessionId** que chegam ainda enquanto a sessão é mantida.

O bloqueio é libertado quando **fechar** ou **CloseAsync** são denominados, ou quando o bloqueio expira nos casos em que a aplicação é consegue fazer. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo num ficheiro, que significa que a aplicação deve fechar a sessão, assim já não tem-e/ou não espera mais mensagens.

Quando vários recetores simultâneos solicitar a partir da fila, as mensagens que pertencem a uma determinada sessão são distribuídas para o recetor específico que atualmente detém o bloqueio para essa sessão. Com essa operação, um fluxo de mensagens intercalado que reside numa fila ou a subscrição está corretamente anular multiplexado a recetores diferentes e os recetores também podem em direto em máquinas de cliente diferente, uma vez que a gestão de bloqueio acontece lado do serviço, no interior Barramento de serviço.

Uma fila, no entanto, ainda é uma fila: não há nenhum acesso aleatório. Se vários recetores simultâneos aguardar para aceitar sessões específicas ou aguarde que as mensagens de sessões específicas e houver uma mensagem na parte superior de uma fila que pertencem a uma sessão que não recetor ainda tem reclamado, entregas contém até um recetor de sessão de afirmações que sessão.

A ilustração anterior mostra três recetores de sessão em simultâneo, tudo o que tem de efetuar ativamente mensagens da fila para cada recetor tornar o progresso. A sessão anterior *SessionId*= 4 não tem nenhum Active Directory proprietário de cliente, que significa que não existem mensagens são entregues a qualquer pessoa até essa mensagem já foi tomada por um criado recentemente, o proprietário recetor de sessão.

Enquanto que poderão parecer estar constraining, um processo único recetor pode processar várias sessões simultâneas facilmente, especialmente quando estas são escritas com o código de estritamente assíncrono; juggling várias sessões simultâneas dozen é efetivamente automática com o modelo de chamada de retorno.

A estratégia para processar demasiadas sessões em simultâneo, na qual cada sessão apenas esporadicamente recebe mensagens, é enviado ao processador remover a sessão após algum tempo de inatividade e retomar o processamento quando a sessão é aceite como chega a sessão seguinte.

O bloqueio de sessão, o recetor de sessão é um umbrella para os bloqueios de mensagem utilizado pelo *peek bloqueio* modo settlement. Um recetor não pode ter duas mensagens simultaneamente "em trânsito", mas as mensagens têm de ser processadas por ordem. Uma nova mensagem só pode ser obtida quando a mensagem anterior foi concluída ou lettered de mensagens não. Abandonar um causas de mensagem a mesma mensagem para ser servidos novamente com a próxima operação de receção.

## <a name="message-session-state"></a>Estado da sessão de mensagem

Quando os fluxos de trabalho são processados em grande escala, os sistemas em nuvem de elevada disponibilidade, o processador de fluxo de trabalho associado uma determinada sessão devem ser capazes de recuperar de falhas inesperadas e também de ser capaz de retomar o trabalho parcialmente concluído no outro máquina em que o trabalho data de início ou processo.

A instalações de estado de sessão permite uma anotação definido pela aplicação de uma sessão de mensagem dentro do Mediador, para que o estado de processamento registado relativamente à sessão torna-se disponíveis de imediato quando a sessão é adquirida por um processador de novo.

Da perspetiva de Service Bus, o estado da sessão mensagem é um objeto de binário opaco que possa englobar os dados do tamanho de uma mensagem é 256 KB para padrão de barramento de serviço e de 1 MB para Premium do Service Bus. O estado de processamento relativo para uma sessão pode ser contido dentro o estado da sessão ou o estado da sessão pode apontar para alguns localização de armazenamento ou o registo de base de dados que contém essas informações.

As APIs para gerir o estado da sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), pode ser encontrado no [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objeto c# tanto nas APIs de Java. Numa sessão que tinha anteriormente sem estado de sessão definido devolve um **nulo** referenciar para **GetState**. Limpar o estado da sessão previamente definido é feito com [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Todas as sessões existentes numa fila ou a subscrição podem ser enumeradas com o **SessionBrowser** método na Java API e com [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) no [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) no cliente do .NET.

O estado da sessão contido uma fila ou em que contagens de uma subscrição para a quota de armazenamento essa entidade. Quando a aplicação for concluída com uma sessão, recomenda-se, por conseguinte, para a aplicação Limpar estado retido para evitar o custo de gestão externo.

## <a name="next-steps"></a>Passos seguintes

- [Um exemplo completo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/BasicSessionSendReceiveUsingQueueClient) enviar e receber mensagens baseados em sessão do barramento de serviço e escalona as utilizando a biblioteca .NET padrão.
- [Um exemplo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) que utiliza o cliente do .NET Framework para processar mensagens com suporte para a sessão. 

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png