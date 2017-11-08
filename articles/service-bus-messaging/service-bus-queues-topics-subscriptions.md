---
title: "Descrição geral de mensagens de filas, tópicos e subscrições do Azure Service Bus | Microsoft Docs"
description: "Descrição geral de entidades de mensagens do Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e subscrições do Service Bus

Microsoft Azure Service Bus suporta um conjunto de tecnologias de middleware baseado na nuvem, orientado para a mensagem, incluindo a colocação de mensagens fiável e durável publicação de mensagens. Estas capacidades de mensagens "mediadas" podem considerar como desassociada funcionalidades de mensagens que o suporte de publicação-subscrição, desacoplamento temporal e cenários utilizando a carga de trabalho mensagens do Service Bus de balanceamento de carga. A comunicação desacoplada tem muitas vantagens; por exemplo, os clientes e os servidores podem ligar-se conforme necessário e efetuar as operações de forma assíncrona.

As entidades de mensagens que formam a core das capacidades de mensagens no Service Bus são filas, tópicos e subscrições e regras/ações.

## <a name="queues"></a>Filas

As filas oferecem *First In, Out primeiro* entrega de mensagens (FIFO) para um ou mais consumidores concorrentes. Ou seja, mensagens normalmente deverão ser recebidas e processadas pelos recetores pela ordem em que foram adicionadas à fila e cada mensagem é recebida e processada por apenas um consumidor de mensagens. Uma vantagem de utilizar as filas é obter "desacoplamento temporal" dos componentes da aplicação. Por outras palavras, os produtores (remetentes) e os consumidores (recetores) não dispõe de enviar e receber mensagens ao mesmo tempo, porque as mensagens são armazenadas de maneira duradoura na fila. Além disso, o produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e enviar mensagens.

Uma vantagem relacionada é "nivelamento de carga," que permite aos produtores e consumidores enviar e receber mensagens a taxas diferentes. Em muitas aplicações, a carga de sistema varia ao longo do tempo; No entanto, o tempo de processamento necessário para cada unidade de trabalho é geralmente constante. A intermediação de mensagem de produtores e consumidores através de uma fila significa que a aplicação de consumo só tem de ser aprovisionada para conseguir processar a carga média em vez de pico de carga. A profundidade da fila aumenta e contrai à medida que a carga a receber varia. Tal poupa diretamente dinheiro em relação a quantidade de infraestrutura necessária para a carga de aplicação de serviço. À medida que a carga aumenta, mais processos de trabalho podem ser adicionados a leitura da fila. Cada mensagem é processada apenas por um dos processos de trabalho. Além disso, este balanceamento de carga baseado na solicitação permite que uma utilização ideal dos computadores de trabalho, mesmo se os computadores de trabalho diferirem em relação a potência de processamento, como estas mensagens de extração na sua própria taxa máxima. Este padrão é frequentemente denominado padrão o padrão "competir consumidor".

Utilizar filas para intermédio entre mensagem produtores e consumidores fornece um coupling soltas inerente entre os componentes. Porque os produtores e consumidores não têm conhecimento entre si, um consumidor possa ser atualizado sem ter qualquer efeito no produtor.

Criar uma fila é um processo de vários passo. Efetuar operações de gestão do Service Bus (filas e tópicos) de entidades de mensagens através de [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) classe, que é criada, fornecendo o endereço base do espaço de nomes de barramento de serviço e as credenciais do utilizador. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) fornece métodos para criar, enumerar e eliminar entidades de mensagens. Depois de criar um [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) objeto de objeto de nome SAS e chave e um gestão de espaço de nomes de serviço, pode utilizar o [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) método para criar a fila. Por exemplo:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Em seguida, pode criar um objeto de fila e uma fábrica de mensagens com o URI de barramento de serviço como um argumento. Por exemplo:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Em seguida, pode enviar mensagens para a fila. Por exemplo, se tiver uma lista de mensagens mediadas chamado `MessageList`, o código apresentado de forma semelhante ao seguinte exemplo:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Em seguida, receber mensagens da fila da seguinte forma:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

No [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo, a operação de receção única; ou seja, quando o Service Bus recebe o pedido, marca a mensagem como consumida e devolve a mesma à aplicação. **ReceiveAndDelete** modo é o modelo mais simples e funciona melhor para cenários em que a aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus marca a mensagem como consumida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

No [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo, a operação de receção torna-se duas etapas que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe o pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a receção outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazenar a mesma de forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao atribuir o nome [Concluída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) à mensagem recebida. Quando o Service Bus vê a **concluída** chamada, marca a mensagem como consumida.

Se a aplicação não é possível processar a mensagem por algum motivo, pode chamar o [abandonar](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) método à mensagem recebida (em vez de [concluída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Isto permite que o Service Bus desbloqueie a mensagem e disponibilizá-lo ser novamente recebida, através do consumidor mesmo ou outro consumidor concorrente. Lugar, existe um tempo limite associado o bloqueio e se a aplicação não conseguir processar a mensagem antes do bloqueio de tempo limite expirar (por exemplo, se a falha da aplicação), então o Service Bus desbloqueia a mensagem e torna a mesma disponível para ser recebida novamente (essencialmente efetuar um [abandonar](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) operação por predefinição).

Tenha em atenção que no caso de falha da aplicação após o processamento da mensagem, mas antes o **concluída** pedido é emitido, a mensagem é reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado *, pelo menos, uma vez* processamento; ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não pode tolerar o processamento duplicado, em seguida, é necessário lógica adicional na aplicação para detetar duplicados, que pode ser conseguido com base no **MessageId** propriedade da mensagem, que permanece constante em tentativas de entrega. Isto é conhecido como *exatamente uma vez* processamento.

## <a name="topics-and-subscriptions"></a>Tópicos e subscrições
Contrariamente filas, em que cada mensagem é processada por um único consumidor, *tópicos* e *subscrições* proporcionar uma forma de um-para-muitos de comunicação, um *publicar/subscrever* padrão. Útil para dimensionamento para um grande número de destinatários, cada mensagem publicada é disponibilizada para cada subscrição registada juntamente com o tópico. As mensagens enviadas para um tópico e entregar uma ou mais subscrições associadas, consoante as regras de filtro que podem ser definidas numa base por subscrição. As subscrições podem utilizar filtros adicionais para restringir as mensagens que pretende receber. As mensagens são enviadas para um tópico da mesma forma são enviadas para uma fila, mas mensagens não são recebidas do tópico diretamente. Em vez disso, são recebidos de subscrições. Uma subscrição de tópico é semelhante uma fila virtual que recebe cópias das mensagens que são enviadas para o tópico. As mensagens são recebidas a partir uma subscrição de forma idêntica ao modo como o que são recebidos a partir de uma fila.

A título de comparação, a funcionalidade de uma fila de envio de mensagem mapeia diretamente para um tópico e a funcionalidade de receção de mensagens mapeia para uma subscrição. Entre outras coisas, isto significa que as subscrições suportam os padrões mesmos descritos anteriormente nesta secção em relação a filas: consumidor concorrente, desacoplamento temporal, nivelamento de carga e balanceamento de carga.

Criar um tópico é semelhante ao criar uma fila, conforme mostrado no exemplo na secção anterior. Crie o URI do serviço e, em seguida, utilize o [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) classe para criar o cliente do espaço de nomes. Em seguida, pode criar um tópico, utilizando o [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) método. Por exemplo:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Em seguida, adicione as subscrições como pretendido:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Em seguida, pode criar um cliente de tópico. Por exemplo:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Utilizar o remetente da mensagem, pode enviar e receber mensagens de e para o tópico, conforme mostrado na secção anterior. Por exemplo:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Semelhantes a filas, as mensagens são recebidas a partir de uma subscrição a utilizar um [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) objeto em vez de um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) objeto. Crie a subscrição do cliente, transmitir o nome do tópico, o nome da subscrição e (opcionalmente) o modo de receção como parâmetros. Por exemplo, com o **inventário** subscrição:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Regras e ações
Em vários cenários, as mensagens com características específicas tem de ser processadas de formas diferentes. Para ativar esta opção, pode configurar subscrições para localizar mensagens que tenham pretendido propriedades e, em seguida, efetuar determinadas modificações para essas propriedades. Enquanto as subscrições do Service Bus ver todas as mensagens enviadas para o tópico, só poderá copiar um subconjunto dessas mensagens para a fila virtual da subscrição. Isto é conseguido utilizando filtros de subscrição. Essas alterações são denominadas *filtrar ações*. Quando é criada uma subscrição, pode fornecer uma expressão de filtro que opera nas propriedades da mensagem, ambas as propriedades do sistema (por exemplo, **etiqueta**) e as propriedades de aplicação personalizada (por exemplo, **StoreName**.) A expressão de filtro do SQL Server é opcional neste caso; sem uma expressão de filtro do SQL Server, será efetuada qualquer ação do filtro definida em subscrições em todas as mensagens para essa subscrição.

Utilizando o exemplo anterior, para filtrar mensagens apenas feitos **Store1**, poderá criar a subscrição do Dashboard da seguinte forma:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Com este filtro de subscrição no local, apenas as mensagens com a `StoreName` propriedade definida como `Store1` são copiados para a fila virtual para o `Dashboard` subscrição.

Para obter mais informações sobre os valores de filtro possíveis, consulte a documentação para o [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) classes. Além disso, consulte o [mensagens mediadas: filtros avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) e [filtros de tópico](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) amostras.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes tópicos para obter mais informações e exemplos de como utilizar mensagens do Service Bus de avançadas.

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Tutorial de .NET de mensagens mediadas do Service Bus](service-bus-brokered-tutorial-dotnet.md)
* [Tutorial de REST de mensagens mediadas do Service Bus](service-bus-brokered-tutorial-rest.md)
* [Mensagens mediadas: Exemplo de filtros de avançadas](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

