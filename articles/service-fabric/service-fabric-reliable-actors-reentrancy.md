---
title: "Reentrancy em com base em ator micro-serviços do Azure | Microsoft Docs"
description: "Introdução ao reentrancy dos recursos de infraestrutura de serviço Reliable Actors"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 00fcccb379bf1ba3875fbaba57a05b00fa228622
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-actors-reentrancy"></a>Reentrancy do Reliable Actors
Tempo de execução Reliable Actors, por predefinição, permite reentrancy baseado no contexto de chamada lógico. Isto permite atores ser reentrantes se estiverem na mesma cadeia de contexto da chamada. Por exemplo, o Ator A envia uma mensagem para Ator B, que envia uma mensagem para c de Atores. Como parte do processamento de mensagens, se Ator C chamadas de Atores A, a mensagem é reentrante, pelo que serão permitida. As mensagens que fazem parte de um contexto de chamada diferente serão bloqueadas no Ator A até terminar o processamento.

Existem duas opções disponíveis para reentrancy ator definido no `ActorReentrancyMode` enum:

* `LogicalCallContext`(comportamento predefinido)
* `Disallowed`-Desativa reentrancy

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Reentrancy podem ser configuradas num `ActorService`do definições durante o registo. A definição aplica-se a todas as instâncias de ator criadas no serviço de atores.

O exemplo seguinte mostra um serviço de atores que define o modo de reentrancy para `ActorReentrancyMode.Disallowed`. Neste caso, se um ator envia uma mensagem reentrante para outro ator, uma exceção do tipo `FabricException` será emitida.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre reentrancy no [documentação de referência da API de Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
