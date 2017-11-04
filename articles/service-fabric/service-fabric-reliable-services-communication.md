---
title: "Descrição geral de comunicação do Reliable Services | Microsoft Docs"
description: "Descrição geral do modelo de comunicação de Reliable Services, incluindo os serviços de escuta de abertura nos serviços, resolver pontos finais e comunicação entre os serviços."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/07/2017
ms.author: vturecek
ms.openlocfilehash: b418904f50b772c12bfcdbb95beb9312c8b9fb00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como utilizar a APIs de comunicação Reliable Services
Azure Service Fabric, como uma plataforma é completamente agnóstico relativamente sobre a comunicação entre os serviços. Todas as pilhas e protocolos são aceitáveis, do UDP para HTTP. Está a funcionar para o Programador de serviço para escolher a forma como os serviços devem comunicar. A estrutura da aplicação Reliable Services fornece pilhas de comunicação incorporada, bem como as APIs que pode utilizar para criar os componentes de comunicação personalizado.

## <a name="set-up-service-communication"></a>Configurar a comunicação de serviço
A API de serviços fiável utiliza uma interface simples para comunicação de serviço. Para abrir um ponto final para o seu serviço, basta implemente esta interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Em seguida, pode adicionar a implementação de serviço de escuta de comunicação ao devolver uma substituição de método de classe com base no serviço.

Para serviços sem monitorização de estado:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Para os serviços com monitorização de estado:

> [!NOTE]
> Serviços fiáveis com monitorização de estado não são suportados ainda em Java.
>
>

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Em ambos os casos, tem de devolver uma coleção de serviços de escuta. Isto permite que o serviço escute em múltiplos pontos finais, potencialmente com protocolos diferentes, utilizando vários serviços de escuta. Por exemplo, pode ter um serviço de escuta HTTP e um serviço de escuta de WebSocket separado. Cada serviço de escuta obtém um nome e a coleção resultante de *name: endereço* pares são representados como um objeto JSON quando um cliente solicita os serviço de escuta endereços para uma instância de serviço ou uma partição.

Um serviço sem monitorização de estado, a substituição devolve uma coleção de ServiceInstanceListeners. A `ServiceInstanceListener` contém uma função para criar um `ICommunicationListener(C#) / CommunicationListener(Java)` e concede-lhe um nome. Para os serviços com monitorização de estado, a substituição devolve uma coleção de ServiceReplicaListeners. Isto é ligeiramente diferente do respetivo homólogo sem monitorização de estado, porque um `ServiceReplicaListener` tem uma opção para abrir um `ICommunicationListener` nas réplicas secundárias. Não só pode utilizar vários serviços de escuta de comunicação num serviço, mas também pode especificar que os serviços de escuta que aceitam pedidos em réplicas secundárias e aqueles escutarem apenas em réplicas primárias.

Por exemplo, pode ter um ServiceRemotingListener que aceita chamadas RPC apenas em réplicas primárias e uma escuta segundo, personalizada que demora leitura pedidos nas réplicas secundárias através de HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Ao criar vários serviços de escuta para um serviço, cada serviço de escuta **tem** ser fornecido um nome exclusivo.
>
>

Por fim, descrevem os pontos finais que são necessários para o serviço no [manifesto serviço](service-fabric-application-model.md) na secção em pontos finais.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

O serviço de escuta de comunicação pode aceder os recursos de ponto final atribuídos ao mesmo a partir do `CodePackageActivationContext` no `ServiceContext`. O serviço de escuta, em seguida, pode começar a escutar para pedidos quando for aberta.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Recursos de ponto final são comuns para o pacote de todo o serviço e são atribuídos pelo Service Fabric quando o pacote de serviço é ativado. Várias réplicas de serviço alojadas no mesmo ServiceHost podem partilhar a mesma porta. Isto significa que o serviço de escuta de comunicação deve suportar a partilha de portas. É a forma recomendada de fazê-lo para o serviço de escuta de comunicação utilizar a partição ID e o ID de réplica/instância ao gerar o endereço de escuta.
>
>

### <a name="service-address-registration"></a>Registo de endereço do serviço
Um serviço de sistema chamado o *Naming Service* é executado em clusters de Service Fabric. O serviço de atribuição de nomes é uma entidade de registo para serviços e os respetivos endereços que cada instância ou a réplica do serviço está a escutar. Quando o `OpenAsync(C#) / openAsync(Java)` método de um `ICommunicationListener(C#) / CommunicationListener(Java)` estiver concluída, o retorno valor obtém registado no serviço de nomenclatura. Este valor de retorno que seja publicado no serviço de atribuição de nomes é uma cadeia cujo valor pode ser qualquer coisa de todo. Este valor de cadeia é clientes veem quando pedem para um endereço para o serviço a partir do serviço de nomenclatura.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

O Service Fabric fornece APIs que permitem que os clientes e outros serviços e, em seguida, peça para este endereço de por nome de serviço. Isto é importante porque o endereço do serviço não é estático. Os serviços são movidos em torno do cluster para fins de balanceamento e disponibilidade de recursos. Este é o mecanismo que permitem aos clientes resolver o endereço para um serviço de escuta.

> [!NOTE]
> Para uma passagem completa de como escrever um serviço de escuta de comunicação, consulte [serviços de API Web do Service Fabric com OWIN automática de alojamento](service-fabric-reliable-services-communication-webapi.md) para c#, enquanto que para Java pode escrever a sua própria implementação do servidor de HTTP, consulte o artigo EchoServer aplicação exemplo ao https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Ao comunicar com um serviço
A API de serviços fiável fornece as bibliotecas seguintes para clientes que comunicam com os serviços de escrita.

### <a name="service-endpoint-resolution"></a>Resolução de ponto final de serviço
É o primeiro passo para comunicação com um serviço para resolver um endereço de ponto final da partição ou instância de serviço que pretende comunicar com. O `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` classe de utilitário é um primitivo básico que ajuda os clientes a determinar o ponto final de um serviço em tempo de execução. Na terminologia de Service Fabric, o processo de determinar o ponto final de um serviço é referido como o *resolução de ponto final de serviço*.

Para ligar aos serviços dentro de um cluster, ServicePartitionResolver pode ser criada através das predefinições. Esta é a utilização recomendada para a maioria das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Para ligar a serviços em cluster diferente, um ServicePartitionResolver pode ser criado com um conjunto de pontos finais de gateway do cluster. Tenha em atenção que os pontos finais de gateway são apenas diferentes pontos finais para ligar ao mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Em alternativa, `ServicePartitionResolver` pode ser especificado uma função para a criação de um `FabricClient` internamente a utilizar:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient`é o objeto que é utilizado para comunicar com o cluster do Service Fabric para várias operações de gestão no cluster. Isto é útil quando pretende mais controlo sobre como uma resolução de partição de serviço interage com o cluster. `FabricClient`efetua a colocação em cache internamente e é geralmente dispendioso criar, pelo que é importante reutilizar `FabricClient` instâncias quanto possíveis.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Um método de resolver, em seguida, é utilizado para obter o endereço de um serviço ou uma partição de serviço para os serviços particionadas.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Um endereço de serviço pode ser resolvido utilizando facilmente um ServicePartitionResolver, mas mais trabalho é necessário para garantir que o endereço resolvido pode ser utilizado corretamente. O cliente tem de detetar se a tentativa de ligação falhou devido a um erro transitório e pode ser repetida (por exemplo, serviço movido ou está temporariamente indisponível), ou um erro permanente (por exemplo, serviço foi eliminado ou o recurso pedido já não existe). Instâncias de serviço ou réplicas podem mover à volta de nó para o nó em qualquer altura para por vários motivos. O endereço de serviço resolvido através de ServicePartitionResolver poderá estar obsoleto que o código de cliente tenta ligar-se do tempo. Nesse caso novamente o cliente tem de voltar a resolver o endereço. Fornecer anterior `ResolvedServicePartition` indica que a resolução precisa tentar novamente em vez de apenas obter um endereço em cache.

Normalmente, o código de cliente necessário não funciona com o ServicePartitionResolver diretamente. É criado e transmitido ao fábricas de cliente de comunicação na API serviços fiável. As fábricas de utilizam o resolvedor internamente para gerar um objeto de cliente que pode ser utilizado para comunicar com os serviços.

### <a name="communication-clients-and-factories"></a>Os clientes de comunicação e fábricas de
A biblioteca de fábrica de comunicação implementa um padrão de repetição de processamento de falhas normal que facilita a repetir as ligações a pontos finais de serviço resolvido. A biblioteca de fábrica fornece o mecanismo de repetição enquanto está a fornecer os processadores de erro.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`Define a interface base implementada por uma fábrica do cliente de comunicação que produza os clientes que podem comunicar com um serviço do Service Fabric. A implementação do CommunicationClientFactory depende da pilha de comunicação utilizada pelo serviço do Service Fabric onde pretende comunicar o cliente. A API de serviços fiável fornece um `CommunicationClientFactoryBase<TCommunicationClient>`. Isto fornece uma implementação da CommunicationClientFactory interface base e executa tarefas que são comuns a todas as pilhas de comunicação. (Estas tarefas incluem a utilização de um ServicePartitionResolver para determinar o ponto final do serviço). Os clientes, normalmente, implementam classe abstrata CommunicationClientFactoryBase para processar lógica específica para a pilha de comunicação.

O cliente de comunicação apenas recebe um endereço e utiliza-o para ligar a um serviço. O cliente pode utilizar qualquer protocolo que pretende.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

A fábrica do cliente é principalmente responsável pela criação de clientes de comunicação. Para clientes que não mantêm uma ligação persistente, por exemplo, um cliente HTTP, a fábrica só tem de criar e devolver o cliente. Outros protocolos que mantêm uma ligação persistente, por exemplo, alguns protocolos binários, também devem ser validados pela fábrica para determinar se a ligação tem de ser recriada.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Por fim, um processador de excepções é responsável por determinar a ação a tomar quando ocorre uma exceção. Exceções são categorizadas em **repetição** e **sem repetição**.

* **Sem repetição** exceções simplesmente obterem novamente iniciadas novamente para o autor da chamada.
* **repetição** exceções mais são categorizadas em **transitório** e **não transitório**.
  * **Transitório** exceções são aqueles que pode ser repetida simplesmente sem voltar a resolver o endereço de ponto final de serviço. Estes irão incluir problemas de rede transitórios ou respostas de erro do serviço além das que indicam que o endereço de ponto final de serviço não existe.
  * **Não transitório** exceções são aqueles que requerem o endereço de ponto final de serviço seja novamente resolvido. Estas incluem as exceções que indicam o que ponto final do serviço não está acessível, que indica que o serviço foi movido para um nó diferente.

O `TryHandleException` faz uma decisão sobre uma determinado exceção. Se este **não souber** que decisões a tomar sobre uma exceção, deverá devolver **falso**. Se este **saber** que é decisão, deve definir o resultado em conformidade e devolver **verdadeiro**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {        

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Passar todos os em conjunto
Com um `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, e `IExceptionHandler(C#) / ExceptionHandler(Java)` construído em torno de um protocolo de comunicação, um `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` encapsula-lo num wrapper tudo em conjunto e fornece o ciclo de resolução de endereço de partição de serviço e de processamento de falhas em torno estes componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Passos seguintes
* Ver um exemplo de comunicação HTTP entre serviços num [c# projeto de exemplo no GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount) ou [projeto de exemplo de Java no GitHUb](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Services/WatchDog).
* [Chamadas de procedimento remoto com o sistema de interação remota Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que utiliza OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação de WCF utilizando Reliable Services](service-fabric-reliable-services-communication-wcf.md)
