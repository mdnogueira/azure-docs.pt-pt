---
title: "Descrição geral de Reliable Actors de recursos de infraestrutura de serviço | Microsoft Docs"
description: "Introdução ao modelo de programação Reliable Actors do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 640e051a909b1b9457b20cbd507b418342297c6e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introdução aos Reliable Actors do Service Fabric
Reliable Actors é uma estrutura de aplicação de Service Fabric com base no [Ator Virtual](http://research.microsoft.com/en-us/projects/orleans/) padrão. A API de Atores fiável fornece um único thread modelo de programação incorporado nas garantias de escalabilidade e fiabilidade fornecidas pelo Service Fabric.

## <a name="what-are-actors"></a>Quais são Atores?
Um ator é uma unidade isolada, independentemente da computação e de estado de execução de thread único. O [padrão de ator](https://en.wikipedia.org/wiki/Actor_model) é um modelo computacional para sistemas em simultâneo ou distribuídos no qual um grande número destas atores pode ser executados em simultâneo e independentemente entre si. Actors possam comunicar entre si e podem criar mais de atores.

### <a name="when-to-use-reliable-actors"></a>Quando utilizar Reliable Actors
Serviço de recursos de infraestrutura Reliable Actors é uma implementação do padrão de conceção de atores. Tal como acontece com quaisquer padrão de conceção de software, a decisão se deve utilizar um padrão específico é efetuada com base em se pretende ou não um software design problema encaixa padrão.

Embora o ator padrão de design pode ser uma boa ajustar a um número de cenários, tendo em conta as restrições de padrão e a arquitetura que implementar têm de ser efetuado cuidados e problemas de sistemas distribuídos. Como orientação geral, considere o padrão de ator para modelar o seu cenário ou um problema se:

* O espaço de problema envolve um grande número (milhares ou mais) de pequenas, independentes e isoladas unidades de estado e a lógica.
* Pretende trabalhar com objetos único thread que não necessitam de significativa interação de componentes externos, incluindo a consultar o estado através de um conjunto de atores.
* As instâncias de ator não irão bloquear os chamadores com atrasos imprevisíveis ao emitir operações de e/s.

## <a name="actors-in-service-fabric"></a>Atores nos recursos de infraestrutura de serviço
No Service Fabric, atores são implementados no framework Reliable Actors: uma arquitetura de aplicações baseados no padrão de ator incorporada de [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Cada serviço de Atores fiável que escreve é realmente uma particionadas, com monitorização de estado fiável serviço.

Cada ator está definido como uma instância de um tipo de ator idêntico para a forma como objeto .NET é uma instância de um tipo .NET. Por exemplo, poderá existir um tipo de ator que implementa a funcionalidade de um cálculo e podem existir muitas atores desse tipo que estão distribuídos em vários nós de um cluster. Cada essa ator é identificado de forma exclusiva por um ID de actor.

### <a name="actor-lifetime"></a>Duração de ator
Atores do Service Fabric são virtuais, o que significa que os respetivos duração não está associada a respetiva representação dentro da memória. Como resultado, não é necessário ser explicitamente criado ou destruído. O tempo de execução Reliable Actors ativa automaticamente um tempo de ator primeiro recebe um pedido para esse ID de actor. Se um ator não for utilizado por um período de tempo, o tempo de execução Reliable Actors libertação da memória-recolhe o objeto de memória. Também mantém os dados de conhecimento da existência de ator deve têm de ser reativados mais tarde. Para obter mais detalhes, consulte [coleção de ciclo de vida e libertação da memória de Ator](service-fabric-reliable-actors-lifecycle.md).

Esta abstração da duração de ator virtual acarreta alguns avisos como resultado de modelo de ator virtual e, na realidade a implementação de Reliable Actors deviates por vezes deste modelo.

* Um ator é ativada automaticamente (fazendo com que um objeto de ator para ser construído) na primeira vez que é enviada uma mensagem para um ID de actor. Após um determinado período de tempo, o objeto de atores é memória libertada. No futuro, com o ID de actor novamente, faz com que um novo objeto de atores a ser criada. O estado de um ator outlives duração do objeto quando armazenada no Gestor de estado.
* Chamar qualquer método de ator para um ID de actor ativa essa ator. Por este motivo, os tipos de ator ter o respetivo construtor chamado implicitamente pelo runtime. Por conseguinte, código de cliente não é possível transmitir parâmetros para o construtor do tipo de ator, embora parâmetros podem ser transmitidos para o construtor de ator pelo serviço próprio. O resultado é que os atores podem ser construídos num Estado inicializado parcialmente do tempo de que outros métodos são denominados no mesmo, se o ator necessitar de parâmetros de inicialização do cliente. Não há nenhum ponto de entrada único para a ativação de um ator do cliente.
* Embora Reliable Actors implicitamente criar objetos de ator; Tem a capacidade de eliminar explicitamente um ator e o respetivo estado.

### <a name="distribution-and-failover"></a>Distribuição e ativação pós-falha
Para fornecer escalabilidade e fiabilidade, o Service Fabric distribui atores em todo o cluster e migra-los automaticamente de nós de falha para bom estado de funcionamento diferentes, conforme necessário. Esta é uma abstração através de um [particionadas, com monitorização de estado de serviço fiável](service-fabric-concepts-partitioning.md). Distribuição, escalabilidade, fiabilidade e ativação pós-falha automática são todos fornecido em virtude o facto de atores em execução no interior de um serviço fiável com monitorização de estado chamado o *serviço de Atores*.

Atores estão distribuídos as partições do serviço de Atores e essas partições são distribuídas por nós num cluster de Service Fabric. Cada partição de serviço contém um conjunto de atores. O Service Fabric gere distribuição e a ativação pós-falha das partições de serviço.

Por exemplo, um serviço de atores com nove partições implementadas em três nós com a colocação de partição de ator predefinido teria de ser distribuído thusly:

![Distribuição do Reliable Actors][2]

A estrutura de Ator gere as definições de intervalo de esquema e a chave de partição para si. Isto simplifica algumas opções, mas também acarreta tidos em consideração:

* Reliable Services permite-lhe escolher um esquema de criação de partições, o intervalo de chaves (ao utilizar um esquema de criação de partições de intervalo) e contagem de partição. Reliable Actors está restringida ao intervalo de criação de partições de esquema (o esquema de Int64 uniforme) e necessita de utilizar o intervalo de chave completo Int64.
* Por predefinição, atores aleatoriamente são colocados em partições, resultando em distribuição uniforme.
* Porque atores aleatoriamente são colocados, deve ser esperada operações de ator poder requerem sempre a comunicação de rede, incluindo a serialização e a anulação da serialização de dados de chamada de método, incorrer em overhead e latência.
* Cenários avançados, é possível controlo ator partição colocação utilizando Int64 ator IDs que mapeiam para partições específicas. No entanto, fazê-lo, por isso, pode resultar numa distribuição desequilibrada de atores em partições.

Para obter mais informações sobre como os serviços de atores se partições, consulte [conceitos de criação de partições de atores](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

### <a name="actor-communication"></a>Comunicação de ator
Interações de ator são definidas numa interface que é partilhada por ator que implementa a interface e o cliente que obtém um proxy para um ator através da interface do mesma. Como esta interface é utilizada para invocar métodos de atores de forma assíncrona, a cada método de interface tem de ser Task-returning.

Invocações de método e as respetivas respostas basicamente resultam em pedidos de rede no cluster, por isso, os argumentos e os tipos de resultados das tarefas que devolvem tem de ser serializáveis por plataforma. Em particular, têm de ser [contrato de dados serializáveis](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

#### <a name="the-actor-proxy"></a>O proxy de ator
O cliente Reliable Actors API fornece comunicação entre um cliente de ator e uma instância de atores. Para comunicar com um ator, um cliente cria um objeto de proxy de ator que implementa a interface de atores. O cliente interage com o ator invocando métodos no objeto de proxy. O proxy de ator pode ser utilizado para comunicação cliente-para-ator e ator-ator.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Tenha em atenção que as duas peças de informações utilizadas para criar o objeto de proxy de ator são o ID de actor e o nome da aplicação. O ID de actor identifica exclusivamente o ator, enquanto o nome da aplicação identifica o [aplicação de Service Fabric](service-fabric-reliable-actors-platform.md#application-model) onde o ator está implementado.

O `ActorProxy`(c#) / `ActorProxyBase`classe (Java) do lado do cliente efetua a resolução necessária para localizar o ator por ID e a abrir um canal de comunicação com o mesmo. Também tentar para localizar o ator nos casos de falhas de comunicação e as ativações pós-falha. Como resultado, a entrega de mensagens com as seguintes características:

* Entrega de mensagens é melhor esforço.
* Atores poderão receber mensagens duplicadas do mesmo cliente.

### <a name="concurrency"></a>Simultaneidade
O tempo de execução Reliable Actors fornece um modelo de acesso baseado em ative simples para aceder aos métodos de atores. Isto significa que não mais do que um thread pode estar ativo no interior do código de um objeto de ator em qualquer altura. Acesso baseado em ative simplifica bastante a sistemas simultâneos que não necessitam de mecanismos de sincronização para acesso a dados. Isto também significa que os sistemas têm de ser concebidos com considerações especiais sobre a natureza de acesso único thread de cada instância de atores.

* Uma instância de ator único não consegue processar mais do que um pedido de cada vez. Uma instância de ator pode causar um estrangulamento do débito se é esperado para processar pedidos em simultâneo.
* Atores podem criar um impasse em si se existir um pedido de circular entre duas atores enquanto um pedido externo é feito para um de atores em simultâneo. O tempo de execução de ator do tempo em chamadas de atores e automaticamente acionar uma excepção para o autor da chamada para interromper situações de impasse possíveis.

![Comunicação do Reliable Actors][3]

#### <a name="turn-based-access"></a>Acesso baseado em vez
Uma vez consiste na execução de um método de ator em resposta a um pedido de outros clientes ou de atores concluída ou a execução de conclua de uma [temporizador/lembrete](service-fabric-reliable-actors-timers-reminders.md) chamada de retorno. Apesar destes métodos e as chamadas de retorno são assíncronas, o tempo de execução de Atores não intercalação-los. Uma vez tem de ser totalmente concluída antes de vez nova é permitido. Por outras palavras, uma ator método ou temporizador/lembrete chamada de retorno que está atualmente em execução tem de ser totalmente concluída antes de uma nova chamada para um método ou chamada de retorno é permitida. Uma chamada de retorno ou método é considerada como tiver concluído a se a execução foi devolvida do método ou chamada de retorno e a tarefa devolvida pelo método ou chamada de retorno foi concluída. É importante, que realçam que esse concorrência com base em ative é respeitada mesmo através de métodos diferentes, os temporizadores e chamadas de retorno.

O tempo de execução de Atores impõe concorrência com base em ative por adquirir um bloqueio por ator no início de uma vez e libertação do bloqueio no final da ativar. Assim, com base em vez de concorrência é imposta numa base por ator e não através de atores. Métodos de ator e chamadas de retorno de temporizador/lembrete podem ser executado em simultâneo em nome de atores diferentes.

O exemplo a seguir ilustra os conceitos acima. Considere um tipo de ator que implementa os dois métodos assíncronos (diga, *método1* e *método2*), um temporizador e um lembrete. O diagrama abaixo mostra um exemplo de uma linha cronológica para a execução destas métodos e as chamadas de retorno em nome de dois atores (*ActorId1* e *ActorId2*) que pertence a este tipo de atores.

![Simultaneidade de com base em vez de tempo de execução de Atores fiável e acesso][1]

Este diagrama segue estes convenções de:

* Cada linha vertical apresenta o fluxo lógico da execução de um método ou uma chamada de retorno em nome do actor um determinado.
* Os eventos marcado como em cada linha vertical ocorrerem por ordem cronológica, com mais recentes eventos ocorridos abaixo antigos.
* Cores diferentes são utilizadas para linhas cronológicas correspondente atores diferentes.
* Realce é utilizado para indicar a duração para o qual o bloqueio por ator está contido em nome de um método ou chamada de retorno.

Alguns pontos importantes a considerar:

* Enquanto *método1* está a executar em nome de *ActorId2* em resposta ao pedido de cliente *xyz789*, outro pedido de cliente (*abc123*) chega que também requer *método1* para ser executado por *ActorId2*. No entanto, a execução do segundo *método1* não começa até que a execução anterior foi concluída. Da mesma forma, um lembrete registada por *ActorId2* desencadeado enquanto *método1* está a ser executado em resposta ao pedido de cliente *xyz789*. A chamada de retorno lembrete é executada apenas depois de ambas as execuções de *método1* estiverem concluídas. Tudo isto acontece devido a concorrência com base em vez que está a ser imposta para *ActorId2*.
* Da mesma forma, com base em vez de concorrência também é imposta para *ActorId1*, conforme demonstrado pela execução do *método1*, *método2*e a chamada de retorno de temporizador em nome de *ActorId1* decorrer de uma forma de série.
* A execução do *método1* em nome de *ActorId1* sobrepõe-se a execução em nome de *ActorId2*. Isto acontece porque a simultaneidade baseado em ative é imposta apenas dentro de um ator e não através de atores.
* Em algumas das execuções de chamada de retorno/método, o `Task`(c#) / `CompletableFuture`(Java) devolvida pelo depois de concluída chamada de retorno/método depois do método devolve. Em alguns outros recursos, a operação assíncrona já foi concluída dentro do tempo que devolve a método/chamada de retorno. Em ambos os casos, o bloqueio por ator for lançado depois de método/chamada de retorno devolve tanto concluída a operação assíncrona.

#### <a name="reentrancy"></a>Reentrancy
O tempo de execução de Atores permite reentrancy por predefinição. Isto significa que, se um método de ator de *Ator A* chama um método no *Ator B*, que por sua vez, chama outro método no *Ator A*, que método pode ser executada. Isto acontece porque faz parte do mesmo contexto de lógica de cadeia de chamadas. Todas as chamadas de temporizador e lembrete começar a utilizar o contexto de chamada lógico novo. Consulte o [reentrancy Reliable Actors](service-fabric-reliable-actors-reentrancy.md) para obter mais detalhes.

#### <a name="scope-of-concurrency-guarantees"></a>Âmbito de garantias de concorrência
O tempo de execução de Atores fornece estes garantias de concorrência em situações em que controla a invocação de um destes métodos. Por exemplo, fornece estes garantias para as invocações de método que são efetuadas em resposta a um pedido de cliente, bem como para chamadas de retorno de temporizador e lembretes. No entanto, se o código de ator invoca diretamente estes métodos fora dos mecanismos de fornecidos pelo tempo de execução de Atores, em seguida, o tempo de execução não é possível fornecer quaisquer garantias de concorrência. Por exemplo, se o método é invocado no contexto de algumas tarefas que não esteja associado a tarefa devolvida através dos métodos de ator, em seguida, o tempo de execução não é possível fornecer garantias de concorrência. Se o método é invocado a partir de um thread que o ator cria no seu próprio, em seguida, o tempo de execução também não é possível fornecer garantias de concorrência. Por conseguinte, para efetuar operações em segundo plano, atores devem utilizar [temporizadores de ator e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md) que Respeitamos a concorrência com base em ativar.

## <a name="next-steps"></a>Passos seguintes
Introdução ao criar o primeiro serviço Reliable Actors:
   * [Introdução a Reliable Actors no .NET](service-fabric-reliable-actors-get-started.md)
   * [Introdução a Reliable Actors em Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
