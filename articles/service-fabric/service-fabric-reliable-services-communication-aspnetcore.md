---
title: "Serviço de comunicação com o ASP.NET Core | Microsoft Docs"
description: "Saiba como utilizar o ASP.NET Core nos serviços de fiável sem monitorização de estado e com monitorização de estado."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: a98e9ad891fcfaf02ca7df5d10d5b310445c9d34
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core nos serviços de fiável de recursos de infraestrutura de serviço

ASP.NET Core é uma arquitetura de open source e plataforma novo para a criação de baseado na nuvem ligada à Internet aplicações modernas, tais como as web apps, aplicações de IoT e back-ends móveis. 

Este artigo é um guia aprofundado para alojar o ASP.NET Core dos serviços no Service Fabric Reliable Services utilizando o **Microsoft.ServiceFabric.AspNetCore.** * conjunto de pacotes NuGet.

Para um tutorial introdutórias em ASP.NET Core no Service Fabric e instruções sobre como começar a configurar o ambiente de desenvolvimento, consulte [construir uma web front-end para a sua aplicação com o ASP.NET Core](service-fabric-add-a-web-frontend.md).

O resto deste artigo assume que já estiver familiarizado com o ASP.NET Core. Se não, recomenda-se ler de [Noções básicas sobre o ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Enquanto as aplicações de ASP.NET Core podem ser executado no .NET Core ou o .NET Framework completa, serviços do Service Fabric atualmente só podem ser executada no .NET Framework completa. Isto significa quando criar um serviço de recursos de infraestrutura de serviço de núcleo de ASP.NET, ainda devem visar o .NET Framework completa.

ASP.NET Core podem ser utilizada duas formas diferentes no Service Fabric:
 - **Alojado como um executável de convidado**. Isto é principalmente utilizado para executar aplicações ASP.NET Core existentes em recursos de infraestrutura de serviço sem alterações de código.
 - **Executar dentro de um serviço fiável**. Isto permite uma melhor integração com o tempo de execução do Service Fabric e permite que o ASP.NET Core com monitorização de estado dos serviços.

O resto deste artigo explica como utilizar o ASP.NET Core dentro de um serviço fiável com os componentes de integração de núcleo de ASP.NET que são enviados juntamente com o SDK de Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Alojamento de serviço do Service Fabric

No Service Fabric, um ou mais instâncias de e/ou as réplicas do seu serviço executam *processo anfitrião do serviço*, um ficheiro executável que executa o código do serviço. Que, como um autor de serviço, possui o processo de anfitrião do serviço e o Service Fabric ativa e monitoriza-lo por si.

ASP.NET tradicional (até MVC 5) é fortemente conjugados ao IIS através do System.Web.dll. ASP.NET Core fornece uma separação entre o servidor web e a sua aplicação web. Isto permite que as aplicações web para ser portátil entre servidores web diferente e também permite que os servidores web ser *autoalojado*, que significa que pode iniciar um servidor web no seu próprio processo, por oposição a um processo que é propriedade de web dedicado software de servidor, tais como o IIS. 

Para combinar um serviço do Service Fabric e ASP.NET, como um executável de convidado ou num serviço fiável, tem de conseguir iniciar ASP.NET dentro do processo de anfitrião do serviço. ASP.NET Core alojamento personalizada permite-lhe fazê-lo.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>ASP.NET Core num serviço fiável de alojamento
Normalmente, aplicações de ASP.NET Core Self-hospedadas criam um WebHost no ponto de entrada de uma aplicação, tais como o `static void Main()` método `Program.cs`. Neste caso, o ciclo de vida do WebHost está vinculado ao ciclo de vida do processo.

![Alojamento ASP.NET Core num processo][0]

No entanto, o ponto de entrada de aplicação não é o local certo para criar um WebHost num serviço fiável, porque o ponto de entrada de aplicação só é utilizado para registar um tipo de serviço com o tempo de execução do Service Fabric, para que pode criar instâncias desse tipo de serviço. O WebHost deve ser criado num serviço fiável o próprio. Dentro do processo de anfitrião do serviço, instâncias de serviço e/ou de réplicas podem percorrer vários ciclos de vida. 

Uma instância de serviço fiável é representada pela classe de serviço efectuar a derivação de `StatelessService` ou `StatefulService`. Faz a pilha de comunicações para um serviço um `ICommunicationListener` implementação na sua classe de serviço. O `Microsoft.ServiceFabric.Services.AspNetCore.*` pacotes NuGet contenham implementações de `ICommunicationListener` que iniciar e gerir o WebHost de núcleo de ASP.NET para Kestrel ou HttpSys num serviço fiável.

![ASP.NET Core num serviço fiável de alojamento][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners de núcleo de ASP.NET
O `ICommunicationListener` implementações para Kestrel e HttpSys no `Microsoft.ServiceFabric.Services.AspNetCore.*` pacotes NuGet possuem padrões de utilização semelhante, mas efetuar ligeiramente diferentes ações específicas para cada servidor web. 

Ambos os serviços de escuta de comunicação de fornecer um construtor que assuma argumentos seguintes:
 - **`ServiceContext serviceContext`**: O `ServiceContext` objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`**: o nome de um `Endpoint` configuração no ServiceManifest.xml. Isto é principalmente onde os serviços de escuta de dois comunicação diferem: HttpSys **requer** um `Endpoint` configuração, enquanto Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: um lambda que implementar na qual criar e retorno um `IWebHost`. Isto permite-lhe configurar `IWebHost` a forma como faria normalmente de uma aplicação ASP.NET Core. O lambda fornece um URL que é gerado para, consoante a integração do Service Fabric opções utilizar e o `Endpoint` configuração que fornecer. Que URL, em seguida, pode ser modificado ou utilizado como-está a iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O `Microsoft.ServiceFabric.Services.AspNetCore` pacote NuGet inclui o `UseServiceFabricIntegration` método de extensão no `IWebHostBuilder` que adiciona o middleware de suporte para o Service Fabric. Este middleware configura o Kestrel ou HttpSys `ICommunicationListener` para registar um URL de serviço exclusivo com o serviço Service Fabric Naming e, em seguida, valida os pedidos de cliente para garantir que os clientes estão a ligar ao serviço direita. Isto é necessário num ambiente anfitrião partilhados, tais como o Service Fabric, em que várias aplicações web podem ser executados no mesmo físico ou máquina virtual, mas não utilizem nomes de anfitrião exclusivo, para impedir que os clientes por engano ligar ao serviço errado. Este cenário é descrito mais detalhadamente na secção seguinte.

### <a name="a-case-of-mistaken-identity"></a>Um cenário de identidade mistaken
Réplicas de serviço, independentemente do protocolo, escutarem uma combinação de IP:port exclusivo. Depois de uma réplica de serviço tenha iniciado a escuta num ponto final IP:port, relatórios esse endereço de ponto final para o serviço Service Fabric Naming onde podem ser detetado por clientes ou outros serviços. Se os serviços utilizam portas atribuídas dinamicamente a aplicação, uma réplica de serviço por coincidência pode utilizar o mesmo ponto final do IP:port de outro serviço que foi anteriormente no mesmo físico ou máquina virtual. Isto pode fazer com que um cliente mistakely para ligar ao serviço errado. Isto pode acontecer se ocorrer a seguinte sequência de eventos:

 1. Serviço A escuta 10.0.0.1:30000 através de HTTP. 
 2. Resolve um serviço de cliente e obtém 10.0.0.1:30000 de endereço
 3. O serviço A move num nó diferente.
 4. Serviço B é colocada 10.0.0.1 e por coincidência utiliza a mesma porta 30000.
 5. Cliente tenta ligar a um serviço com 10.0.0.1:30000 endereço em cache.
 6. Cliente está agora ligado com êxito ao serviço B não compreender está ligado ao serviço errado.

Isto pode causar erros em alturas aleatórias que podem ser difícil diagnosticar. 

### <a name="using-unique-service-urls"></a>Utilizar URLs do serviço exclusivo
Para evitar esta situação, serviços podem publicar um ponto final para o serviço de atribuição de nomes com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante os pedidos de cliente. Esta é uma ação cooperative entre os serviços num ambiente fidedigno não hostile inquilinos. Isto não fornece autenticação do serviço segura num ambiente hostile inquilino.

Num ambiente fidedigno, o middleware que é adicionado pelo `UseServiceFabricIntegration` método acrescenta automaticamente um identificador exclusivo para o endereço que é publicado para o serviço de atribuição de nomes e valida esse identificador em cada pedido. Se o identificador não corresponderem, o middleware devolve imediatamente uma resposta de HTTP 410 removido.

Os serviços que utilizam uma porta atribuído dinamicamente, deverá certificar-utilizar este middleware.

Serviços que utilizam uma porta exclusiva fixa não têm este problema num ambiente cooperative. Uma porta exclusiva fixa é normalmente utilizada para serviços com acesso exterior que necessitar de uma porta para as aplicações de cliente ligar ao conhecido. Por exemplo, a maioria das aplicações de web para a Internet utilizará a porta 80 ou 443 para ligações de browser web. Neste caso, o identificador exclusivo não deve estar ativado.

O diagrama seguinte mostra o fluxo de pedidos com o middleware ativada:

![Integração de núcleo de ASP.NET de recursos de infraestrutura de serviço][2]

Kestrel e HttpSys `ICommunicationListener` implementações utilizam este mecanismo exatamente da mesma forma. Embora HttpSys internamente capaz de distinguir o pedidos com base nos caminhos do URL exclusivos utilizando subjacentes *http.sys* funcionalidade, o que é a funcionalidade de partilha de porta *não* utilizado pelo HttpSys `ICommunicationListener` implementação porque o que resultará num 503 de HTTP e HTTP 404 códigos de estado de erro o cenário descrito anteriormente. Por sua vez torna muito difícil para os clientes determinar o objetivo do erro, como HTTP 503 e HTTP 404 são já frequentemente utilizadas para indicar outros erros. Assim, Kestrel tanto HttpSys `ICommunicationListener` implementações padronizar no middleware fornecida pelo `UseServiceFabricIntegration` método de extensão para que os clientes só terá de executar um ponto final de serviço resolver novamente a ação de respostas de HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys nos serviços de fiáveis
HttpSys pode ser utilizado num serviço fiável ao importar o **Microsoft.ServiceFabric.AspNetCore.HttpSys** pacote NuGet. Este pacote contém `HttpSysCommunicationListener`, uma implementação de `ICommunicationListener`, que lhe permite criar um WebHost de núcleo de ASP.NET dentro de um serviço fiável com HttpSys que o servidor web.

HttpSys está incorporado no [API de servidor Windows HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Esta opção utiliza o *http.sys* controladores de kernel utilizado pelo IIS para processar pedidos de HTTP e encaminhá-las para processos de aplicações web em execução. Isto permite que vários processos no mesmo físico ou máquina virtual para alojar web Apps na mesma porta, resolver esta ambiguidade por um caminho de URL exclusivo ou o nome do anfitrião. Estas funcionalidades são úteis no Service Fabric para alojar vários sites no mesmo cluster.

O diagrama seguinte ilustra como HttpSys utiliza o *http.sys* controladores de kernel no Windows para a partilha de porta:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys num serviço sem monitorização de estado
Para utilizar `HttpSys` num serviço sem monitorização de estado, substituir o `CreateServiceInstanceListeners` método e devolver um `HttpSysCommunicationListener` instância:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HttpSys num serviço de monitorização de estado

`HttpSysCommunicationListener`Atualmente não foi concebida para utilização nos serviços de monitorização de estado devido a complicações com subjacentes *http.sys* funcionalidade de partilha de porta. Para obter mais informações, consulte a secção seguinte na alocação de porta dinâmica com HttpSys. Para os serviços com monitorização de estado, Kestrel é o servidor web recomendada.

### <a name="endpoint-configuration"></a>Configuração de ponto final

Um `Endpoint` é necessária configuração para servidores web que utilizam a API do servidor Windows HTTP, incluindo HttpSys. Servidores Web que utilizam a API de servidor de HTTP do Windows tem de reservar primeiro os URL com *http.sys* (isto é normalmente conseguido com a [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ferramenta). Esta ação requer privilégios elevados que não tenham os seus serviços por predefinição. As opções de "http" ou "https" para o `Protocol` propriedade o `Endpoint` configuração no *ServiceManifest.xml* são utilizados especificamente ao instruir o tempo de execução do Service Fabric para registar um URL com  *http.sys* em seu nome, utilizando o [ *universal forte* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefixo de URL.

Por exemplo, para reservar `http://+:80` para um serviço, a seguinte configuração deve ser utilizada em ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

E o nome do ponto final tem de ser transmitido para o `HttpSysCommunicationListener` construtor:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Utilizar HttpSys com uma porta estática
Para utilizar uma porta estática com HttpSys, forneça o número da porta no `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utilizar HttpSys com uma porta dinâmica
Para utilizar uma porta dinamicamente atribuída com HttpSys, omita o `Port` propriedade no `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Tenha em atenção que uma porta dinâmica atribuída por um `Endpoint` configuração fornece apenas uma porta *por processo anfitrião*. O modelo de alojamento de Service Fabric atual permite que várias instâncias de serviço e/ou réplicas alojadas no mesmo processo, que significa que cada um deles irão partilhar a mesma porta quando atribuído através de `Endpoint` configuração. Várias instâncias de HttpSys podem partilhar uma porta com subjacentes *http.sys* porta partilha funcionalidade, mas que não é suportada pelo `HttpSysCommunicationListener` devido a complicações introduz para pedidos de cliente. Para utilização de porta dinâmica, Kestrel é o servidor web recomendada.

## <a name="kestrel-in-reliable-services"></a>Kestrel nos serviços de fiáveis
Kestrel pode ser utilizado num serviço fiável ao importar o **Microsoft.ServiceFabric.AspNetCore.Kestrel** pacote NuGet. Este pacote contém `KestrelCommunicationListener`, uma implementação de `ICommunicationListener`, que lhe permite criar um WebHost de núcleo de ASP.NET dentro de um serviço fiável utilizando Kestrel que o servidor web.

Kestrel é que um servidor de plataforma web de ASP.NET Core com base no libuv, uma biblioteca de e/s assíncrona de várias plataformas. Ao contrário HttpSys, Kestrel não utilizar um ponto final centralizada gestor como *http.sys*. E, ao contrário HttpSys, Kestrel não suporta a partilha de portas entre vários processos. Cada instância de Kestrel tem de utilizar uma porta exclusiva.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel num serviço sem monitorização de estado
Para utilizar `Kestrel` num serviço sem monitorização de estado, substituir o `CreateServiceInstanceListeners` método e devolver um `KestrelCommunicationListener` instância:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel num serviço de monitorização de estado
Para utilizar `Kestrel` num serviço com monitorização de estado, substituir o `CreateServiceReplicaListeners` método e devolver um `KestrelCommunicationListener` instância:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Neste exemplo, uma instância singleton do `IReliableStateManager` é fornecido para o contentor de injeção de dependência de WebHost. Isto não é estritamente necessário, mas permite-lhe utilizar `IReliableStateManager` e fiável de coleções em seu métodos de ação de controlador MVC.

Tenha em atenção que um `Endpoint` nome da configuração é **não** fornecido para `KestrelCommunicationListener` num serviço de monitorização de estado. Isto é explicado mais detalhadamente na secção seguinte.

### <a name="endpoint-configuration"></a>Configuração de ponto final
Um `Endpoint` não é necessária uma configuração para utilizar Kestrel. 

Kestrel é um servidor web autónoma simples; ao contrário HttpSys (ou HttpListener), não necessita de um `Endpoint` configuração no *ServiceManifest.xml* porque que não é necessário o registo de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Utilizar Kestrel com uma porta estática
Pode ser configurada uma porta estática a `Endpoint` configuração da ServiceManifest.xml para utilização com Kestrel. Embora não seja estritamente necessário, fornece dois possíveis vantagens:
 1. Se a porta não pertencer ao intervalo de portas de aplicação, é aberto através da firewall do SO pelo Service Fabric.
 2. O URL fornecido que `KestrelCommunicationListener` irá utilizar esta porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` é configurado, o nome tem de ser transmitido para o `KestrelCommunicationListener` construtor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se um `Endpoint` configuração não é utilizada, omita o nome no `KestrelCommunicationListener` construtor. Neste caso, será utilizada a uma porta dinâmica. Consulte a secção seguinte para obter mais informações.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Utilizar Kestrel com uma porta dinâmica
Kestrel não é possível utilizar a atribuição automática de porta do `Endpoint` configuração no ServiceManifest.xml, porque a atribuição automática de porta de uma `Endpoint` configuração atribui uma porta exclusiva por *alojar processo*, e um processo único anfitrião pode conter várias instâncias de Kestrel. Uma vez que Kestrel não suporta a partilha de portas, este não funciona como cada instância de Kestrel tem de ser aberta numa porta exclusiva.

Para utilizar atribuição de porta dinâmica com Kestrel, basta omitir o `Endpoint` configuração no ServiceManifest.xml completamente e não são transmitidas um nome de ponto final de `KestrelCommunicationListener` construtor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nesta configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de portas de aplicação.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta secção descreve os seguintes cenários e fornece a combinação recomendada de servidor web, configuração da porta, as opções de integração do Service Fabric e diversas definições para alcançar um serviço de funcionar corretamente:
 - Externamente expostos serviço sem monitorização de estado do ASP.NET Core
 - Serviços sem monitorização de estado de ASP.NET Core só de interno
 - Serviço com estado de ASP.NET Core só interno

Um **externamente expostos** serviço é aquele que expõe um ponto final acessível a partir de fora do cluster, normalmente, através de um balanceador de carga.

Um **meramente internos** service é uma cujo ponto final só é acessível a partir do cluster.

> [!NOTE]
> Pontos finais de serviço com estado, geralmente, não devem ser expostos à Internet. Clusters que estejam atrás de balanceadores de carga que não estiverem cientes da resolução de serviço do Service Fabric, tais como o Balanceador de carga do Azure, será possível expor os serviços com monitorização de estado porque o Balanceador de carga não será possível localizar e encaminhar o tráfego para o adequado réplica de monitorização de estado do serviço. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externamente expostos serviços sem monitorização de estado do ASP.NET Core
Kestrel é o servidor de web recomendada para os serviços front-end que expõem pontos finais HTTP externos, o acesso à Internet. No Windows, HttpSys podem ser utilizadas para fornecer a capacidade de partilha de porta que lhe permite alojar vários serviços web no mesmo conjunto de nós com a mesma porta, diferenciada por nome de anfitrião ou o caminho, sem depender de um proxy de front-end ou o gateway para fornecer o encaminhamento de HTTP.
 
Quando exposto à Internet, um serviço sem monitorização de estado deve utilizar um ponto final bem conhecido e estável que está acessível através de um balanceador de carga. Este é o URL que irá fornecer aos utilizadores da sua aplicação. Recomenda-se a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | kestrel | Kestrel é o servidor web preferencial, dado que é suportada em Windows e Linux. |
| Configuração da porta | estático | Deve ser configurada uma porta estática bem conhecida no `Endpoints` configuração de ServiceManifest.xml, por exemplo 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhuma | O `ServiceFabricIntegrationOptions.None` opção deve ser utilizada quando configurar o middleware de integração do Service Fabric para que o serviço não tenta validar pedidos recebidos para um identificador exclusivo. Os utilizadores externos da sua aplicação não saberá que as informações de identificação exclusivas utilizadas pelo middleware. |
| Contagem de instâncias | -1 | Em casos de utilização típica, a contagem de instâncias definição deve ser definida como "-1" para que uma instância está disponível em todos os nós que recebem o tráfego do Balanceador de carga. |

Se vários serviços externamente expostos partilham o mesmo conjunto de nós, HttpSys pode ser utilizado com um caminho de URL único, porém está estável. Isto pode ser efetuado ao modificar o URL fornecido ao configurar IWebHost. Tenha em conta de que isto se aplica a HttpSys apenas.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviços de ASP.NET Core sem monitorização de estado só de interno
Os serviços sem monitorização de estado que só são chamados de dentro do cluster devem utilizar URLs exclusivos e atribuídas dinamicamente portas para garantir a cooperação entre vários serviços. Recomenda-se a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | kestrel | Embora HttpSys podem ser utilizadas para serviços sem monitorização de estado internos, Kestrel é o servidor recomendado para permitir que várias instâncias do serviço partilhar um anfitrião.  |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço com monitorização de estado podem partilhar um processo de anfitrião ou o sistema operativo anfitrião e, por conseguinte, terá de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, esta definição impede que o problema de identidade mistaken descrito anteriormente. |
| InstanceCount | qualquer | A contagem de instâncias definição pode ser definida como qualquer valor necessária operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Serviços de ASP.NET Core com monitorização de estado só de interno
Serviços com monitorização de estado que só são chamados de dentro do cluster devem utilizar portas dinamicamente atribuídas para garantir a cooperação entre vários serviços. Recomenda-se a seguinte configuração:

|  |  | **Notas** |
| --- | --- | --- |
| Servidor Web | kestrel | O `HttpSysCommunicationListener` não foi concebido para utilização pelos serviços com monitorização de estado em que as réplicas partilham um processo de anfitrião. |
| Configuração da porta | dinamicamente atribuído | Várias réplicas de um serviço com monitorização de estado podem partilhar um processo de anfitrião ou o sistema operativo anfitrião e, por conseguinte, terá de portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição de porta dinâmica, esta definição impede que o problema de identidade mistaken descrito anteriormente. |

## <a name="next-steps"></a>Passos seguintes
[Depurar a aplicação de Service Fabric através do Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
