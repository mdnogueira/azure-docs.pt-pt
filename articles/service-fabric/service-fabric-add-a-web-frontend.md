---
title: "Criar um front-end de publicação na web ' para a sua aplicação de Service Fabric do Azure com o ASP.NET Core | Microsoft Docs"
description: "Expor a aplicação de Service Fabric para a web utilizando um projeto em ASP.NET Core e comunicações interserviços através de comunicação remota do serviço."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Criar um serviço front-end web para a sua aplicação com o ASP.NET Core
Por predefinição, os serviços do Azure Service Fabric não fornecem uma interface pública para o web. Ao exporem a funcionalidade da sua aplicação para clientes HTTP, tem de criar um projeto web para atuar como um ponto de entrada e, em seguida, comunicar a partir daí aos seus serviços individuais.

Neste tutorial, vamos escolher onde iremos parou no [criar a sua primeira aplicação no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) tutorial e adicionar um serviço web do ASP.NET Core à frente do serviço de monitorização de estado do contador. Se ainda não o fez, deve voltar atrás e seguir este tutorial primeiro.

## <a name="set-up-your-environment-for-aspnet-core"></a>Configurar o ambiente para o ASP.NET Core

É necessário o Visual Studio 2017 para seguir este tutorial. Qualquer edição será efetuado. 

 - [Instalar Visual Studio 2017](https://www.visualstudio.com/)

Para desenvolver aplicações do ASP.NET Core Service Fabric, deve ter as seguintes cargas de trabalho instaladas:
 - **Desenvolvimento do Azure** (em *Web & nuvem*)
 - **Desenvolvimento do ASP.NET e web** (em *Web & nuvem*)
 - **Desenvolvimento de plataformas cruzadas .NET core** (em *Toolsets outras*)

>[!Note] 
>As ferramentas do .NET Core para o Visual Studio 2015 já não estão a ser atualizadas, no entanto, se ainda estiver a utilizar o Visual Studio 2015, tem de ter [.NET Core VS 2015 ferramentas Preview 2](https://www.microsoft.com/net/download/core) instalado.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Adicionar um serviço de núcleo de ASP.NET à sua aplicação
ASP.NET Core é uma estrutura de desenvolvimento simples, de plataforma web que pode utilizar para criar a IU da web do moderna e web APIs. 

Vamos adicionar um projeto API Web do ASP.NET a nossa aplicação existente.

1. No Explorador de soluções, faça duplo clique **serviços** na aplicação do projeto e escolha **adicionar > novo serviço de recursos de infraestrutura de serviço**.
   
    ![Adicionar um novo serviço para uma aplicação existente][vs-add-new-service]
2. No **criar um serviço** página, escolha **ASP.NET Core** e atribua um nome.
   
    ![Escolher o serviço web ASP.NET na caixa de diálogo novo serviço][vs-new-service-dialog]

3. A página seguinte fornece um conjunto de ASP.NET Core modelos de projeto. Tenha em atenção que estas são as escolhas mesmas que verá se criou um projeto em ASP.NET Core fora de uma aplicação de Service Fabric, com uma pequena quantidade de códigos adicionais para registar o serviço com o tempo de execução do Service Fabric. Para este tutorial, escolha **Web API**. No entanto, pode aplicar os mesmos conceitos para criar uma aplicação web completa.
   
    ![Escolher o tipo de projeto ASP.NET][vs-new-aspnet-project-dialog]
   
    Assim que for criado o seu projeto Web API, deve ter dois serviços na sua aplicação. À medida que continue a criar a sua aplicação, pode adicionar mais serviços exatamente da mesma forma. Cada pode ser independente com versão e atualizado.

## <a name="run-the-application"></a>Executar a aplicação
Para obter uma noção do tiver feito, vamos implementar a nova aplicação e observe o comportamento predefinido que fornece o modelo de API Web do ASP.NET Core.

1. Prima F5 no Visual Studio para depurar a aplicação.
2. Quando a implementação estiver concluída, o Visual Studio inicia um browser para a raiz do serviço de API Web do ASP.NET. O modelo de API Web do ASP.NET Core não fornece comportamento predefinido para a raiz, por isso, deve ver um erro 404 no browser.
3. Adicionar `/api/values` para a localização no browser. Esta invoca o `Get` método no ValuesController no modelo de Web API. Devolve a resposta de predefinido que é fornecida pelo modelo – uma matriz JSON que contém duas cadeias de:
   
    ![Valores predefinidos devolvidos a partir do modelo de API Web do ASP.NET Core][browser-aspnet-template-values]
   
    No final do tutorial, esta página irá mostrar o valor do contador mais recente do nosso serviço com estado em vez das cadeias de predefinição.

## <a name="connect-the-services"></a>Ligar os serviços
O Service Fabric fornece flexibilidade concluída na como comunicar com serviços fiáveis. Dentro de uma única aplicação, poderá ter serviços que estão acessíveis através de TCP, outros serviços que estão acessíveis através de uma API de REST de HTTP e ainda outros serviços que estão acessíveis através de web sockets. Para fundo sobre as opções disponíveis e fala envolvidas, consulte [comunicar com serviços](service-fabric-connect-and-communicate-with-services.md). Neste tutorial, utilizamos [comunicação remota de serviço do Service Fabric](service-fabric-reliable-services-communication-remoting.md), fornecido no SDK.

A abordagem de comunicação remota do serviço (modelada chamadas de procedimento remoto ou RPCs), definir uma interface para agir como o contrato público para o serviço. Em seguida, utilizar essa interface para gerar uma classe de proxy para interagir com o serviço.

### <a name="create-the-remoting-interface"></a>Criar a interface de gestão remota
Vamos começar por criar a interface para agir como o contrato entre o serviço de monitorização de estado e outros serviços, neste caso, o ASP.NET Core web projeto. Esta interface tem de ser partilhada por todos os serviços que utilizá-lo para efetuar chamadas RPC, pelo que iremos criá-lo no seu próprio projeto da biblioteca de classe.

1. No Explorador de soluções, faça duplo clique a sua solução e selecione **adicionar** > **novo projeto**.

2. Escolha o **Visual c#** entrada no painel de navegação esquerdo e, em seguida, selecione o **biblioteca de classes** modelo. Certifique-se de que a versão do .NET Framework estiver definida **4.5.2**.
   
    ![Criar um projeto de interface para o seu serviço de monitorização de estado][vs-add-class-library-project]

3. Instalar o **Microsoft.ServiceFabric.Services.Remoting** pacote NuGet. Procurar **Microsoft.ServiceFabric.Services.Remoting** do NuGet do Gestor de pacote e instalá-la para todos os projetos na solução de utilizam a comunicação remota do serviço, incluindo:
   - O projeto da biblioteca de classe que contém a interface de serviço
   - O projeto de serviço de monitorização de estado
   - O projeto de serviço web do ASP.NET Core
   
    ![Adicionar o pacote NuGet de serviços][vs-services-nuget-package]

4. A biblioteca de classe, criar uma interface com um único método `GetCountAsync`, e expandir a interface de `Microsoft.ServiceFabric.Services.Remoting.IService`. A interface de comunicação remota tem de derivar desta interface para indicar que é uma interface de comunicação remota do serviço.
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Implementa a interface no seu serviço de monitorização de estado
Agora que definiu a interface, é necessário implementá-las no serviço de monitorização de estado.

1. No seu serviço de monitorização de estado, adicione uma referência para o projeto da biblioteca de classe que contém a interface.
   
    ![Adicionar uma referência para o projeto da biblioteca de classe de serviço com monitorização de estado][vs-add-class-library-reference]
2. Localizar a classe que herda de `StatefulService`, tais como `MyStatefulService`, e expandi-lo para implementar o `ICounter` interface.
   
    ```c#
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Implementar agora o único método que está definido a `ICounter` interface, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Expor o serviço de monitorização de estado utilizando um serviço de escuta de comunicação remota do serviço
Com o `ICounter` interface implementada, o passo final consiste em abrir o canal de comunicação remota do serviço. Para os serviços com monitorização de estado, o Service Fabric fornece um método substituível denominado `CreateServiceReplicaListeners`. Com este método, pode especificar um ou mais escuta de comunicação, consoante o tipo de comunicação que pretende ativar para o seu serviço.

> [!NOTE]
> O método equivalente para abrir um canal de comunicação para serviços sem monitorização de estado é chamado `CreateServiceInstanceListeners`.

Neste caso, iremos substituir a existente `CreateServiceReplicaListeners` método e forneça uma instância de `ServiceRemotingListener`, que cria um ponto final RPC que é possível chamar EndRead de clientes através de `ServiceProxy`.  

O `CreateServiceRemotingListener` método de extensão no `IService` interface permite-lhe criar facilmente uma `ServiceRemotingListener` com todas as definições predefinidas. Para utilizar este método de extensão, certifique-se de que tem o `Microsoft.ServiceFabric.Services.Remoting.Runtime` espaço de nomes importado. 

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Utilizar a classe de ServiceProxy para interagir com o serviço
O nosso serviço com monitorização de estado está agora pronto para receber o tráfego de outros serviços através de RPC. Portanto, tudo o que resta é adicionar o código para comunicar com o mesmo do serviço web ASP.NET.

1. No seu projeto ASP.NET, adicione uma referência para a biblioteca de classe que contém o `ICounter` interface.

2. Anteriormente adicionados a **Microsoft.ServiceFabric.Services.Remoting** pacote NuGet para o projeto ASP.NET. Este pacote fornece o `ServiceProxy` classe que utilizar para efetuar a RPC chamadas para o serviço de monitorização de estado. Certifique-se de que este pacote NuGet está instalado no projeto de serviço web do ASP.NET Core.

4. No **controladores** pasta, abra o `ValuesController` classe. Tenha em atenção que o `Get` método atualmente apenas devolve uma matriz de cadeia hard-coded "value1" e "value2 –" que corresponda ao que vimos anteriormente no browser. Substitua esta implementação com o seguinte código:
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    A primeira linha de código é a chave. Para criar o proxy de ICounter para o serviço com estado, terá de fornecer dois tipos de informações: um ID de partição e o nome do serviço.
   
    Pode utilizar a criação de partições para serviços com monitorização de estado de dimensionamento ao dividi respetivo estado para registos de diferentes, com base numa chave por si, tal como um ID de cliente ou código postal. Na nossa aplicação trivial, o serviço de monitorização de estado tem apenas uma partição, para que a chave não é relevante. Irá causar qualquer tecla que fornece a mesma partição. Para saber mais sobre a criação de partições de serviços, consulte o artigo [como partição de serviço de recursos de infraestrutura Reliable Services](service-fabric-concepts-partitioning.md).
   
    O nome do serviço é um URI de recursos de infraestrutura do formulário: /&lt;application_name&gt;/&lt;service_name&gt;.
   
    Com duas estas informações, o Service Fabric pode identificar exclusivamente a máquina que pedidos devem ser enviados para. O `ServiceProxy` classe também perfeitamente processa caso a máquina que aloja a partição de serviço com estado falhar e outra máquina tem de ser promovida para tirar o seu lugar. Esta abstração torna a escrever o código de cliente para lidar com outros serviços significativamente mais simples.
   
    Assim que tivermos o proxy, iremos simplesmente invocar o `GetCountAsync` método e devolve o resultado.

5. Prima F5 novamente para executar a aplicação modificada. Como anteriormente, o Visual Studio inicia automaticamente o browser para a raiz do projeto web. Adicione o caminho "api/valores" e deverá ver o valor do contador atual devolvido.
   
    ![O valor do contador de monitorização de estado apresentado no browser][browser-aspnet-counter-value]
   
    Atualize o browser periodicamente para ver o valor do contador de atualização.

## <a name="kestrel-and-weblistener"></a>Kestrel e WebListener

O servidor de web ASP.NET Core predefinido, conhecido como Kestrel, é [não suportada atualmente para processar o tráfego de internet direta](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel). Como resultado, o modelo de serviço sem monitorização de estado do ASP.NET Core para o Service Fabric utiliza [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) por predefinição. 

Para saber mais sobre Kestrel e WebListener nos serviços do Service Fabric, consulte [ASP.NET Core no Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="connecting-to-a-reliable-actor-service"></a>Ligar a um serviço de Atores fiável
Este tutorial centra-se adicionar um web front-end que comunicou com um serviço com monitorização de estado. No entanto, pode seguir um modelo muito semelhante para falar com atores. Quando cria um projeto de Ator fiável, o Visual Studio gera automaticamente um projeto de interface para si. Pode utilizar essa interface para gerar um proxy de ator no projeto web para comunicar com o ator. O canal de comunicação é fornecido automaticamente. Para que precisa de fazer tudo o que é equivalente ao estabelecer uma `ServiceRemotingListener` como fez para o serviço de monitorização de estado neste tutorial.

## <a name="how-web-services-work-on-your-local-cluster"></a>Como funcionam os serviços web no seu cluster local
Em geral, pode implementar exatamente a mesma aplicação de Service Fabric para um cluster com várias máquinas que implementou no seu cluster local e ter a certeza de elevada disponibilidade que funciona como esperado. Isto acontece porque o seu cluster local é simplesmente uma configuração de cinco nós que é fechada para um único computador.

Quando se trata de serviços web, no entanto, há um nuance chave. Quando o cluster se encontra por trás de um balanceador de carga, como no Azure, tem de garantir que os serviços web são implementados em cada máquina desde o Balanceador de carga round-robins simplesmente o tráfego entre as máquinas. Pode fazê-lo definindo a `InstanceCount` para o valor especial de "-1" do serviço.

Por outro lado, quando executar um serviço web localmente, tem de garantir que apenas uma instância de serviço está em execução. Caso contrário, depare com conflitos de vários processos que são está a escutar a porta e o caminho do mesmo. Como resultado, a contagem de instâncias de serviço web deve ser definida como "1" para implementações locais.

Para saber como configurar valores diferentes para o ambiente diferente, consulte [gerir parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Passos seguintes
Agora que tem um início de web terminar o conjunto de cópias de segurança para a sua aplicação com o ASP.NET Core, saiba mais sobre [ASP.NET Core no Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) para compreender como funciona o ASP.NET Core com o Service Fabric aprofundada.

Em seguida, [saber mais sobre a comunicar com serviços](service-fabric-connect-and-communicate-with-services.md) no geral para obter um concluída imagem como serviço de comunicação funciona no Service Fabric.

Depois de ter uma boa compreensão sobre como funciona a comunicação de serviço, [criar um cluster no Azure e implementar a sua aplicação para a nuvem](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
