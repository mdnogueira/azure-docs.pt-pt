---
title: "Eventos na baseado em ator micro-serviços do Azure | Microsoft Docs"
description: "Introdução aos eventos para o serviço de recursos de infraestrutura Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 779d39e2910d0de25ee07b8fae3ca7a0e9772776
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="actor-events"></a>Eventos de ator
Os eventos de ator fornecem uma forma para enviar notificações de melhor esforço a partir de ator aos clientes. Eventos de ator foram concebidos para a comunicação de ator-cliente e não devem ser utilizados para comunicação de ator-ator.

Os fragmentos de código seguintes mostram como utilizar eventos de ator na sua aplicação.

Defina uma interface que descreve os eventos de publicado por de atores. Esta interface tem de ser derivada do `IActorEvents` interface. Os argumentos dos métodos têm de ser [contrato de dados serializáveis](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos tem de devolver void, como eventos notificações são uma forma e melhor esforço.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Declare os eventos de publicado por ator na interface de atores.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
No lado do cliente, implementa o processador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

No cliente, criar um proxy para o ator que publica o evento e subscrever os eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Em caso das ativações pós-falha, o ator poderá falhar para um processo diferente ou o nó. O proxy de ator gere as subscrições ativas e automaticamente subscreve-las novamente. Pode controlar o intervalo de nova subscrição através de `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Para anular a subscrição, utilize o `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

No ator, publica os eventos que possam ocorrem. Se existirem subscritores para o evento, o tempo de execução de Atores envia-as a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Passos seguintes
* [Reentrancy ator](service-fabric-reliable-actors-reentrancy.md)
* [Monitorização de desempenho e diagnóstico de ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do c#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de c# .NET Core exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Código de exemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)
