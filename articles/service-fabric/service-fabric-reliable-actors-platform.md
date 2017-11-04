---
title: "Reliable Actors em recursos de infraestrutura de serviço | Microsoft Docs"
description: "Descreve como Reliable Actors são em camadas sobre Reliable Services e utilizar as funcionalidades de plataforma do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 43b3f758fe7017c0ec949ba6e28b76438cf1bc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como os Reliable Actors utilizam a plataforma Service Fabric
Este artigo explica como funcionam os Reliable Actors na plataforma do Azure Service Fabric. Chamado Reliable Actors executadas uma arquitetura que está alojada numa implementação de um serviço fiável com monitorização de estado a *serviço de atores*. O serviço de atores contém todos os componentes necessários para gerir o ciclo de vida e a mensagem de emissão para os atores:

* O tempo de execução de Ator gere o ciclo de vida, recolha de lixo e impõe o acesso single-threaded.
* Um serviço de escuta de comunicação remota de serviço ator aceita as chamadas de acesso remoto para atores e envia-as para um distribuidor para encaminhar para a instância de ator adequado.
* O fornecedor de estado de Ator encapsula num wrapper a fornecedores de estado (por exemplo, o fornecedor de estado coleções fiável) e fornece um adaptador de gestão do Estado de ator.

Estes componentes em conjunto formam a estrutura de Ator fiável.

## <a name="service-layering"></a>Dispor de serviço
Porque o próprio serviço de atores é um serviço fiável, todos os o [modelo de aplicação](service-fabric-application-model.md), ciclo de vida, [empacotamento](service-fabric-package-apps.md), [implementação](service-fabric-deploy-remove-applications.md), atualizar e dimensionar os conceitos das fiável Serviços aplicam-se da mesma forma para serviços de atores.

![Dispor de serviço de atores][1]

O diagrama anterior mostra a relação entre as estruturas de aplicações de Service Fabric e o código de utilizador. Elementos azuis representam a estrutura da aplicação Reliable Services, laranja representa o framework de Ator fiável e verde representa o código de utilizador.

No Reliable Services, o seu serviço herda o `StatefulService` classe. Esta classe é próprio derivado `StatefulServiceBase` (ou `StatelessService` para serviços sem monitorização de estado). Reliable Actors, utiliza o serviço de atores. O serviço de atores é uma implementação diferente de `StatefulServiceBase` classe que implementa o padrão de ator onde executam a sua atores. Porque o próprio serviço de atores é apenas uma implementação de `StatefulServiceBase`, pode escrever o seu próprio serviço que deriva de `ActorService` e implementar funcionalidades de nível de serviço da mesma forma que faria ao herdar `StatefulService`, tais como:

* Cópia de segurança do serviço e de restauro.
* Partilhado funcionalidade para todos os atores, por exemplo, um disjuntor automático.
* Chamadas de procedimento remoto do serviço de atores em si e em cada ator individuais.

> [!NOTE]
> Serviços com monitorização de estado não são atualmente suportados em Java/Linux.

### <a name="using-the-actor-service"></a>Utilizar o serviço de atores
Instâncias de ator ter acesso ao serviço de atores em que estejam a ser executados. Através do serviço de atores, instâncias de ator programaticamente podem obter o contexto de serviço. O contexto de serviço tem o ID de partição, o nome do serviço, o nome da aplicação e outras informações específicas da plataforma do Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


Como Reliable Services, o serviço de atores tem de ser registado com um tipo de serviço no tempo de execução do Service Fabric. Para o serviço de atores para executar as instâncias de ator, o tipo de ator também tem de ser registado com o serviço de atores. O método de registo `ActorRuntime` realiza este trabalho para atores. No caso mais simples, apenas pode registar o tipo de ator e implicitamente será utilizado o serviço de atores com as predefinições:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Em alternativa, pode utilizar um lambda fornecido pelo método de registo para construir o serviço de atores por si. Pode, em seguida, configure o serviço de atores e explicitamente construir as instâncias de ator, onde pode inserir dependências para o seu ator através do respetivo construtor:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>Métodos de serviço de atores
A implementa do serviço de Atores `IActorService` (c#) ou `ActorService` (Java), que por sua vez implementa `IService` (c#) ou `Service` (Java). Esta é a interface utilizada pelo Reliable Services sistema de interação remota, que permite chamadas de procedimento remoto em métodos do serviço. Contém os métodos de nível de serviço que podem ser chamados remotamente através de comunicação remota do serviço.

#### <a name="enumerating-actors"></a>Enumerar actors
O serviço de atores permite que um cliente a enumeração de metadados sobre os atores que aloja o serviço. Porque o serviço de atores é um serviço com estado particionado, enumeração é efetuada por partição. Porque cada partição pode conter muitos atores, enumeração é devolvida como um conjunto de resultados paginados. As páginas são looped através de até que todas as páginas são lidas. O exemplo seguinte mostra como criar uma lista de todos os atores de Active Directory de uma partição de um serviço de atores:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Eliminar actors
O serviço de atores também fornece uma função para a eliminação de atores:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Para obter mais informações sobre como eliminar atores e o respetivo estado, consulte o [documentação de ciclo de vida de ator](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Serviço de atores personalizado
Ao utilizar o lambda de registo de ator, pode registar o seus próprios serviço de atores personalizado que deriva de `ActorService` (c#) e `FabricActorService` (Java). Neste serviço de atores personalizado, pode implementar a sua própria funcionalidade de nível de serviço ao escrever uma classe de serviço que herda `ActorService` (c#) ou `FabricActorService` (Java). Um serviço de atores personalizado herda todas as funcionalidades de tempo de execução de ator do `ActorService` (c#) ou `FabricActorService` (Java) e pode ser utilizado para implementar os suas próprias métodos do serviço.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
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
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>Implementar a cópia de segurança de ator e restauro
 No exemplo seguinte, o serviço de atores personalizado expõe um método para cópias de segurança ator, tirando partido do serviço de escuta de sistema de interação remota já está presente no `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


Neste exemplo, `IMyActorService` é um contrato de sistema de interação remota que implementa `IService` (c#) e `Service` (Java) e, em seguida, é implementado por `MyActorService`. Ao adicionar este contrato de sistema de interação remota, métodos em `IMyActorService` agora também estão disponíveis para um cliente através da criação de um proxy do sistema de interação remota via `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Modelo de aplicação
Serviços de atores são Reliable Services, pelo que o modelo de aplicação é o mesmo. No entanto, as ferramentas de compilação do framework ator geram alguns dos ficheiros de modelo de aplicação por si.

### <a name="service-manifest"></a>Manifesto de serviço
As ferramentas de compilação do framework ator geram automaticamente o conteúdo do ficheiro do seu serviço de atores ServiceManifest.xml. Este ficheiro inclui:

* Tipo de serviço de atores. O nome do tipo é gerado com base no nome do seu ator projeto. Com base no atributo persistência no seu ator, o sinalizador de HasPersistedState seja também definido em conformidade.
* Pacote do código.
* Pacote de configuração.
* Recursos e os pontos finais.

### <a name="application-manifest"></a>manifesto da aplicação
As ferramentas de compilação do framework ator criar automaticamente uma definição de serviço predefinido para o seu serviço de atores. As ferramentas de compilação preencher as propriedades de serviço predefinido:

* Contagem de conjunto de réplicas é determinada pelo atributo persistência no seu ator. Sempre que o atributo de persistência em seu ator é alterado, a contagem de conjunto de réplica na definição de serviço predefinido é reposta em conformidade.
* Esquema de partição e o intervalo são definidos para Uniform Int64 com o intervalo de chave completo Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de recursos de infraestrutura do serviço de atores
Serviços de atores são particionado com monitorização de estado. Cada partição de um serviço de atores contém um conjunto de atores. As partições de serviço são distribuídos automaticamente por através de vários nós no Service Fabric. Instâncias de ator são distribuídas, como resultado.

![Ator criação de partições e de distribuição][5]

Reliable Services podem ser criados com esquemas de partição diferentes e os intervalos de chaves de partição. O serviço de atores utiliza o esquema de partições Int64 com o intervalo de chave Int64 completo para mapear atores para partições.

### <a name="actor-id"></a>ID de actor
Cada ator que é criado no serviço tem um ID exclusivo associado, representado pelo `ActorId` classe. `ActorId`é um valor de ID opaco que pode ser utilizado para distribuição uniform de atores entre as partições de serviço através da geração de IDs aleatórios:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` ser codificado para um Int64. Esta é a razão pela qual o serviço de atores tem de utilizar um esquema de partições Int64 com o intervalo de chave completo Int64. No entanto, os valores de ID personalizados podem ser utilizados para um `ActorID`, incluindo GUIDs/UUIDs não, as cadeias e Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Quando estiver a utilizar cadeias e GUIDs/UUIDs não, os valores são codificados para um Int64. No entanto, quando que está a explicitamente a fornecer Int64 para um `ActorId`, o Int64 mapeará diretamente a uma partição sem obter hash. Pode utilizar esta técnica para controlo de partição de atores são colocados em.

## <a name="actor-using-remoting-v2-stack"></a>Ator utilizando a pilha do sistema de interação remota V2
Com o pacote nuget 2.8, os utilizadores podem agora utilizar a pilha de comunicação remota V2, que é mais performant e fornece as funcionalidades, como serialização personalizada. V2 de sistema de interação remota não é retrocompatível com pilha de comunicação remota existente (estamos a ligar agora-lo como a pilha do sistema de interação remota V1).

As alterações seguintes são necessários para utilizar a pilha do sistema de interação remota V2.
 1. Adicione o seguinte atributo de assemblagem em Interfaces de Atores.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Compilação e atualizar ActorService Ator cliente e projetos para começar a utilizar a pilha de V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Ator serviço atualizar para o sistema de interação remota V2 pilha sem afetar a disponibilidade do serviço.
Esta alteração será uma atualização do passo 2. Siga os passos na mesma sequência conforme indicado.

1.  Adicione o seguinte atributo de assemblagem em Interfaces de Atores. Este atributo irá iniciar os serviços de dois escuta para ActorService, V1 (existente) e o serviço de escuta V2. Atualize ActorService com esta alteração.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Atualize ActorClients depois de concluir a atualização acima.
Este passo certifica-se de que proxy de Ator está a utilizar o sistema de interação remota V2 pilha.

3. Este passo é opcional. Altere o atributo acima para remover o serviço de escuta V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Passos seguintes
* [Gestão do Estado de ator](service-fabric-reliable-actors-state-management.md)
* [Coleção de ciclo de vida e libertação da memória de ator](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API de atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
