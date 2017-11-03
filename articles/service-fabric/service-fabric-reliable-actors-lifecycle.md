---
title: "Descrição geral do ciclo de vida de com base em ator micro-serviços do Azure | Microsoft Docs"
description: "Explica Ator fiável de recursos de infraestrutura de serviço do ciclo de vida, recolha de lixo e como eliminar manualmente atores e o respetivo estado"
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: d49afd9e5cfe80ddc2d919c76eaa0cb168280c15
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo de vida de ator, recolha de lixo automática e manual delete
Um ator está ativado na primeira vez que é efetuada uma chamada para qualquer um dos respetivos métodos. Um ator está desativada (libertação da memória recolhidos pelo tempo de execução de Atores) se não for utilizado para um período de tempo configurável. Um ator e o estado também podem ser eliminadas manualmente em qualquer altura.

## <a name="actor-activation"></a>Ativação de ator
Quando um ator está ativado, ocorre o seguinte:

* Quando vem de uma chamada para um ator e já não estiver ativo, é criado um novo ator.
* Estado do ator está carregado se é manter o estado.
* O `OnActivateAsync` (c#) ou `onActivateAsync` método (Java) (o que pode ser substituído na implementação de ator) é chamado.
* O ator agora é considerado ativo.

## <a name="actor-deactivation"></a>Desativação de ator
Quando um ator está desativada, ocorre o seguinte:

* Quando um ator não é utilizado para um determinado período de tempo, esta é removida da tabela de Atores Active Directory.
* O `OnDeactivateAsync` (c#) ou `onDeactivateAsync` método (Java) (o que pode ser substituído na implementação de ator) é chamado. Isto limpa todos os temporizadores para os atores. Operações de ator, como estado de alterações não devem ser chamadas a partir deste método.

> [!TIP]
> O tempo de execução de Atores de recursos de infraestrutura emite algumas [eventos relacionados com a ativação de ator e desativação](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Estes são úteis em diagnóstico e monitorização do desempenho.
>
>

### <a name="actor-garbage-collection"></a>Recolha de lixo de ator
Quando um ator está desativada, são lançadas as referências para o objeto de ator e pode ser libertados normalmente pelo common language runtime (CLR) ou o recoletor de lixo de máquina virtual (JVM) de java. Recolha de lixo limpa apenas o objeto de ator; faz **não** remover estado armazenado no Gestor de estado do ator. Da próxima vez que o ator está ativado, é criado um novo objeto de ator e o estado do utilizador é restaurado.

O que a conta como "a ser utilizada" para fins de Desativação e a recolha de lixo?

* Receber uma chamada
* `IRemindable.ReceiveReminderAsync`método que está a ser invocado (aplicável apenas se o ator utiliza lembretes)

> [!NOTE]
> Se o ator utiliza temporizadores e a chamada de retorno de temporizador é invocada, faz **não** contagem como "a ser utilizada".
>
>

Antes de iremos entrar em detalhes de Desativação, é importante definir os seguintes termos:

* *Analisar o intervalo*. Este é o intervalo a que o tempo de execução de Atores verifica a respetiva tabela de Atores Active Directory de atores que podem ser desativados e libertação da memória. O valor predefinido é de 1 minuto.
* *Tempo limite de inatividade*. Esta é a quantidade de tempo que um ator tem de permanecer não utilizados (inativo) antes que pode ser desativado e libertação da memória. O valor predefinido é de 60 minutos.

Normalmente, não é necessário alterar estas predefinições. No entanto, se necessário, estes intervalos podem ser alterados através de `ActorServiceSettings` ao registar o [serviço de Atores](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Para cada ator Active Directory, o tempo de execução de ator mantém um registo da quantidade de tempo que foi inativo (ou seja, não utilizado). O tempo de execução de ator verifica cada um de atores cada `ScanIntervalInSeconds` para ver se podem ser recolhidas de libertação da memória e recolhe-se de que está inativo há `IdleTimeoutInSeconds`.

Sempre que é utilizado um ator, o tempo de inatividade é reposto a 0. Após esta, o ator pode ser libertados apenas se novamente permanece inativo para `IdleTimeoutInSeconds`. Recuperar o que é considerada uma ator foram utilizadas se um método de interface de ator ou uma chamada de retorno do lembrete de ator for executada. É um ator **não** considera foram utilizados se for executada a sua chamada de retorno do temporizador.

O diagrama seguinte mostra o ciclo de vida de um único ator para ilustrar estes conceitos.

![Exemplo de tempo de inatividade][1]

O exemplo mostra o impacto de chamadas de método de ator, lembretes e temporizadores na duração deste ator. Os seguintes pontos sobre o exemplo são mencionar:

* ScanInterval e IdleTimeout estão definidos com 5 e 10, respetivamente. (Unidades importa aqui, uma vez que o nosso objetivo é apenas para ilustrar o conceito.)
* A análise de atores a serem libertados ocorre ao T = 0, 5, 10, 15, 20, 25, tal como definido pelo intervalo de análise de 5.
* Desencadeado um temporizador periódico em T = 4, 8, 12, 16, 20, 24, e executa a sua chamada de retorno. Não afeta o tempo de inatividade do ator.
* Uma chamada de método de ator em T = 7 repõe o tempo de inatividade para 0 e atrasa a recolha de lixo do ator.
* Uma chamada de retorno do lembrete de ator executada às T = 14 e atrasa ainda mais a recolha de lixo do ator.
* Durante a análise de recolha de lixo em T = 25, tempo de inatividade do ator finally excede o tempo limite de inatividade 10 e o ator é memória libertada.

Um ator nunca serão libertados enquanto está a executar um dos respectivos métodos, independentemente do quanto tempo é gasto ao executar este método. Conforme mencionado anteriormente, a execução de métodos de interface de ator e chamadas de retorno lembrete impede a recolha de lixo ao repor o tempo de inatividade do ator como 0. A execução de chamadas de retorno do temporizador não repor o tempo de inatividade para 0. No entanto, a recolha de lixo de atores é deferida até que a chamada de retorno de temporizador concluiu a execução.

## <a name="deleting-actors-and-their-state"></a>A eliminação dos atores e o respetivo estado
Recolha de lixo de atores desativados limpa apenas o objeto de ator, mas não remove os dados armazenados no Gestor de estado de um ator. Quando um ator voltar a ativar, dos dados é novamente disponibilizados ao mesmo através do Gestor de estado. Em casos onde atores armazenar dados no Gestor de estado e estão desativados, mas nunca novamente ativados, poderá ser necessário limpar os seus dados.

O [serviço de Atores](service-fabric-reliable-actors-platform.md) fornece uma função para a eliminação dos atores de um autor da chamada remota:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Eliminar um ator tem os seguintes efeitos dependendo se pretende ou não está atualmente ativa o ator:

* **Ator Active Directory**
  * Ator é removido da lista de atores Active Directory e está desativada.
  * O estado é eliminado permanentemente.
* **Ator inativo**
  * O estado é eliminado permanentemente.

Tenha em atenção que um ator não é possível chamar eliminar em si próprio a partir de um dos respectivos métodos de ator porque não é possível eliminar o ator ao executar dentro de um contexto de chamada de ator em que o tempo de execução tem de obter um bloqueio em torno da chamada de ator para impor o acesso single-threaded.

## <a name="next-steps"></a>Passos seguintes
* [Os temporizadores de ator e lembretes](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrancy ator](service-fabric-reliable-actors-reentrancy.md)
* [Monitorização de desempenho e diagnóstico de ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do c#](https://github.com/Azure/servicefabric-samples)
* [Código de exemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
