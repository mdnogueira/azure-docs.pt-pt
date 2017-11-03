---
title: "Descrição geral dos recursos de infraestrutura de serviço no Azure | Microsoft Docs"
description: "Uma descrição geral do Service Fabric, onde as aplicações são compostas por muitas micro-serviços para fornecer o dimensionamento e resiliência. Service Fabric é uma plataforma de sistemas distribuídos utilizado para criar dimensionável, fiável e gerido facilmente aplicações para a nuvem."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: 8ff0d38a679b673b148dd808050eda82060cfe80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-service-fabric"></a>Descrição geral dos recursos de infraestrutura de serviço do Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis. Serviço de recursos de infraestrutura também aborda os desafios significativos no desenvolvimento e gestão de aplicações nativas de nuvem. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis. Service Fabric representa a plataforma de próxima geração para criar e gerir estes empresarial, a camada-1, a aplicações de escala da nuvem em execução nos contentores.

Este breve vídeo apresenta Service Fabric e micro-serviços:<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Aplicações composto micro-serviços 
Recursos de infraestrutura de serviço permite-lhe criar e gerir aplicações escaláveis e fiáveis compostas micro-serviços que são executadas a alta densidade num agrupamento partilhado de máquinas, que é referido como um cluster. Fornece um tempo de execução sofisticado, simples para criar distribuída, dimensionável, sem monitorização de estado e micro-serviços com monitorização de estado em execução nos contentores. Também fornece capacidades de gestão de aplicações abrangente para aprovisionar, implementar, monitorizar, atualização/patch e eliminar as aplicações implementadas, incluindo de serviços.

Muitos serviços Microsoft hoje em dia, incluindo a SQL Database do Azure está na base do Service Fabric, base de dados do Azure Cosmos, Cortana, Microsoft Power BI, Microsoft Intune, Event Hubs do Azure, IoT Hub do Azure, Dynamics 365, Skype para empresas e muitas principais serviços do Azure.

Service Fabric é adaptada para criar serviços nativos, que podem começar por algo pequeno, conforme necessário e aumentar de grande escala com centenas ou milhares de máquinas de nuvem.

Serviços de escala de Internet de hoje são criados de micro-serviços. Micro-serviços exemplos de gateways de protocolo, perfis de utilizador, compras carts, inventário de processamento, filas e coloca em cache. Service Fabric é uma plataforma de micro-serviços que proporcione cada microsserviço (ou contentor) um nome exclusivo que pode ser sem monitorização de estado ou com monitorização de estado.

O Service Fabric fornece abrangentes tempo de execução do ciclo de vida das capacidades de gestão e para aplicações que são compostas destas micro-serviços. Nele alojado micro-serviços dentro de contentores que são implementadas e ativadas no cluster do Service Fabric. Uma mudança de máquinas virtuais para contentores torna possível um aumento de ordem de magnitude no densidade. Da mesma forma, outro ordem de grandeza no densidade torna-se possíveis quando mover de contentores para micro-serviços estes contentores. Por exemplo, um único cluster para a SQL Database do Azure é composto por centenas de máquinas com dezenas de milhares de contentores que alojam um total de centenas de milhares de bases de dados. Cada base de dados é um microsserviço com monitorização de estado de Service Fabric. 

Para mais informações sobre a abordagem de micro-serviços, leia o artigo [porquê uma abordagem de micro-serviços à criação de aplicações?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Orquestração e implementação do contentor
Service Fabric é da Microsoft [orchestrator contentor](service-fabric-cluster-resource-manager-introduction.md) implementação micro-serviços através de um cluster de máquinas. Podem ser desenvolvidos micro-serviços de várias maneiras de utilizar o [modelos de programação do Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)ao implementar [qualquer código à sua escolha](service-fabric-deploy-existing-app.md). Importante ainda, pode combinar ambos os serviços de processos e serviços nos contentores na mesma aplicação. Se apenas pretender [implementar e gerir contentores](service-fabric-containers-overview.md), Service Fabric é uma opção perfeita como orchestrator um contentor.

## <a name="any-os-any-cloud"></a>Qualquer SO, qualquer na nuvem
Recursos de infraestrutura de serviço é executado everywhere. Pode criar clusters de Service Fabric em muitos ambientes, incluindo o Azure ou no local, no Windows Server ou no Linux. Pode ainda criar clusters em outras nuvens públicas. Além disso, o ambiente de desenvolvimento no SDK é **idênticos** para o ambiente de produção, com nenhuma emuladores envolvidos. Por outras palavras, o que é executado no seu cluster de desenvolvimento local implementa os clusters nos outros ambientes.

![Plataforma de Service Fabric][Image1]

Para o desenvolvimento do Windows, o SDK de .NET do Service Fabric está integrado com o Visual Studio e o Powershell. Consulte [preparar o ambiente de desenvolvimento Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md). Para o desenvolvimento de Linux, o SDK de Java do Service Fabric está integrado com o Eclipse e Yeoman é utilizado para gerar os modelos para Java, .NET Core e aplicações de contentor. Consulte [preparar o ambiente de desenvolvimento no Linux](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md)

Para obter mais informações sobre a criação de clusters, leia o artigo [criar um cluster no Windows Server ou Linux](service-fabric-deploy-anywhere.md) ou para o Azure, criar um cluster [através do portal do Azure](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Micro-serviços sem monitorização de estado e com monitorização de estado para o Service Fabric
Recursos de infraestrutura de serviço permite-lhe criar aplicações que são compostos micro-serviços ou contentores. Sem monitorização de estado micro-serviços (como gateways de protocolo e web proxies) não manter um Estado mutável fora de um pedido e a resposta do serviço. As funções de trabalho de serviços em nuvem do Azure são um exemplo de um serviço sem estado. Com monitorização de estado micro-serviços (por exemplo, contas de utilizador, as bases de dados, dispositivos, carts compras e filas) mantêm um Estado mutável, autoritativo, para além do pedido e a respetiva resposta. Aplicações de dimensionamento de Internet de hoje consistem de uma combinação de micro-serviços sem monitorização de estado e com monitorização de estado. 

Uma diferenciação chave com o Service Fabric é o foco forte na criação de serviços com monitorização de estado, com o [incorporados modelos de programação ](service-fabric-choose-framework.md) ou de serviços com monitorização de estado. O [cenários de aplicação](service-fabric-application-scenarios.md) descrevem os cenários em que são utilizados serviços com monitorização de estado.


## <a name="application-lifecycle-management"></a>Gestão de ciclo de vida de aplicações
Service Fabric fornece suporte para o ciclo de vida de aplicação completa e CI/CD das aplicações em nuvem, incluindo contentores. Este ciclo de vida inclui desenvolvimento através da implementação, gestão diária e eventual desativação da manutenção.

Capacidades de gestão de ciclo de vida de aplicação de Service Fabric ativar operadores IT e administradores da aplicação utilizar fluxos de trabalho simples e touch baixa para aprovisionar, implementar, aplicar o patch e monitorizar aplicações. Estes fluxos de trabalho incorporados reduzem significativamente a carga sobre operadores IT para manter as aplicações disponíveis continuamente.

A maioria das aplicações é constituída por uma combinação de micro-serviços sem monitorização de estado e com monitorização de estado, contentores e outros executáveis que são implementadas em conjunto. Por ter tipos fortes nas aplicações, o Service Fabric permite a implementação de várias instâncias de aplicações. Cada instância é gerida e atualizar independentemente. Importante ainda, o Service Fabric pode implementar contentores ou qualquer executáveis e torná-los fiável. Por exemplo, Service Fabric pode implementar .NET, núcleo de ASP.NET, node.js, Windows contentores, Linux contentores, Java máquinas virtuais, scripts, Angular ou literalmente tudo o que constitui a sua aplicação.

Service Fabric está integrado com ferramentas de CI/CD como [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html), e [Octopus implementar](https://octopus.com/) e pode ser utilizado com qualquer outra ferramenta CI/CD popular.

Para obter mais informações sobre a gestão de ciclo de vida de aplicação, leia o artigo [ciclo de vida de aplicação](service-fabric-application-lifecycle.md). Para obter mais informações sobre como implementar qualquer código, consulte [implementar um executável de convidado](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Principais capacidades
Ao utilizar o Service Fabric, pode:

* Implemente no Azure ou centros de dados no local que executam o Windows ou Linux com zero alterações de código. Escrever uma vez e, em seguida, implementar em qualquer lugar em qualquer cluster do Service Fabric.
* Desenvolva aplicações dimensionáveis que são compostas de micro-serviços utilizando o Service Fabric programming modelos, contentores ou qualquer código.
* Desenvolva altamente fiáveis micro-serviços sem monitorização de estado e com monitorização de estado. Simplifica a estrutura da sua aplicação utilizando micro-serviços com monitorização de estado. 
* Utilize o modelo de programação Reliable Actors novel para criar objetos da nuvem com o código de self contido e estado.
* Implementar e orquestrar contentores que incluem a contentores do Windows e Linux contentores. Recursos de infraestrutura de serviço é um orchestrator de contentor, a monitorização de estado, de dados.
* Implemente aplicações em segundos, em alta densidade com centenas ou milhares de aplicações ou de contentores por máquina.
* Implementar versões diferentes da mesma aplicação lado a lado e atualizar cada aplicação de forma independente.
* Gerir o ciclo de vida das suas aplicações sem qualquer período de inatividade, incluindo atualizações interrompendo e nonbreaking.
* Ampliar ou reduzir o número de nós num cluster. À medida que nós, as aplicações de dimensionar.
* Monitorizar e diagnosticar o estado de funcionamento das suas aplicações e definir políticas para a execução automáticas reparações.
* Veja o Balanceador de recursos orquestrar a redistribuição de aplicações no cluster. Service Fabric recupera contra falhas e otimiza a distribuição de carga com base nos recursos disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* Para mais informações:
  * [Por que motivo um micro-serviços abordagem de criação de aplicações?](service-fabric-overview-microservices.md)
  * [Descrição geral de terminologia](service-fabric-technical-overview.md)
* Como configurar a sua [ambiente de desenvolvimento do Windows](service-fabric-get-started.md)  
* Como configurar a sua [ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
