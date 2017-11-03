---
title: "Criar a primeira aplicação de Service Fabric em c# | Microsoft Docs"
description: "Introdução à criação de uma aplicação do Microsoft Azure Service Fabric com serviços sem monitorização de estado e com monitorização de estado."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: vturecek
ms.openlocfilehash: 813021d6239ae3cf79bb84b78f77e39c9e0783f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Uma aplicação do Azure Service Fabric contém um ou mais serviços com o seu código. Este guia mostra como criar aplicações de Service Fabric sem monitorização de estado e com monitorização de estado com [Reliable Services](service-fabric-reliable-services-introduction.md).  Este vídeo do Microsoft Virtual Academy também mostra-lhe como criar um serviço fiável sem monitorização de estado:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=s39AO76yC_7206218965">  
<img src="./media/service-fabric-reliable-services-quick-start/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a Reliable Services, só tem de compreender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a implementação de serviço. Está definido pela classe de escrita que expande `StatelessService` e quaisquer outras código ou dependências utilizadas nas mesmas, juntamente com um nome e um número de versão.
* **Com o nome de instância de serviço**: ao executar o serviço, criar instâncias nomeadas do seu tipo de serviço, muito como criar instâncias do objecto de um tipo de classe. Uma instância de serviço tem um nome no formato de um URI, utilizando o "recursos de infraestrutura: /" esquema, tal como "fabric: / MyApp/MyService".
* **Anfitrião do serviço**: as instâncias de serviço com nome que cria tem de executar no interior de um processo de anfitrião. O anfitrião do serviço é apenas um processo em que podem executar instâncias do seu serviço.
* **Serviço de registo**: registo reúne tudo. O tipo de serviço tem de ser registado com o runtime Service Fabric num anfitrião de serviço para permitir que o Service Fabric criar instâncias do mesmo para ser executada.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem monitorização de estado
Um serviço sem monitorização de estado é um tipo de serviço que está atualmente a norma em aplicações na nuvem. Considera-se sem monitorização de estado porque o próprio serviço não contém dados que tem de ser armazenados de forma fiável ou tornado de elevada. Se uma instância de um serviço sem monitorização de estado foi encerrado, todos os respetivo estado interno é perdido. Este tipo de serviço, estado tem de ser persistente para um arquivo externo, como tabelas do Azure ou uma base de dados do SQL Server, para se tornar elevada disponibilidade e fiável.

Inicie o Visual Studio 2015 ou Visual Studio 2017 como administrador e criar um novo projeto de aplicação de Service Fabric com o nome *Olámundo*:

![Utilize a caixa de diálogo novo projeto para criar uma nova aplicação de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, criar um projeto de serviço sem monitorização de estado com o nome *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um projeto de serviço sem monitorização de estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Agora, a sua solução contém dois projetos:

* *Olámundo*. Este é o *aplicação* projeto que contém o *serviços*. Também contém o manifesto da aplicação que descreve a aplicação, bem como um número de scripts do PowerShell que ajudam a implementar a sua aplicação.
* *HelloWorldStateless*. Este é o projeto de serviço. Contém a implementação de serviço sem estado.

## <a name="implement-the-service"></a>Implementar o serviço
Abra o **HelloWorldStateless.cs** ficheiros no projeto de serviço. No Service Fabric, um serviço pode ser executado qualquer lógica empresarial. A API de serviço fornece dois pontos de entrada para o seu código:

* Um método de ponto de entrada open-ended, denominado *runasync com*, onde pode começar a executar quaisquer cargas de trabalho, incluindo as cargas de trabalho de computação de execução longa.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Um ponto de entrada de comunicação em que pode plug-in a pilha de comunicação de escolha, tal como ASP.NET Core. Este é onde pode começar a receber pedidos de utilizadores e outros serviços.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, iremos irá focar-se no `RunAsync()` o método de ponto de entrada. Este é onde imediatamente pode começar a executar o seu código.
O modelo de projeto inclui uma implementação de exemplo de `RunAsync()` que incrementa uma contagem graduais.

> [!NOTE]
> Para obter mais informações sobre como trabalhar com uma pilha de comunicação, consulte [serviços de API Web do Service Fabric com OWIN automática de alojamento](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>Runasync com
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A plataforma chama este método quando uma instância de um serviço está pronto para executar e colocá-la. Para um serviço sem monitorização de estado, basta que significa quando a instância de serviço é aberta. Um token de cancelamento é fornecido para coordenar quando a instância de serviço tem de ser fechado. No Service Fabric, este ciclo de abertura/fecho de uma instância de serviço pode ocorrer demasiadas vezes ao longo da duração do serviço como um todo. Esta situação pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recurso.
* Falhas ocorrerem no seu código.
* A aplicação ou o sistema está atualizado.
* O hardware subjacente sofre uma falha.

Esta orquestração é gerida pelo sistema para manter o seu serviço altamente disponível e corretamente equilibrada.

`RunAsync()`não deverá bloquear forma síncrona. A implementação de runasync com deve devolver uma tarefa ou await em quaisquer operações demoradas ou bloqueios para permitir que o tempo de execução continuar. Tenha em atenção no `while(true)` ciclo no exemplo anterior, uma tarefa-returning `await Task.Delay()` é utilizado. Se a carga de trabalho tem bloquear de forma síncrona, deve agendar uma nova tarefa com `Task.Run()` no seu `RunAsync` implementação.

O cancelamento da sua carga de trabalho é um esforço cooperative orquestrado pelo token de cancelamento fornecido. O sistema irá aguardar que a tarefa terminar (por conclusão com êxito, cancelamento ou falhas) antes de se move. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair `RunAsync()` mais rapidamente possível quando o sistema pede cancelamento.

Neste exemplo de serviço sem monitorização de estado, a contagem é armazenada numa variável local. Mas porque se trata de um serviço sem monitorização de estado, o valor que é armazenado existe apenas para o ciclo de vida atual da respetiva instância de serviço. Quando move ou reinicia o serviço, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
Serviço de recursos de infraestrutura apresenta um novo tipo de serviço que tem o estado monitorizado. Um serviço com estado pode manter o estado da forma fiável no serviço de si próprio, localizado conjuntamente com o código que está a ser utilizado. Estado é efetuado elevado pelo Service Fabric sem a necessidade de manter o estado para um arquivo de externo.

Para converter um valor de contador sem monitorização de estado de elevada disponibilidade e persistente, mesmo quando move ou reinicia o serviço, terá de um serviço com monitorização de estado.

Da mesma *Olámundo* aplicação, pode adicionar um novo serviço clicar nos serviços de referências do projeto de aplicação e selecionando **adicionar -> novo serviço de recursos de infraestrutura de serviço**.

![Adicionar um serviço a sua aplicação de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **serviço de monitorização de estado** e dê-lhe nome *HelloWorldStateful*. Clique em **OK**.

![Utilize a caixa de diálogo novo projeto para criar um novo serviço de monitorização de estado de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A aplicação deve agora ter dois serviços: o serviço sem estado *HelloWorldStateless* e o serviço com estado *HelloWorldStateful*.

Um serviço com estado tem os mesmos pontos de entrada como um serviço sem estado. A principal diferença é a disponibilidade de um *fornecedor de estado* que pode armazenar o estado de forma fiável. Service Fabric inclui uma implementação do fornecedor de estado chamada [coleções fiável](service-fabric-reliable-services-reliable-collections.md), que permite criar replicado estruturas de dados através do Gestor de estado fiável. Um serviço fiável com monitorização de estado utiliza este fornecedor de estado por predefinição.

Abra **HelloWorldStateful.cs** no *HelloWorldStateful*, que contém o seguinte método runasync com:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>Runasync com
`RunAsync()`funciona da mesma forma nos serviços de monitorização de estado e sem monitorização de estado. No entanto, num serviço com monitorização de estado, a plataforma executa tarefas adicionais em seu nome antes de ser executada `RunAsync()`. Este trabalho pode incluir a garantir que o Gestor de estado fiável e coleções fiável, estará pronto a utilizar.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de estado fiável
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) é uma implementação de dicionário que pode utilizar para armazenar o estado da forma fiável no serviço. Com o Service Fabric e fiável de coleções, pode armazenar dados diretamente no seu serviço sem a necessidade de um arquivo persistente externo. Coleções fiáveis tornar os dados de elevada disponibilidade. Service Fabric executa esta operação através da criação e gestão de vários *réplicas* do seu serviço para si. Também fornece uma API que deduz ausente complexidades de gerir as réplicas e os respetivos transições de estado.

Coleções fiáveis podem armazenar qualquer tipo de .NET, incluindo os tipos personalizados, com algumas limitações:

* Recursos de infraestrutura de serviço faz com que o seu estado altamente disponível *replicar* Estado em nós e coleções fiável armazenam os seus dados para disco local em cada réplica. Isto significa que tudo o que é armazenado em coleções fiável tem de ser *serializável*. Por predefinição, utilize coleções fiável [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para serialização, por isso, é importante certificar-se de que os tipos são [suportado pelo serializador de contrato de dados](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) quando utiliza o serializador de predefinição.
* Objetos são replicados para elevada disponibilidade ao consolidar transações em coleções fiável. Objetos armazenados em coleções fiável são mantidos na memória local no seu serviço. Isto significa que tem uma referência local para o objeto.
  
   É importante que não mutate locais instâncias desses objetos sem efetuar uma operação de atualização na coleção de fiável numa transação. Isto acontece porque as alterações ao locais instâncias de objetos não serão replicadas automaticamente. Tem de inserir novamente o objeto no dicionário ou utilize um do *atualizar* os métodos no dicionário.

O Gestor de estado fiável gere coleções fiável para si. Pode simplesmente colocar o Gestor de estado fiável para uma coleção fiável pelo nome em qualquer altura e em qualquer lugar no seu serviço. O Gestor de estado fiável garante que obtém uma referência de volta. Não recomendamos que guarde as referências a instâncias de coleção fiável no membro de classe variáveis ou propriedades. Deve ser dada especial cuidado para garantir que a referência está definida para uma instância de todas as vezes no ciclo de vida do serviço. O Gestor de estado fiável processa este trabalho por si e está otimizado para visitas de repetições.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Coleções fiáveis tem muitas das mesmas operações que os respetivos `System.Collections.Generic` e `System.Collections.Concurrent` homólogos, exceto LINQ. As operações em coleções fiável são assíncronas. Isto acontece porque as operações de escrita de coleções fiável efetuarem operações de e/s para replicar e manter os dados para o disco.

As operações de coleção fiável *transacional*, para que pode manter estado consistente em vários coleções fiável e operações. Por exemplo, pode anular um item de trabalho a partir de uma fila fiável, efetuar uma operação no mesmo e guardar o resultado um dicionário fiável, tudo dentro de uma única transação. Esta é tratada como uma operação Atómica e garante que será efetuada com êxito a operação de toda ou irá reverter a operação de toda. Se ocorrer um erro depois de anular o item, mas antes de guardar o resultado, toda a transação é revertida e o item permanece na fila para processamento.

## <a name="run-the-application"></a>Executar a aplicação
Vamos voltar agora ao *Olámundo* aplicação. Agora pode compilar e implementar os seus serviços. Quando prime **F5**, a aplicação será criada e implementada para o cluster local.

Depois dos serviços de iniciar a execução, pode ver os eventos de rastreio de eventos para o Windows (ETW) gerado num **eventos de diagnóstico** janela. Tenha em atenção que os eventos apresentados são do serviço sem monitorização de estado e o serviço de monitorização de estado da aplicação. Pode interromper o fluxo clicando a **colocar em pausa** botão. Em seguida, pode examinar os detalhes de uma mensagem, expandindo essa mensagem.

> [!NOTE]
> Antes de executar a aplicação, certifique-se de que tem um cluster de desenvolvimento local em execução. Veja o [guia de introdução](service-fabric-get-started.md) para obter informações sobre como configurar o ambiente local.
> 
> 

![Ver eventos de diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Passos seguintes
[Depurar a aplicação de Service Fabric no Visual Studio](service-fabric-debugging-your-application.md)

[Começar a: serviços de API Web do Service Fabric com OWIN automática de alojamento](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre coleções fiável](service-fabric-reliable-services-reliable-collections.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização da aplicação](service-fabric-application-upgrade.md)

[Referência para programadores para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

