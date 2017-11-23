---
title: "Criar a sua primeira microsserviço do Azure com base em ator em c# | Microsoft Docs"
description: "Este tutorial explica os passos da criação, depurar e implementar um serviço baseado em ator simple utilizando o serviço de recursos de infraestrutura Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: vturecek
ms.openlocfilehash: eb076c30eda63c37a8b555d40d5903cbbf0d426a
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="getting-started-with-reliable-actors"></a>Introdução a Reliable Actors
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-actors-get-started.md)
> * [Java em Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica criar e depurar uma aplicação simples de Ator fiável no Visual Studio. Para obter mais informações sobre Reliable Actors, consulte [introdução ao serviço de recursos de infraestrutura Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Service Fabric, incluindo o Visual Studio, configurar no seu computador. Para obter mais informações, consulte [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio

Inicie o Visual Studio versão 2015 ou posterior como um administrador e, em seguida, crie um novo **aplicação do serviço Fabric** projeto:

![Ferramentas do Service Fabric para Visual Studio – novo projeto][1]

Na caixa de diálogo seguinte, escolha **serviço de Atores** e introduza um nome para o serviço.

![Modelos de projeto de Service Fabric][5]

O projeto criado mostra a estrutura seguinte:

![Estrutura do projeto de Service Fabric][2]

## <a name="examine-the-solution"></a>Examine a solução

A solução contém três projetos:

* **O projeto de aplicação (MinhaAplicação)**. Este projeto pacotes todos os serviços em conjunto para implementação. Contém o *ApplicationManifest.xml* e scripts do PowerShell para gerir a aplicação.

* **O projeto de interface (HelloWorld.Interfaces)**. Este projeto contém a definição de interface para os atores. Interfaces de ator podem ser definidas em qualquer projeto com um nome.  A interface define o contrato de ator que é partilhado pela implementação de ator e os clientes de ator ao chamar.  Porque os projetos de cliente podem dependem da mesma, normalmente, faz sentido defini-lo numa assemblagem separada da implementação ator.

* **O projeto de serviço de atores (Olámundo)**. Este projeto define o serviço do Service Fabric que vai alojar o ator. Contém a implementação de ator, *HellowWorld.cs*. Uma implementação de atores é uma classe que deriva de um tipo base `Actor` e implementa as interfaces definidas no *MyActor.Interfaces* projeto. Uma classe de ator também tem de implementar um construtor que aceite um `ActorService` instância e um `ActorId` e transmite-os para a base `Actor` classe.
    
    Este projeto também contém *Program.cs*, que regista as classes de ator com o tempo de execução do Service Fabric utilizando `ActorRuntime.RegisterActorAsync<T>()`. O `HelloWorld` classe já está registada. Quaisquer implementações de ator adicionais adicionadas ao projeto também tem de estar registadas no `Main()` método.

## <a name="customize-the-helloworld-actor"></a>Personalizar o ator HelloWorld

O modelo de projeto define alguns métodos no `IHelloWorld` interface e implementa-las no `HelloWorld` implementação ator.  Substitua os métodos de, pelo que o serviço de atores devolve uma cadeia "Olá mundo" simple.

No *HelloWorld.Interfaces* no projeto a *IHelloWorld.cs* de ficheiros, substitua a definição de interface da seguinte forma:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

No **Olámundo** no projeto **HelloWorld.cs**, substitua a definição de classe inteira da seguinte forma:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Prima **Ctrl-Shift-B** compilar o projeto e certifique-se de que tudo compila.

## <a name="add-a-client"></a>Adicionar um cliente

Crie uma aplicação de consola simples para chamar o serviço de atores.

1. Clique com o botão direito na solução no Explorador de soluções > **adicionar** > **novo projeto...** .

2. Sob o **.NET Core** tipos de projeto, escolha **aplicação de consola (.NET Core)**.  Nomeie o projeto *ActorClient*.
    
    ![Adicionar caixa de diálogo novo projeto][6]    
    
    > [!NOTE]
    > Uma aplicação de consola não é do tipo de aplicação que normalmente utilizaria como um cliente no Service Fabric, mas permite um exemplo de depuração e teste utilizando o emulador do Service Fabric local conveniente.

3. A aplicação de consola tem de ser uma aplicação de 64 bits para manter a compatibilidade com o projeto de interface e outras dependências.  No Explorador de soluções, clique com botão direito do **ActorClient** projeto e, em seguida, clique em **propriedades**.  No **criar** separador, defina **destino plataforma** para **x64**.
    
    ![Propriedades de compilação][8]

4. O projeto de cliente requer que o pacote NuGet do reliable actors.  clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.  Na consola do Gestor de pacotes, introduza o seguinte comando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    O pacote NuGet e as respetivas dependências estão instaladas no projeto ActorClient.

5. O projeto de cliente também requer uma referência para o projeto de interfaces.  No projeto ActorClient, faça duplo clique **dependências** e, em seguida, clique em **Adicionar referência...** .  Selecione **projetos > solução** (se não estiver já selecionada) e escala, em seguida, a caixa de verificação junto a **HelloWorld.Interfaces**.  Clique em **OK**.
    
    ![Adicionar caixa de diálogo referência][7]

6. No projeto ActorClient, substitua os conteúdos integrais de *Program.cs* com o seguinte código:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Em execução e de depuração

Prima **F5** para criar, implementar e executar a aplicação localmente no cluster de desenvolvimento do Service Fabric.  Durante o processo de implementação, pode ver o progresso no **saída** janela.

![Janela de saída de depuração Service Fabric][3]

Quando a saída contém o texto, *a aplicação esteja pronta*, é possível testar o serviço utilizando a aplicação de ActorClient.  No Explorador de soluções, clique com botão direito no **ActorClient** do projeto, em seguida, clique em **depurar** > **iniciar nova instância**.  A aplicação de linha de comando deve apresentar a saída do serviço de atores.

![saída da aplicação][9]

> [!TIP]
> O tempo de execução de Atores do Service Fabric emite algumas [eventos e contadores de desempenho relacionados com métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Estes são úteis em diagnóstico e monitorização do desempenho.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [como Reliable Actors utilizar a plataforma de Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png