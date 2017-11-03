---
title: "Gestão de estado de Reliable Actors | Microsoft Docs"
description: Descreve como Estado Reliable Actors gerido, persistente e replicado para elevada disponibilidade.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: aca8cf2b94e8b746a5cac6af021c7221a29b7345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-actors-state-management"></a>Gestão de estado do Reliable Actors
Reliable Actors são single-threaded objetos que podem encapsular a lógica e estado. Porque atores executam Reliable Services, estes podem manter o estado da forma fiável com o mesma persistência e mecanismos de replicação que utiliza Reliable Services. Desta forma, atores não percam o respetivo estado após falhas, após a reativação após a recolha de lixo ou quando são movidos entre nós num cluster devido ao balanceamento de recurso ou atualizações à volta.

## <a name="state-persistence-and-replication"></a>Estado persistência e replicação
Todos os Reliable Actors são considerados *com monitorização de estado* porque cada instância de ator está mapeado para um ID exclusivo. Isto significa que repetido chamadas para o mesmo ID de actor são encaminhadas para a mesma instância de atores. Num sistema sem monitorização de estado, por outro lado, as chamadas de cliente não são garantidas ser encaminhados para o mesmo servidor sempre. Por este motivo, serviços de atores são sempre serviços com monitorização de estado.

Apesar de atores são considerados com monitorização de estado, não significa que eles devem armazenar o estado de forma fiável. Atores podem escolher o nível de persistência do Estado e a replicação com base nos seus dados requisitos de armazenamento:

* **Persistente estado**: estado é persistente para disco e são replicadas para 3 ou mais réplicas. Esta é a opção de armazenamento de estado mais durável, onde pode manter o estado através de falha completa do cluster.
* **Estado volátil**: estado é replicado para 3 ou mais réplicas e apenas é mantido na memória. Isto oferece resiliência contra falhas de nó e de ator e durante a atualização e balanceamento de recurso. No entanto, o estado não é continuado no disco. Por isso, se todas as réplicas são perdidas em simultâneo, o estado é perdido bem.
* **Sem estado persistente**: Estado não é replicado ou escrito no disco. Este nível destina-se a atores simplesmente não precisam de manter o estado de forma fiável.

Cada nível de persistência é simplesmente uma diferente *fornecedor de estado* e *replicação* configuração do seu serviço. Se pretende ou não é escrito Estado disco depende do fornecedor de estado--o componente de um serviço fiável que armazena o estado. Depende da replicação em réplicas quantos um serviço é implementado com. Tal como acontece com Reliable Services, o fornecedor de estado e a contagem de réplicas podem facilmente ser definidas manualmente. A estrutura de ator fornece um atributo que, quando utilizada numa ator, seleciona automaticamente um fornecedor de estado predefinido e gera automaticamente as definições da contagem de réplicas para alcançar uma destas três definições de persistência. O atributo StatePersistence não é herdado pelos classe derivada, cada tipo de Ator tem de fornecer o nível de StatePersistence.

### <a name="persisted-state"></a>Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Esta definição utiliza um fornecedor de estado que armazena dados no disco e define automaticamente a contagem de réplicas do serviço para 3.

### <a name="volatile-state"></a>Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Esta definição utiliza um fornecedor de estado na memória-só e define o número de réplica para 3.

### <a name="no-persisted-state"></a>Sem estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Esta definição utiliza um fornecedor de estado na memória-só e define o número de réplica para 1.

### <a name="defaults-and-generated-settings"></a>As predefinições e definições geradas
Quando estiver a utilizar o `StatePersistence` atributo, um fornecedor de estado é selecionado automaticamente para si no tempo de execução quando inicia o serviço de atores. A contagem de réplicas, no entanto, está definida no momento da compilação pelas ferramentas de compilação de atores de Visual Studio. As ferramentas de compilação geram automaticamente um *serviço predefinido* para o serviço de atores no ApplicationManifest.xml. Os parâmetros são criados para **réplica mín. Defina o tamanho** e **réplica de destino definir tamanho**.

Pode alterar manualmente estes parâmetros. Mas sempre que o `StatePersistence` atributo é alterado, os parâmetros estão definidos para os valores de tamanho de conjunto de réplica predefinidos para selecionado `StatePersistence` atributo, substituir quaisquer valores anteriores. Por outras palavras, os valores que definiu na ServiceManifest.xml são *apenas* substituída no momento da compilação quando altera o `StatePersistence` valor de atributo.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Gestor de estado
Todas as instâncias de ator tem o seu próprio gestor de estado: pares chave/valor uma estrutura semelhante de dicionário de dados que armazena de forma fiável. O Gestor de estado é um wrapper em torno de um fornecedor de estado. Pode utilizá-lo para armazenar dados, independentemente da que é utilizada a definição de persistência. Não fornece quaisquer garantias que um serviço de atores em execução pode ser alterado de uma definição de estado (na memória-só) volátil para uma definição de estado persistente através de uma atualização sem interrupção, mantendo os dados. No entanto, é possível alterar a contagem de réplicas para um serviço em execução.

Chaves de Gestor de estado têm de ser cadeias. Os valores são genéricos e pode ser qualquer tipo, incluindo tipos personalizados. Valores armazenados no Gestor de estado tem de ser serializável de contrato de dados porque que podem ser transmitidas através da rede para outros nós durante a replicação e podem ser gravados no disco, consoante a definição de persistência de estado de um ator.

O Gestor de estado expõe métodos de dicionário comuns para gerir o estado, semelhante às foi encontrado no dicionário fiável.

### <a name="accessing-state"></a>Aceder ao Estado
Estado pode ser acedido através do Gestor de estado por chave. Métodos de Gestor de estado são todos os assíncronos porque poderá necessitam de e/s de disco quando atores tem persistente estado. Após a primeira acesso, os objetos de estado são colocadas em cache na memória. Repita a objetos de acesso de operações de acesso diretamente a partir da memória e devolver de forma síncrona sem incorrer em e/s de disco ou assíncrona contexto-mudar sobrecarga. Um objeto de estado é removido da cache de nos seguintes casos:

* Um método de ator emite uma exceção não processada depois obtém um objecto do Gestor de estado.
* Um ator for reativado, após a ser desativada ou após a falha.
* O fornecedor de estado de páginas de estado para o disco. Este comportamento depende a implementação do fornecedor de estado. O fornecedor de estado predefinido para o `Persisted` definição tem este comportamento.

Pode obter o estado utilizando um padrão *obter* operação que emite `KeyNotFoundException`(c#) ou `NoSuchElementException`(Java) se não existir uma entrada para a chave:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Também pode obter o estado utilizando um *TryGet* método que inicia se não existir uma entrada para uma chave:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

### <a name="saving-state"></a>A guardar Estado
Os métodos de obtenção do Gestor de estado devolvem uma referência a um objeto na memória local. Este objeto no memória local relativos a modificar não irá causar que sejam guardados de forma durável. Quando um objeto é obtido a partir do Gestor de Estados e modificado, tem de ser voltar para o Gestor de estado sejam guardados de forma durável.

Pode inserir o estado utilizando um incondicional *definir*, que é o equivalente a `dictionary["key"] = value` sintaxe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Pode adicionar o estado utilizando um *adicionar* método. Este método emite `InvalidOperationException`(c#) ou `IllegalStateException`(Java) ao tentar adicionar uma chave que já existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Também pode adicionar o estado utilizando um *TryAdd* método. Este método não inicia quando tentar adicionar uma chave que já existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

No final de um método de ator, o Gestor de estado guarda automaticamente quaisquer valores que foram adicionados ou modificados por uma operação de inserção ou atualização. "Guardar" pode incluir a persistência para o disco e a replicação, consoante as definições utilizadas. Os valores que não foram modificados não são persistentes ou replicados. Se não existem valores foram modificados, a guardar operação faz nada. Se guardar falhar, o estado modificado é ignorado e o estado original é recarregar.

Pode também guardar estado manualmente ao chamar o `SaveStateAsync` método na base de ator:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

### <a name="removing-state"></a>Remoção de estado
Pode remover estado permanentemente a partir do Gestor de estado de um ator ao chamar o *remover* método. Este método emite `KeyNotFoundException`(c#) ou `NoSuchElementException`(Java) ao tentar remover uma chave que não existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Pode também remover estado permanentemente utilizando o *TryRemove* método. Este método não throw ao tentar remover uma chave que não existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Estado que é armazenado em Reliable Actors deve ser serializado antes do respetivo escrito no disco e replicados para elevada disponibilidade. Saiba mais sobre [serialização do tipo de Ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre [monitorização de desempenho e diagnóstico de Ator](service-fabric-reliable-actors-diagnostics.md).
