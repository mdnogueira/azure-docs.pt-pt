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
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 3f447e049ccd33c77f422e8aa703ad6646f9ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-reliable-actors"></a>Introdução a Reliable Actors
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-actors-get-started.md)
> * [Java em Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Este artigo explica as noções básicas do Azure Service Fabric Reliable Actors e explica-lhe como criar, depurar e implementar uma aplicação simples de Ator fiável no Visual Studio.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Service Fabric configurar no seu computador.
Se precisar de configurá-lo, consulte as instruções detalhadas no [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a utilizar o Reliable Actors, só precisa de compreender alguns conceitos básicos:

* **Serviço de ator**. O Reliable Actors é compactado no Reliable Services que pode ser implementado na infraestrutura do Service Fabric. As instâncias de ator são ativadas numa instância de serviço com nome.
* **Registo de ator**. Tal como acontece com o Reliable Services, um serviço Reliable Actor tem de ser registado com o runtime do Service Fabric. Além disso, o tipo de ator tem de ser registado com o runtime de Ator.
* **Interface de ator**. A interface de ator serve para definir uma interface pública com tipos de dados inflexíveis de um ator. Na terminologia modelo do Reliable Actor, a interface de ator define os tipos de mensagens que o ator pode compreender e processar. A interface de ator é utilizada por outros atores e aplicações cliente para "enviar" (de forma assíncrona) mensagens para o ator. O Reliable Actors pode implementar várias interfaces.
* **Classe ActorProxy**. A classe ActorProxy é utilizada por aplicações cliente para invocar os métodos expostos através da interface de ator. A classe ActorProxy fornece duas funcionalidades importantes:
  
  * Resolução de nomes: é capaz de localizar o ator no cluster (localizar o nó do cluster onde está alojado).
  * Processamento de falhas: pode repetir invocações de método e voltar a resolver a localização do ator após, por exemplo, uma falha que exija que o ator seja relocalizado para outro nó no cluster.

As seguintes regras que pertencem a interfaces de ator merecem especial referência:

* Os métodos de interfaces de ator não podem ser sobrecarregados.
* Os métodos de interfaces de ator não podem ter parâmetros out, ref ou opcionais.
* As interfaces genéricas não são suportadas.

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio
Inicie o Visual Studio 2015 ou Visual Studio 2017 como administrador e criar um novo projeto de aplicação de Service Fabric:

![Ferramentas do Service Fabric para Visual Studio – novo projeto][1]

Na caixa de diálogo seguinte, pode escolher o tipo de projeto que pretende criar.

![Modelos de projeto de Service Fabric][5]

Para o projeto de Olámundo, vamos utilizar o serviço de Service Fabric Reliable Actors.

Depois de criar a solução, deverá ver a estrutura seguinte:

![Estrutura do projeto de Service Fabric][2]

## <a name="reliable-actors-basic-building-blocks"></a>Blocos modulares básicos do Reliable Actors
Uma solução de Reliable Actors típica é composta por três projetos:

* **O projeto de aplicação (MyActorApplication)**. Este é o projeto que todos os serviços em conjunto para a implementação de pacotes. Contém o *ApplicationManifest.xml* e scripts do PowerShell para gerir a aplicação.
* **O projeto de interface (MyActor.Interfaces)**. Este é o projeto que contém a definição de interface para os atores. No projeto MyActor.Interfaces, pode definir as interfaces que serão utilizadas pelo atores na solução. As interfaces de ator podem ser definidas em qualquer projeto com qualquer nome, no entanto, a interface define o contrato de ator que é partilhado pela implementação de ator e os clientes chamar ator, pelo que, normalmente, faz sentido defini-lo numa assemblagem separado do a implementação de ator e podem ser partilhados por vários outros projetos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **O projeto de serviço de atores (MyActor)**. Este é o projeto utilizado para definir o serviço do Service Fabric que vai alojar o ator. Contém a implementação do ator. Uma implementação de atores é uma classe que deriva de um tipo base `Actor` e implementa as interfaces que estão definidas no projeto MyActor.Interfaces. Uma classe de ator também tem de implementar um construtor que aceite um `ActorService` instância e um `ActorId` e transmite-os para a base `Actor` classe. Isto permite a inserção de dependências de construtor de dependências de plataforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

O serviço de ator tem de ser registado com um tipo de serviço no runtime do Service Fabric. Para que o Serviço de Ator execute as suas instâncias de ator, o tipo de ator também tem de estar registado no Serviço de Ator. O método de registo `ActorRuntime` realiza este trabalho para atores.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

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

Se iniciar a partir de um novo projeto no Visual Studio e tiver apenas uma definição de ator, o registo está incluído por predefinição no código que gera o Visual Studio. Se definir outros atores no serviço, terá de adicionar o registo de ator utilizando:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> O tempo de execução de Atores do Service Fabric emite algumas [eventos e contadores de desempenho relacionados com métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Estes são úteis em diagnóstico e monitorização do desempenho.
> 
> 

## <a name="debugging"></a>Depuração
As ferramentas do Service Fabric para Visual Studio suportam a depuração no seu computador local. Pode iniciar uma sessão de depuração, atingir a tecla F5. Visual Studio cria (se necessário) pacotes. Também implementa a aplicação no cluster de Service Fabric local e anexa o depurador.

Durante o processo de implementação, pode ver o progresso no **saída** janela.

![Janela de saída de depuração Service Fabric][3]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [como Reliable Actors utilizar a plataforma de Service Fabric](service-fabric-reliable-actors-platform.md).

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
