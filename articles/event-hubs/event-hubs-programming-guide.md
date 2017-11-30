---
title: "Guia de programação dos Event Hubs do Azure | Microsoft Docs"
description: "Escreva código para utilizar o SDK .NET da Azure de Event Hubs do Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/16/2017
ms.author: sethm
ms.openlocfilehash: 7d5f14d5a65253cf0aad1811ace419bf2f39f7db
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="event-hubs-programming-guide"></a>Guia de programação dos Event Hubs

Este artigo aborda alguns cenários comuns escrever código com o Event Hubs do Azure e o SDK .NET do Azure. Parte do princípio de que possui compreensão preliminar dos Event Hubs. Para obter uma descrição geral conceptual dos Event Hubs, consulte [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Publicadores de eventos

Enviar eventos para um hub de eventos utilizando o HTTP POST ou através de uma ligação AMQP 1.0. A escolha de qual utilizar e quando depende do cenário específico que está a ser resolvido. As ligações AMQP 1.0 são medidas como ligações mediadas no Service Bus e são mais adequadas nos cenários com requisitos de latência inferiores e volumes de mensagens altos frequentes, que fornecem um canal de mensagens persistente.

Pode criar e gerir os Event Hubs utilizando o [NamespaceManager][] classe. Quando utilizar as APIs .NET geridas, as construções primárias para publicar dados para os Event Hubs são as classes [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) e [EventData][]. [EventHubClient][] fornece o canal de comunicação AMQP, através do qual os eventos são enviados para o hub de eventos. O [EventData][] classe representa um evento e é utilizado para publicar mensagens para um hub de eventos. Esta classe inclui o corpo, alguns metadados e as informações de cabeçalho sobre o evento. Outras propriedades são adicionadas para o [EventData][] objeto como estes passam por um hub de eventos.

## <a name="get-started"></a>Introdução

As classes .NET que suportam Event Hubs são fornecidas na assemblagem Microsoft.ServiceBus.dll. A forma mais fácil para referenciar a API de Service Bus e para configurar a sua aplicação com todas as dependências do Service Bus é através da transferência do [Pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Em alternativa, pode utilizar a [Consola do Gestor de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) no Visual Studio. Para fazê-lo, emita o comando seguinte na janela da [Consola do Gestor de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Criar um hub de eventos
Pode utilizar a classe [NamespaceManager][] para criar Event Hubs. Por exemplo:

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Na maioria dos casos, é recomendável utilizar os métodos [CreateEventHubIfNotExists][] para evitar a geração de exceções se o serviço for reiniciado. Por exemplo:

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Todas as operações de criação de Event Hubs, incluindo [CreateEventHubIfNotExists][], requerem permissões de **Gestão** no espaço de nomes em questão. Se pretender limitar as permissões do publicador ou das aplicações de consumidor, pode evitar estas chamadas de operação de criação num código de produção ao utilizar credenciais com permissões limitadas.

O [EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) classe contém detalhes sobre um hub de eventos, incluindo as regras de autorização, o intervalo de retenção de mensagem, IDs de partição, o estado e caminho. Pode utilizar esta classe para atualizar os metadados de um hub de eventos.

## <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs
A classe principal para interagir com os Event Hubs é [eventhubclient][EventHubClient]. Esta classe fornece capacidades tanto para o emissor como para o recetor. Pode instanciar esta classe utilizando o [criar](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create) método, conforme mostrado no exemplo seguinte:

```csharp
var client = EventHubClient.Create(description.Path);
```

Este método utiliza as informações de ligação do Service Bus no ficheiro App.config, na secção `appSettings`. Para obter um exemplo do XML `appSettings` utilizado para armazenar as informações de ligação do Service Bus, consulte a documentação para o método [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_).

Outra opção consiste em criar o cliente a partir de uma cadeia de ligação. Esta opção funciona bem quando utilizar as funções de trabalho do Azure, uma vez que pode armazenar a cadeia nas propriedades de configuração do trabalho. Por exemplo:

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

A cadeia de ligação ficará no mesmo formato como é apresentada no ficheiro App.config dos métodos anteriores:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

Por fim, também é possível criar um [EventHubClient][] objeto a partir de um [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) instância, conforme mostrado no exemplo seguinte:

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

É importante tenha em atenção que adicionais [EventHubClient][] objetos criados a partir de uma instância de fábrica de mensagens reutilizar a mesma ligação TCP subjacente. Por conseguinte, estes objetos têm um limite de débito no lado do cliente. O método [Criar](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) reutiliza uma fábrica de mensagens única. Se precisar de débito muito elevado de um remetente único, pode criar várias fábricas de mensagens e um objeto [EventHubClient][] a partir de cada fábrica de mensagens.

## <a name="send-events-to-an-event-hub"></a>Enviar eventos para um hub de eventos
Pode enviar eventos para um hub de eventos criando uma [EventData][] instância e enviando-a através do [enviar](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) método. Este método aceita um único [EventData][] parâmetro da instância e de forma síncrona envia-a para um hub de eventos.

## <a name="event-serialization"></a>Serialização de eventos
A classe [EventData][] tem [quatro construtores sobrecarregados](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_) que assumem uma variedade de parâmetros, tal como um objeto e um serializador, uma matriz de bytes ou uma transmissão. Também é possível instanciar a classe [EventData][] e definir o fluxo do corpo posteriormente. Quando utilizar o JSON com [EventData][], pode utilizar **Encoding.UTF8.GetBytes()** para obter a matriz de bytes para uma cadeia codificada em JSON.

## <a name="partition-key"></a>Chave de partição
A classe [EventData][] tem uma propriedade [PartitionKey][] que permite que o remetente especifique um valor que é protegido por hash para produzir uma atribuição de partição. Utilizar uma chave de partição garante que todos os eventos com a mesma chave são enviados de eventos para a mesma partição hub. As chaves de partição comuns incluem os IDs de sessão do utilizador e os IDs do remetente exclusivos. A propriedade [PartitionKey][] é opcional e pode ser fornecida quando utilizar os métodos [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) ou [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_). Se não fornecer um valor para [PartitionKey][], os eventos enviados são distribuídos pelas partições utilizando um modelo round robin.

### <a name="availability-considerations"></a>Considerações de disponibilidade

Utilizar uma chave de partição é opcional e, pondere cuidadosamente se deve ou não utilizar um. Em muitos casos, a utilização de uma chave de partição é uma boa opção se a ordenação de eventos é importante. Quando utiliza uma chave de partição, estas partições necessitam de disponibilidade num único nó e podem ocorrer falhas ao longo do tempo; Por exemplo, quando computação reinício de nós e patch. Como tal, se definir um ID de partição e que partição fica indisponível por algum motivo, uma tentativa de aceder aos dados dessa partição irá falhar. Se elevada disponibilidade é mais importante, não especifique uma chave de partição; Nesse caso os eventos são enviados para partições com o modelo round robin descrito anteriormente. Neste cenário, estiver a efetuar uma opção explícita entre disponibilidade (ID de partição) e consistência (afixação eventos para um ID de partição).

Outra consideração está a processar atrasos no processamento de eventos. Em alguns casos, poderá ser melhor remover dados e tente novamente ao tentar manter-se com o processamento, que pode provocar, potencialmente, mais atrasos de processamento a jusante. Por exemplo, com um ticker as cotações é melhor a aguardar para dados atualizados concluídos, mas num chat em direto ou cenário VOIP em vez disso terá dos dados rapidamente, mesmo que não está concluída.

Fornecido estas considerações de disponibilidade, nestes cenários poderá escolher uma do seguinte erro estratégias de processamento de:

- Parar (paragem ler a partir de Event Hubs até que sejam corrigidas coisas)
- Remover (mensagens não são importantes, largue-os)
- Repita (novamente mensagens como ver cabem)
- [Entregues](../service-bus-messaging/service-bus-dead-letter-queues.md) (utilizar uma fila ou outro event hub a mensagens não letra apenas as mensagens não conseguiu processar)

Para obter mais informações e ver um debate sobre os compromissos entre a disponibilidade e consistência, consulte [disponibilidade e a consistência no Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operações de envio de eventos em lote
Enviar eventos em lotes pode ajudar a aumentar o débito. O [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) método um **IEnumerable** parâmetro do tipo [EventData][] e envia o lote inteiro como uma operação atómica para o hub de eventos.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Tenha em atenção que um único lote não pode exceder o limite de 256 KB de um evento. Além disso, cada mensagem no lote utiliza a mesma identidade do publicador. É da responsabilidade do remetente certificar-se de que o lote não excede o tamanho máximo do evento. Se exceder esse tamanho, é gerado um erro **Enviar** do cliente. Pode utilizar o método de programa auxiliar [EventHubClient.CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch) para se certificar de que o lote não excede 256 KB. Obter vazio [EventDataBatch](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch) do [CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch) API e, em seguida, utilize [TryAdd](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.tryadd#Microsoft_ServiceBus_Messaging_EventDataBatch_TryAdd_Microsoft_ServiceBus_Messaging_EventData_) adicionar eventos ao construir o batch. Por último, utilize [EventDataBatch.ToEnumerable](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.toenumerable) para obter os eventos subjacentes para passar para o [EventHubClient.Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) API.

## <a name="send-asynchronously-and-send-at-scale"></a>Enviar no modo assíncrono e enviar à escala
Também pode enviar eventos para um hub de eventos no modo assíncrono. Enviar no modo assíncrono pode aumentar a taxa a que um cliente é capaz de enviar eventos. Tanto os métodos [Enviar](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) como [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendbatch) estão disponíveis em versões assíncronas que devolvem um objeto [Tarefa](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Apesar de esta técnica poder aumentar o débito, também pode fazer com que o cliente continue a enviar eventos mesmo quando está a ser limitado pelo serviço dos Event Hubs e pode resultar em falhas do cliente ou mensagens perdidas se não for implementada corretamente. Além disso, pode utilizar a propriedade [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity.retrypolicy) no cliente para controlar as opções de repetição do cliente.

## <a name="create-a-partition-sender"></a>Criar um remetente de partição
Embora seja mais comum para enviar eventos para um hub de eventos sem uma chave de partição, em alguns casos poderá querer enviar eventos diretamente para uma determinada partição. Por exemplo:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) devolve um [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) que pode utilizar para publicar eventos para uma partição do hub de eventos específicos de objeto.

## <a name="event-consumers"></a>Consumidores de eventos
Os Event Hubs possuem dois modelos principais para o consumo de eventos: recetores diretos e abstrações de nível superior, tais como [EventProcessorHost][]. Direcionar recetores são responsáveis pela sua própria coordenação de acesso às partições dentro de um *grupo de consumidores*. Um grupo de consumidores é uma vista (estado, posição ou desvio) para um hub de eventos particionada.

### <a name="direct-consumer"></a>Consumidor direto
A forma mais direta de ler a partir de uma partição consiste em utilizar o [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) classe. Para criar uma instância desta classe, tem de utilizar uma instância da classe [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup). No exemplo seguinte, o ID de partição tem de ser especificado quando criar o recetor para o grupo de consumidores:

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

O método [CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) tem várias sobrecargas que facilitam o controlo sobre o leitor a ser criado. Estes métodos incluem a especificação de um desvio como uma cadeia ou um carimbo de data/hora e a capacidade de especificar se pretende incluir este desvio especificado na transmissão devolvida ou iniciar depois. Depois de criar o recetor, pode começar a receber eventos no objeto devolvido. O método [Receção](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) tem quatro sobrecargas que controlam os parâmetros de operação de receção, como o tamanho de lote e o tempo de espera. Pode utilizar as versões assíncronas destes métodos para aumentar o débito de um consumidor. Por exemplo:

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

No que respeita a uma partição específica, as mensagens são recebidas na ordem em que foram enviadas para o hub de eventos. O desvio é um token de cadeia utilizado para identificar uma mensagem numa partição.

Tenha em atenção que uma única partição não pode ter mais de 5 leitores simultâneos ligados em qualquer altura. À medida que os leitores se ligam ou desligam, as respetivas sessões poderão permanecer ativas durante vários minutos antes do serviço reconhecer que estes se desligaram. Durante este período, o restabelecimento da ligação a uma partição poderá falhar. Para obter um exemplo completo de escrever um recetor direto para os Event Hubs, consulte o [Recetores diretos de Hubs de eventos](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) exemplo.

### <a name="event-processor-host"></a>Anfitrião do processador de eventos
A classe [EventProcessorHost][] processa dados dos Event Hubs. Deve utilizar esta implementação quando criar os leitores dos eventos na plataforma .NET. O [EventProcessorHost][] fornece um ambiente de tempo de execução seguro para thread com vários processos para as implementações do processador de eventos que também fornece pontos de verificação e gestão da concessão da partição.

Para utilizar a classe [EventProcessorHost][], pode implementar o [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor). Esta interface contém três métodos:

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

Para iniciar o processamento de eventos, instanciar [EventProcessorHost][], fornecendo os parâmetros adequados para o seu hub de eventos. Em seguida, chame [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) para registar a implementação [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) com o tempo de execução. Neste momento, o anfitrião tentará adquirir uma concessão em cada partição num hub de eventos utilizando um algoritmo "abrangente". Estas concessões última para um determinado período de tempo e, em seguida, tem de ser renovados. À medida que novos nós, instâncias de trabalho neste caso, ficam online, colocam reservas de concessões e ao longo do tempo a carga alterna entre os nós à medida que cada uma tenta adquirir mais concessões.

![Anfitrião do Processador de Eventos](./media/event-hubs-programming-guide/IC759863.png)

Ao longo do tempo, é estabelecido um equilíbrio. Esta capacidade dinâmica permite que o dimensionamento automático baseado em CPU possa ser aplicado aos consumidores para aumentar verticalmente e horizontalmente. Porque os Event Hubs não têm um conceito direto de contagens de mensagens, a utilização média da CPU é, frequentemente, o mecanismo para medir o dimensionamento do consumidor ou de fim anterior. Se os publicadores começarem a publicar mais eventos do que aqueles que os consumidores podem processar, o aumento da CPU nos consumidores pode ser utilizado para provocar um dimensionamento automático na contagem de instâncias de trabalho.

A classe [EventProcessorHost][] também implementa um mecanismo de pontos de verificação baseado no Storage do Azure. Este mecanismo armazena o desvio numa base por partição, para que cada consumidor possa determinar qual era o último ponto de verificação do consumidor anterior. Como a transição das partições entre nós é efetuada por concessões, este é o mecanismo de sincronização que facilita a deslocação da carga.

## <a name="publisher-revocation"></a>Revogação do publicador
Para além das funcionalidades avançadas de tempo de execução do [EventProcessorHost][], os Event Hubs permitem a revogação do publicador para bloquear publicadores específicos de enviar o evento para um hub de eventos. Estas funcionalidades são úteis se um token do publicador tiver ficado comprometido ou se uma atualização de software está a causar se comportem inapropriadamente. Nestas situações, a identidade do publicador, que faz parte do respetivo token SAS, pode ser bloqueada a partir dos eventos de publicação.

Para obter mais informações sobre a revogação do publicador e sobre como enviar Hubs de Eventos como um publicador, veja o exemplo de [Event Hubs Large Scale Secure Publishing (Publicação Segura em Larga Escala dos Hubs de Eventos)](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
* [Novidades dos Event Hubs](event-hubs-what-is-event-hubs.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Event processor host API reference (Referência da API do anfitrião do processador de eventos)](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
