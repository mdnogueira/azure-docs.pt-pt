---
title: Que o polimorfismo no framework Reliable Actors | Microsoft Docs
description: "Crie hierarquias de interfaces de .NET e tipos no framework Reliable Actors reutilizar a funcionalidade e as definições da API."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 38a86b25b30420c6f0b3027258fa094529c90278
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Que o polimorfismo no framework Reliable Actors
A estrutura de Reliable Actors permite-lhe criar atores utiliza muitos as mesmas técnicas de que pretende utilizar na estrutura orientado para objetos. Uma dessas técnicas é que o polimorfismo, que permite que os tipos e interfaces para herdar de mais generalizada principais. Herança no framework Reliable Actors geralmente segue o modelo de .NET com algumas restrições adicionais. Em caso de Java/Linux, segue o modelo de Java.

## <a name="interfaces"></a>Interfaces
A estrutura de Reliable Actors requer que defina, pelo menos, uma interface para ser implementado pelo seu tipo de atores. Esta interface é utilizada para gerar uma classe proxy que pode ser utilizada pelos clientes para comunicar com os atores. Interfaces podem herdar de outras interfaces desde que cada interface que é implementado por um tipo de ator e todos os seus principais basicamente derivar IActor(C#) ou Actor(Java). IActor(C#) e Actor(Java) são respetivamente interfaces base definida por plataforma de atores em estruturas .NET e Java. Assim, o exemplo de que o polimorfismo clássico através de formas poderá algo semelhante ao seguinte:

![Hierarquia de interface para os atores de forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipos de
Também pode criar uma hierarquia de tipos de ator, que são derivadas da classe de Ator base que é fornecida pela plataforma. No caso de formas, poderá ter uma base `Shape`(c#) ou `ShapeImpl`tipo (Java):

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Subtypes de `Shape`(c#) ou `ShapeImpl`(Java) pode substituir métodos de base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Tenha em atenção o `ActorService` atributo no tipo de atores. Este atributo indica o framework de Ator fiável do que deve criar automaticamente um serviço para o alojamento de atores deste tipo. Em alguns casos, poderá pretender criar um tipo de base que apenas se destina a funcionalidade de partilha com subtipos e nunca será utilizado para instanciar atores concretos. Nesses casos, deve utilizar o `abstract` palavra-chave para indicar que nunca irá criar um ator com base nesse tipo.

## <a name="next-steps"></a>Passos seguintes
* Consulte [como o framework Reliable Actors tira partido da plataforma do Service Fabric](service-fabric-reliable-actors-platform.md) para fornecer a fiabilidade, a escalabilidade e o estado consistente.
* Saiba mais sobre o [ciclo de vida de ator](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
