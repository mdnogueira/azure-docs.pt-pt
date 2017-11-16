---
title: "Criar tópicos e filas de Service Bus do Azure particionadas | Microsoft Docs"
description: "Descreve como partição de tópicos e filas do Service Bus, utilizando vários mediadores de mensagens."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: beebfb496604b422e091cd3b4425933f3cea1283
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados
Service Bus do Azure emprega vários mediadores de mensagens para processar mensagens e vários arquivos de mensagens para armazenar as mensagens. Uma fila convencional ou um tópico é processado por um mediador de mensagens única e armazenado num arquivo de mensagens. Barramento de serviço *partições* ativar as filas e tópicos, ou *entidades de mensagens*, para ser particionado em vários mediadores de mensagens e arquivos de mensagens. Isto significa que o débito global de uma entidade particionada já não é limitado pelo desempenho de um mediador de mensagens única ou um arquivo de mensagens. Além disso, uma falha temporária de um arquivo de mensagens não compor uma fila particionada ou um tópico indisponível. Particionada filas e tópicos podem conter avançadas todas as funcionalidades de Service Bus, como o suporte para transações e sessões.

Para obter informações sobre as características de Service Bus, consulte o [arquitetura do Service Bus] [ Service Bus architecture] artigo.

Criação de partições é ativada por predefinição durante a criação de entidade em todas as filas e tópicos Standard e Premium mensagens. Pode criar padrão entidades de camada de mensagens sem a criação de partições, mas as filas e tópicos num espaço de nomes Premium sempre de ser particionados; Não é possível desativar esta opção. 

Não é possível alterar a opção de criação de partições numa fila existente ou tópico em camadas Standard ou Premium, só é possível definir a opção ao criar a entidade.

## <a name="how-it-works"></a>Como funciona

Cada fila particionada ou um tópico é composta por vários fragmentos. Cada fragmento é armazenado num arquivo de mensagens diferentes e processado por um mediador de mensagens diferentes. Quando é enviada uma mensagem para uma fila particionada ou um tópico, o Service Bus atribui a mensagem para um dos fragmentos. A seleção é feita aleatoriamente ao Service Bus ou utilizando uma chave de partição que pode especificar o remetente.

Quando um cliente pretende receber uma mensagem numa fila particionada ou a partir de uma subscrição para um tópico particionada, consultas de Service Bus todos os fragmentos de mensagens, em seguida, devolve a primeira mensagem que é obtida a partir de qualquer um dos arquivos de mensagens para o recetor. Caches de Service Bus, as outras mensagens e devolve-las quando recebe adicionais recebem pedidos. Um cliente receber não está ciente de criação de partições; o comportamento de orientado para o cliente de uma fila particionada ou um tópico (por exemplo, ler, concluir, diferir, mensagens não entregues, prefetching) é idêntico ao comportamento de uma entidade regular.

Não há sem custos adicionais quando uma mensagem a enviar ou receber uma mensagem de fila particionada ou tópico.

## <a name="enable-partitioning"></a>Ativar a criação de partições

Para utilizar particionadas filas e tópicos com o Service Bus do Azure, utilize o SDK do Azure versão 2.2 ou posterior, ou especifique `api-version=2013-10` ou posterior no seus pedidos HTTP.

### <a name="standard"></a>Standard

O escalão padrão de mensagens, pode criar filas do Service Bus e tópicos em tamanhos de 1, 2, 3, 4 ou 5 GB (a predefinição é de 1 GB). Com a criação de partições ativada, o Service Bus cria 16 cópias (16 partições) da entidade para cada GB que especificar. Como tal, se criar uma fila que é de 5 GB de tamanho, com 16 partições o tamanho máximo da fila fique (5 \* 16) = 80 GB. Pode ver o tamanho máximo da fila particionada ou tópico observando a entrada [portal do Azure][Azure portal], no **descrição geral** painel para essa entidade.

### <a name="premium"></a>Premium

Um espaço de nomes de escalão Premium, pode criar filas do Service Bus e tópicos em tamanhos de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 GB (a predefinição é de 1 GB). Com a criação de partições ativada por predefinição, o Service Bus cria duas partições por entidade. Pode ver o tamanho máximo da fila particionada ou tópico observando a entrada [portal do Azure][Azure portal], no **descrição geral** painel para essa entidade.

Para obter mais informações sobre a criação de partições da camada de serviço de mensagens Premium, consulte [Premium do Service Bus e escalões de mensagens Standard](service-bus-premium-messaging.md). 

### <a name="create-a-partitioned-entity"></a>Criar uma entidade particionada

Existem várias formas para criar uma fila particionada ou tópico. Quando criar a fila ou um tópico da sua aplicação, pode ativar a criação de partições para a fila ou um tópico, respetivamente definindo a [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] ou [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] propriedade **verdadeiro**. Estas propriedades tem de ser definidas no momento da fila ou tópico é criado. Conforme indicado anteriormente, não é possível alterar estas propriedades numa fila existente ou tópico. Por exemplo:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Em alternativa, pode criar uma fila particionada ou um tópico a [portal do Azure] [ Azure portal] ou no Visual Studio. Quando cria uma fila ou um tópico no portal, o **ativar a criação de partições** opção na fila ou tópico **criar** caixa de diálogo está selecionada por predefinição. Só pode desativar esta opção na entidade de escalão Standard; o escalão Premium criação de partições está sempre ativada. No Visual Studio, clique o **ativar partições** caixa de verificação no **nova fila** ou **novo tópico** caixa de diálogo.

## <a name="use-of-partition-keys"></a>Utilização de chaves de partição
Quando uma mensagem é colocados em fila para uma fila particionada ou um tópico, o barramento de serviço verifica a presença de uma chave de partição. Se localizar alguma, seleciona o fragmento com base nessa chave. Se encontrar uma chave de partição, seleciona o fragmento com base no algoritmo interno.

### <a name="using-a-partition-key"></a>Utilizar uma chave de partição
Alguns cenários, tais como sessões ou de transações, necessitam de ser armazenado um fragmento específico de mensagens. Todos estes cenários requerem a utilização de uma chave de partição. Todas as mensagens que utilizam a mesma chave de partição estão atribuídas ao mesmo fragmento. Se o fragmento está temporariamente indisponível, o Service Bus devolve um erro.

Dependendo do cenário, as propriedades da mensagem diferentes são utilizadas como uma chave de partição:

**SessionId**: Se tiver uma mensagem a [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] propriedade definida, em seguida, o Service Bus utiliza esta propriedade como a chave de partição. Desta forma, todas as mensagens que pertencem à mesma sessão são processadas pelo mesmo Mediador de mensagens. Isto permite que o barramento de serviço para garantir a mensagem de ordenação, bem como a consistência dos Estados de sessão.

**PartitionKey**: Se tiver uma mensagem a [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] propriedade, mas não o [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] propriedade definida, em seguida, o Service Bus utiliza o [PartitionKey] [ PartitionKey] propriedade como a chave de partição. Se a mensagem tem ambos os [SessionId] [ SessionId] e [PartitionKey] [ PartitionKey] conjunto de propriedades, ambas as propriedades devem ser idênticas. Se o [PartitionKey] [ PartitionKey] propriedade está definida para um valor diferente que o [SessionId] [ SessionId] propriedade, o Service Bus devolve um inválido Exceção de operação. O [PartitionKey] [ PartitionKey] propriedade deve ser utilizada se um remetente envia mensagens transacionais de conhecimento não são de sessão. A chave de partição garante que todas as mensagens que são enviadas dentro de uma transação são processadas pelo Mediador de mensagens mesmo.

**MessageId**: se a fila ou um tópico tem o [QueueDescription.RequiresDuplicateDetection] [ QueueDescription.RequiresDuplicateDetection] propriedade definida como **verdadeiro** e [ BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] ou [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] propriedades não são definidas, a [ BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] propriedade serve como a chave de partição. (Tenha em atenção de que as bibliotecas Microsoft .NET e AMQP atribuir automaticamente um ID de mensagem se a aplicação de envio não.) Neste caso, todas as cópias da mesma mensagem são processadas pelo mesmo Mediador de mensagens. Isto permite que o Service Bus detetar e eliminar mensagens duplicadas. Se o [QueueDescription.RequiresDuplicateDetection] [ QueueDescription.RequiresDuplicateDetection] propriedade não está definida como **verdadeiro**, não considere que o barramento de serviço a [MessageId] [ MessageId] propriedade como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não utilizar uma chave de partição
Na ausência de uma chave de partição, o Service Bus distribui as mensagens de uma forma round robin para todos os fragmentos da fila particionada ou tópico. Se o fragmento que escolheu não estiver disponível, o Service Bus atribui a mensagem para um fragmento diferentes. Desta forma, a operação de envio é concluída com êxito, apesar de indisponibilidade temporária de um arquivo de mensagens. No entanto, não irá conseguir a ordenação garantido que fornece uma chave de partição.

Para um debate mais aprofundado da variação entre a disponibilidade (nenhuma chave de partição) e consistência (utilizando uma chave de partição), consulte [neste artigo](../event-hubs/event-hubs-availability-and-consistency.md). Estas informações aplicam-se igualmente a entidades particionadas do Service Bus.

Para dar o Service Bus suficiente tempo para colocar em fila a mensagem para um fragmento diferentes, o [MessagingFactorySettings.OperationTimeout] [ MessagingFactorySettings.OperationTimeout] valor especificado pelo cliente que envia a mensagem tem de ser superior a 15 segundos. É recomendável que defina o [OperationTimeout] [ OperationTimeout] propriedade para o valor predefinido de 60 segundos.

Tenha em atenção que uma chave de partição "pins" uma mensagem para um fragmento específico. Se o arquivo de mensagens que detém este fragmento do não estiver disponível, o Service Bus devolve um erro. Na ausência de uma chave de partição, Service Bus pode escolher um fragmento diferentes e a operação é concluída com êxito. Por conseguinte, é recomendado que não fornecer uma chave de partição, exceto se for necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos de avançadas: utilizar transações com entidades particionadas
As mensagens que são enviadas como parte de uma transação tem de especificar uma chave de partição. Isto pode ser uma das seguintes propriedades: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], ou [ BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Todas as mensagens que são enviadas como parte da mesma transação tem de especificar a mesma chave de partição. Se tentar enviar uma mensagem sem uma chave de partição dentro de uma transação, o Service Bus devolve uma exceção de operação inválido. Se tentar enviar várias mensagens na mesma transação que têm chaves de partição diferentes, o Service Bus devolve uma exceção de operação inválido. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se alguma das propriedades que funcionam como uma chave de partição estiver definida, o Service Bus pins a mensagem para um fragmento específico. Este comportamento ocorre se ou não é utilizada uma transação. É recomendado que não especificou uma chave de partição se não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Utilizar sessões com entidades particionadas
Para enviar uma mensagem transacional para um tópico com suporte para a sessão ou uma fila, a mensagem tem de ter o [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] propriedade definida. Se o [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] é especificada, bem como a propriedade, tem de ser idêntica do [SessionId] [ SessionId] propriedade. Caso sejam diferentes, o Service Bus devolve uma exceção de operação inválido.

Ao contrário das filas regulares (não particionadas) ou tópicos, não é possível utilizar uma transação única para enviar várias mensagens para sessões diferentes. Se for tentada, o Service Bus devolve uma exceção de operação inválido. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reencaminhamento de mensagens automática com entidades particionadas
Service Bus suporta mensagem automática de reencaminhamento da, para ou entre entidades particionadas. Para ativar o reencaminhamento de mensagens automática, defina o [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] propriedade na fila de origem ou de subscrição. Se a mensagem Especifica uma chave de partição ([SessionId][SessionId], [PartitionKey][PartitionKey], ou [MessageId] [ MessageId]), essa chave de partição é utilizada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e diretrizes
* **Funcionalidades de consistência elevada**: se uma entidade utiliza funcionalidades como sessões, deteção duplicada ou controlo explícito de chave de criação de partições, em seguida, as operações de mensagens sempre são encaminhadas para fragmentos específicos. Se qualquer um dos fragmentos de experiência de tráfego elevado ou o repositório subjacente está danificado, essas operações falharem e disponibilidade é reduzida. Em geral, é ainda muito superior a entidades particionadas não; a consistência apenas um subconjunto de tráfego está com problemas, por oposição a todo o tráfego. Para obter mais informações, consulte este [debate de disponibilidade e consistência](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gestão**: operações, tais como criar, atualizar e eliminar tem de ser efetuadas em todos os fragmentos da entidade. Se qualquer fragmento está danificado, pode resultar em falhas para estas operações. Para a operação Get, informações tais como contagens de mensagem tem de ser agregadas a partir de todos os fragmentos. Se qualquer fragmento é mau estado de funcionamento, o estado de disponibilidade da entidade é reportado como limitado.
* **Cenários de mensagem de volume de baixa**: para tais cenários, especialmente quando utilizar o protocolo HTTP, poderá ter de efetuar várias receber operações para obter todas as mensagens. Para receber pedidos, o front-end executa receber em todos os fragmentos e coloca em cache todas as respostas recebidas. Um pedido de recepção subsequentes na mesma ligação seria beneficiar Esta colocação em cache e receber latências irão ser mais baixas. No entanto, se tiver várias ligações ou utilizar HTTP, que estabelece uma ligação nova para cada pedido. Como tal, não há nenhuma garantia de que este seria apresentado no mesmo nó. Se todas as mensagens existentes são bloqueadas e em cache no outro front-end, a operação de receção devolve **nulo**. Mensagens eventualmente expirarem e pode recebê-las novamente. Recomenda-se a ligação keep-alive de HTTP.
* **Procurar/observar mensagens**: [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) sempre não devolver o número de mensagens especificado no [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) propriedade. Existem duas razões comuns para este. Uma razão é que o tamanho do conjunto de mensagens agregado excede o tamanho máximo de 256 KB. Outra razão é que, se a fila ou um tópico tem o [EnablePartitioning propriedade](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) definido como **verdadeiro**, uma partição poderá não ter suficiente mensagens para concluir o número de mensagens de pedido. Em geral, se quiser ter uma aplicação receber um número específico de mensagens em fila, deve chamar [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) repetidamente até que obtém esse número de mensagens em fila ou não existem nenhum mais mensagens ao pré-visualizar. Para obter mais informações, incluindo exemplos de código, consulte o [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) documentação da API.

## <a name="latest-added-features"></a>Mais recentes funcionalidades adicionadas
* Adicionar ou remover a regra é agora suportada com entidades particionadas. Diferentes das entidades não particionadas, estas operações não são suportadas em transações. 
* AMQP é agora suportado para enviar e receber mensagens a partir de uma entidade particionada.
* AMQP é agora suportado para as seguintes operações: [Batch enviar](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batch receber](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Receive pelo número de sequência](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [observar](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [ Renovar bloqueio](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [agendar mensagem](/dotnet/api/microsoft.servicebus.messaging.queueclient.schedulemessageasync), [Cancelar mensagem agendada](/dotnet/api/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync), [Adicionar regra](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [remover a regra](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Bloqueio de renovação de sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [estado da sessão conjunto](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [obter estado da sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), e [enumerar sessões](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Limitações de entidades particionadas
Atualmente o Service Bus impõe as seguintes limitações no particionada filas e tópicos:

* Particionada filas e tópicos não suportam o envio de mensagens que pertencem a sessões diferentes numa única transação.
* Barramento de serviço atualmente permite até 100 filas particionadas ou tópicos por espaço de nomes. Cada fila particionada ou um tópico conta para a quota de 10 000 entidades por espaço de nomes (não se aplica ao escalão Premium).

## <a name="next-steps"></a>Passos seguintes
Leia sobre os conceitos principais do AMQP 1.0 mensagens especificação no [guia de protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
