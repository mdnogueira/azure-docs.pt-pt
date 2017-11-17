---
title: "Os temporizadores de Atores fiáveis e lembretes | Microsoft Docs"
description: "Introdução ao temporizadores e lembretes dos Reliable Actors do serviço de recursos de infraestrutura."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f61743a7925c26767118dcb2d18799c26f880156
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="actor-timers-and-reminders"></a>Os temporizadores de ator e lembretes
Atores podem agendar trabalho periódico em si próprios registando temporizadores ou lembretes. Este artigo mostra como utilizar os temporizadores e lembretes e explica as diferenças entre os mesmos.

## <a name="actor-timers"></a>Temporizadores de ator
Os temporizadores de ator fornecem um simple wrapper em torno de um temporizador .NET ou Java para se certificar de que os métodos de chamada de retorno respeitem a concorrência com base em ative garante que o tempo de execução de Atores fornece.

Podem utilizar atores a `RegisterTimer`(c#) ou `registerTimer`(Java) e `UnregisterTimer`(c#) ou `unregisterTimer`métodos (Java) na sua classe base para registar e anular o registo os temporizadores. O exemplo abaixo mostra a utilização de temporizador APIs. As APIs são muito semelhantes para o temporizador de .NET ou o temporizador de Java. Neste exemplo, quando o temporizador é devido, o tempo de execução de Atores irá chamar o `MoveObject`(c#) ou `moveObject`método (Java). O método é garantido Respeitamos a concorrência com base em ativar. Isto significa que não existem outros métodos de ator ou chamadas de retorno de temporizador/lembrete será em curso até que esta chamada de retorno de conclusão da execução.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

O próximo período do temporizador é iniciado após a chamada de retorno de conclusão da execução. Isto implica que o temporizador é parou durante a chamada de retorno está a executar e é iniciada quando termina a chamada de retorno.

O tempo de execução de Atores guarda as alterações efetuadas ao Gestor de estado do ator quando termina a chamada de retorno. Se ocorrer um erro ao guardar o estado, esse objeto de ator vai ser desativado e uma nova instância irá ser ativada.

Todos os temporizadores são interrompidos quando o ator está desativado como parte da recolha de lixo. Chamadas de retorno não temporizador são invocadas depois disso. Além disso, o tempo de execução de Atores não mantém qualquer informação sobre os temporizadores que estavam em execução antes de Desativação. Trata-se até o ator para registar os temporizadores de que necessita quando é reativado-la no futuro. Para obter mais informações, consulte a secção sobre [recolha de lixo de ator](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Lembretes de ator
Lembretes são um mecanismo para acionar as chamadas de retorno persistentes num ator em especificado vezes. As suas funcionalidades é semelhante à temporizadores. Mas, ao contrário temporizadores, lembretes são acionados em todas as circunstâncias até o ator explicitamente anula o registo-los ou o ator explicitamente é eliminado. Especificamente, lembretes são acionados em deactivations ator e as ativações pós-falha porque o tempo de execução de Atores persiste informações sobre lembretes de ator utilizando o fornecedor de estado de ator. Tenha em atenção que a fiabilidade de lembretes está associada ao garantias de fiabilidade Estado fornecidas pelo fornecedor de estado de ator. Isto significa que para os atores cuja persistência do estado é definida como nenhum, os lembretes não serão acionados após uma ativação pós-falha. 

Para registar um lembrete, um ator chama o `RegisterReminderAsync` método fornecido na classe base, conforme mostrado no exemplo seguinte:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

Neste exemplo, `"Pay cell phone bill"` é o nome de lembretes. Esta é uma cadeia com o ator utiliza para identificar exclusivamente um lembrete. `BitConverter.GetBytes(amountInDollars)`(C#) é o contexto que estão associado com o lembrete. -Será transmitida novamente para o ator como um argumento para a chamada de retorno lembrete, ou seja, `IRemindable.ReceiveReminderAsync`(c#) ou `Remindable.receiveReminderAsync`(Java).

Atores que utilizam lembretes tem de implementar o `IRemindable` interface, conforme mostrado no exemplo abaixo.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Quando um lembrete é acionado, o tempo de execução Reliable Actors serão invocados a `ReceiveReminderAsync`(c#) ou `receiveReminderAsync`método de Ator (Java). Um ator pode registar lembretes vários e o `ReceiveReminderAsync`(c#) ou `receiveReminderAsync`método (Java) é invocado quando qualquer um desses lembretes é acionada. O ator pode utilizar o nome de lembrete que é transmitido para o `ReceiveReminderAsync`(c#) ou `receiveReminderAsync`método (Java) para descobrir qual lembrete foi acionado.

Os runtime guarda o actor de Atores de estado quando o `ReceiveReminderAsync`(c#) ou `receiveReminderAsync`conclusão de chamada (Java). Se ocorrer um erro ao guardar o estado, esse objeto de ator vai ser desativado e uma nova instância irá ser ativada.

Ao anular o registo um lembrete, um ator chama o `UnregisterReminderAsync`(c#) ou `unregisterReminderAsync`método (Java), conforme ilustrado nos exemplos abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Conforme mostrado acima, o `UnregisterReminderAsync`(c#) ou `unregisterReminderAsync`método (Java) aceita um `IActorReminder`(c#) ou `ActorReminder`interface (Java). O suporta de classe base ator um `GetReminder`(c#) ou `getReminder`método (Java) que pode ser utilizado para obter o `IActorReminder`(c#) ou `ActorReminder`interface (Java) mediante a transmissão no nome do lembrete. Este é conveniente porque o ator têm de manter o `IActorReminder`(c#) ou `ActorReminder`interface (Java) que foi devolvida a `RegisterReminder`(c#) ou `registerReminder`chamada de método (Java).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre eventos de Ator fiável e reentrancy:
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrancy ator](service-fabric-reliable-actors-reentrancy.md)
