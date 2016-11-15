---
title: "Guia de programação dos Event Hubs do Azure | Microsoft Docs"
description: "Descreve a programação com os Event Hubs do Azure utilizando o SDK .NET do Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1b7a19868e75811198f54150ced78e51f42d8017


---
# <a name="event-hubs-programming-guide"></a>Guia de programação dos Event Hubs
Este tópico descreve a programação com os Event Hubs do Azure utilizando o SDK .NET do Azure. Parte do princípio de que possui compreensão preliminar dos Event Hubs. Para obter uma descrição geral conceptual dos Event Hubs, consulte [Descrição geral dos Event Hubs](event-hubs-overview.md).

## <a name="event-publishers"></a>Publicadores de eventos
O envio de eventos para um Hub de Eventos é efetuado utilizando o HTTP POST ou através de uma ligação AMQP 1.0. A escolha de qual o método a utilizar depende do cenário específico que está a ser abordado. As ligações AMQP 1.0 são medidas como ligações mediadas no Service Bus e são mais adequadas nos cenários com requisitos de latência inferiores e volumes de mensagens altos frequentes, que fornecem um canal de mensagens persistente.

Os Event Hubs são criados e geridos através da classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Quando utilizar as APIs .NET geridas, as construções primárias para publicar dados para os Event Hubs são as classes [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) e [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) fornece o canal de comunicação AMQP, através do qual os eventos são enviados para o Hub de Eventos. A classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) representa um evento e é utilizada para publicar mensagens num Event Hub. Esta classe inclui o corpo, alguns metadados e as informações de cabeçalho sobre o evento. Outras propriedades são adicionadas ao objeto [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) à medida que este passa através de um Hub de Eventos.

## <a name="get-started"></a>Introdução
As classes .NET que suportam Event Hubs são fornecidas na assemblagem Microsoft.ServiceBus.dll. A forma mais fácil para referenciar a API de Service Bus e para configurar a sua aplicação com todas as dependências do Service Bus é através da transferência do [Pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Em alternativa, pode utilizar a [Consola do Gestor de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) no Visual Studio. Para fazê-lo, emita o comando seguinte na janela da [Consola do Gestor de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Criar um Hub de Eventos
Pode utilizar a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para criar Event Hubs. Por exemplo:

```
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Na maioria dos casos, é recomendável utilizar os métodos [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) para evitar a geração de exceções se o serviço for reiniciado. Por exemplo:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Todas as operações de criação de Event Hubs, incluindo [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), requerem permissões de **Gestão** no espaço de nomes em questão. Se pretender limitar as permissões do publicador ou das aplicações de consumidor, pode evitar estas chamadas de operação de criação num código de produção ao utilizar credenciais com permissões limitadas.

A classe [EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) contém detalhes sobre um Hub de Eventos, incluindo as regras de autorização, o intervalo de retenção de mensagens, os IDs de partição, o estado e o caminho. Pode utilizar esta classe para atualizar os metadados de um Hub de Eventos.

## <a name="create-an-event-hubs-client"></a>Criar um cliente dos Event Hubs
A classe principal para interagir com os Event Hubs é [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx). Esta classe fornece capacidades tanto para o emissor como para o recetor. Pode instanciar esta classe utilizando o método [Criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx), conforme mostrado no exemplo seguinte.

```
var client = EventHubClient.Create(description.Path);
```

Este método utiliza as informações de ligação do Service Bus no ficheiro App.config, na secção `appSettings`. Para obter um exemplo do XML `appSettings` utilizado para armazenar as informações de ligação do Service Bus, consulte a documentação para o método [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx).

Outra opção consiste em criar o cliente a partir de uma cadeia de ligação. Esta opção funciona bem quando utilizar as funções de trabalho do Azure, uma vez que pode armazenar a cadeia nas propriedades de configuração do trabalho. Por exemplo:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

A cadeia de ligação ficará no mesmo formato como é apresentada no ficheiro App.config dos métodos anteriores:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Por fim, também é possível criar um objeto [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) a partir de uma instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx), conforme mostrado no exemplo seguinte.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

É importante que tenha em atenção que os objetos [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) adicionais criados a partir de uma instância de fábrica de mensagens irão reutilizar a mesma ligação TCP subjacente. Por conseguinte, estes objetos têm um limite de débito no lado do cliente. O método [Criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) reutiliza uma fábrica de mensagens única. Se precisar de débito muito elevado de um remetente único, pode criar várias fábricas de mensagens e um objeto [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) a partir de cada fábrica de mensagens.

## <a name="send-events-to-an-event-hub"></a>Enviar eventos para um Hub de Eventos
Pode enviar eventos para um Hub de Eventos criando uma instância [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e enviando-a através do método [Enviar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx). Este método obtém um único parâmetro da instância [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e envia-o de forma síncrona para um Hub de Eventos.

## <a name="event-serialization"></a>Serialização de eventos
A classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) tem [quatro construtores sobrecarregados](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.eventdata.aspx) que assumem uma variedade de parâmetros, tal como um objeto e um serializador, uma matriz de bytes ou uma transmissão. Também é possível instanciar a classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e definir o fluxo do corpo posteriormente. Quando utilizar o JSON com [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx), pode utilizar **Encoding.UTF8.GetBytes()** para obter a matriz de bytes para uma cadeia codificada em JSON.

## <a name="partition-key"></a>Chave de partição
A classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) tem uma propriedade [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) que permite que o remetente especifique um valor que é protegido por hash para produzir uma atribuição de partição. Utilizar uma chave de partição garante que todos os eventos com a mesma chave sejam enviados para a mesma partição do Hub de Eventos. As chaves de partição comuns incluem os IDs de sessão do utilizador e os IDs do remetente exclusivos. A propriedade [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) é opcional e pode ser fornecida quando utilizar os métodos [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) ou [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). Se não fornecer um valor para [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), os eventos enviados são distribuídos pelas partições utilizando um modelo round robin.

## <a name="batch-event-send-operations"></a>Operações de envio de eventos em lote
Enviar eventos em lotes pode aumentar substancialmente o débito. O método [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) obtém um parâmetro **IEnumerable** do tipo [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e envia o lote inteiro como uma operação atómica para o Hub de Eventos.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Tenha em atenção que um único lote não pode exceder o limite de 256 KB de um evento. Além disso, cada mensagem no lote utiliza a mesma identidade do publicador. É da responsabilidade do remetente certificar-se de que o lote não excede o tamanho máximo do evento. Se exceder esse tamanho, é gerado um erro **Enviar** do cliente.

## <a name="send-asynchronously-and-send-at-scale"></a>Enviar no modo assíncrono e enviar à escala
Pode também enviar eventos para um Hub de Eventos no modo assíncrono. Enviar no modo assíncrono pode aumentar a taxa a que um cliente é capaz de enviar eventos. Tanto os métodos [Enviar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) como [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) estão disponíveis em versões assíncronas que devolvem um objeto [Tarefa](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Apesar de esta técnica poder aumentar o débito, também pode fazer com que o cliente continue a enviar eventos mesmo quando está a ser limitado pelo serviço dos Event Hubs e pode resultar em falhas do cliente ou mensagens perdidas se não for implementada corretamente. Além disso, pode utilizar a propriedade [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) no cliente para controlar as opções de repetição do cliente.

## <a name="create-a-partition-sender"></a>Criar um remetente de partição
Embora seja mais comum para enviar eventos para um Hub de Eventos com uma chave de partição, em alguns casos, poderá querer enviar eventos diretamente para uma determinada partição. Por exemplo:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) devolve um objeto [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx) que pode utilizar para publicar eventos para uma partição do Hub de Eventos específica.

## <a name="event-consumers"></a>Consumidores de eventos
Os Event Hubs possuem dois modelos principais para o consumo de eventos: recetores diretos e abstrações de nível superior, tais como [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). Os recetores diretos são responsáveis pela sua própria coordenação de acesso às partições dentro de um grupo de consumidores.

### <a name="direct-consumer"></a>Consumidor direto
A forma mais direta de ler a partir de uma partição dentro de um grupo de consumidores consiste em utilizar a classe [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx). Para criar uma instância desta classe, tem de utilizar uma instância da classe [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx). No exemplo seguinte, o ID de partição tem de ser especificado quando criar o recetor para o grupo de consumidores.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

O método [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) tem várias sobrecargas que facilitam o controlo sobre o leitor a ser criado. Estes métodos incluem a especificação de um desvio como uma cadeia ou um carimbo de data/hora e a capacidade de especificar se pretende incluir este desvio especificado na transmissão devolvida ou iniciar depois. Depois de criar o recetor, pode começar a receber eventos no objeto devolvido. O método [Receção](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) tem quatro sobrecargas que controlam os parâmetros de operação de receção, como o tamanho de lote e o tempo de espera. Pode utilizar as versões assíncronas destes métodos para aumentar o débito de um consumidor. Por exemplo:

```
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

No que respeita a uma partição específica, as mensagens são recebidas na ordem pela qual foram enviadas para o Hub de Eventos. O desvio é um token de cadeia utilizado para identificar uma mensagem numa partição.

Tenha em atenção que uma única partição dentro de um grupo de consumidores não pode ter mais de 5 leitores simultâneos ligados em qualquer altura. À medida que os leitores se ligam ou desligam, as respetivas sessões poderão permanecer ativas durante vários minutos antes do serviço reconhecer que estes se desligaram. Durante este período, o restabelecimento da ligação a uma partição poderá falhar. Para obter um exemplo completo de como escrever um recetor direto para os Event Hubs, consulte o exemplo [Recetores Diretos de Event Hubs do Service Bus](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### <a name="event-processor-host"></a>Anfitrião do processador de eventos
A classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) processa dados dos Event Hubs. Deve utilizar esta implementação quando criar os leitores dos eventos na plataforma .NET. O [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornece um ambiente de tempo de execução seguro para thread com vários processos para as implementações do processador de eventos que também fornece pontos de verificação e gestão da concessão da partição.

Para utilizar a classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), pode implementar o [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx). Esta interface contém três métodos:

* [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)
* [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)
* [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Para iniciar o processamento de eventos, instancie [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), fornecendo os parâmetros adequados para o Hub de Eventos. Em seguida, chame [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) para registar a implementação [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) com o tempo de execução. Neste ponto, o anfitrião tentará adquirir uma concessão em cada partição do Hub de Eventos utilizando um algoritmo “abrangente”. Estas concessões duram um determinado período de tempo e, em seguida, têm de ser renovadas. À medida que novos nós, instâncias de trabalho neste caso, ficam online, colocam reservas de concessões e ao longo do tempo a carga alterna entre os nós à medida que cada uma tenta adquirir mais concessões.

![Anfitrião do Processador de Eventos](./media/event-hubs-programming-guide/IC759863.png)

Ao longo do tempo, é estabelecido um equilíbrio. Esta capacidade dinâmica permite que o dimensionamento automático baseado em CPU possa ser aplicado aos consumidores para aumentar verticalmente e horizontalmente. Uma vez que os Event Hubs não têm um conceito direto de contagens de mensagens, a média de utilização da CPU é, muitas vezes, o mecanismo para medir o dimensionamento do consumidor ou do back-end. Se os publicadores começarem a publicar mais eventos do que aqueles que os consumidores podem processar, o aumento da CPU nos consumidores pode ser utilizado para provocar um dimensionamento automático na contagem de instâncias de trabalho.

A classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) também implementa um mecanismo de pontos de verificação baseado no Storage do Azure. Este mecanismo armazena o desvio numa base por partição, para que cada consumidor possa determinar qual era o último ponto de verificação do consumidor anterior. Como a transição das partições entre nós é efetuada por concessões, este é o mecanismo de sincronização que facilita a deslocação da carga.

## <a name="publisher-revocation"></a>Revogação do publicador
Para além das funcionalidades avançadas de tempo de execução do [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), os Event Hubs permitem a revogação do publicador para impedir que publicadores específicos enviem eventos para um Event Hub. Estas funcionalidades são particularmente úteis se um token do publicador tiver ficado comprometido ou se uma atualização de software estiver a fazer com que elas se comportem inapropriadamente. Nestas situações, a identidade do publicador, que faz parte do respetivo token SAS, pode ser bloqueada a partir dos eventos de publicação.

Para obter mais informações sobre a revogação do publicador e sobre como enviar Hubs de Eventos como um publicador, veja o exemplo de [Event Hubs Large Scale Secure Publishing (Publicação Segura em Larga Escala dos Hubs de Eventos)](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os cenários dos Event Hubs, consulte estas ligações:

* [Event Hubs API overview (Descrição geral da API dos Hubs de Eventos)](event-hubs-api-overview.md)
* [Descrição geral dos Hubs de Eventos](event-hubs-overview.md)
* [Exemplos de código dos Hubs de Eventos](http://code.msdn.microsoft.com/site/search?query=event hub&f\[0\].Value=event hub&f\[0\].Type=SearchText&ac=5)
* [Event processor host API reference (Referência da API do anfitrião do processador de eventos)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)




<!--HONumber=Nov16_HO2-->


