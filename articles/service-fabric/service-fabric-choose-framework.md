---
title: "Descrição geral do modelo de programação do Service Fabric | Microsoft Docs"
description: "Recursos de infraestrutura de serviço oferece dois estruturas para a criação de serviços: a estrutura de ator e a arquitetura de serviços. Oferecem distintos compromissos na simplicidade e controlo."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 237b8396b56fdec86cc005c121646556825d8e98
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="service-fabric-programming-model-overview"></a>Descrição geral do modelo programação Service Fabric
Recursos de infraestrutura de serviço oferece várias formas para escrever e gerir os seus serviços. Serviços podem optar por utilizar as APIs de recursos de infraestrutura de serviço para tirar partido das funcionalidades e estruturas de aplicações da plataforma. Os serviços também podem ser qualquer programa do executável compilado escrito em qualquer idioma ou código em execução num contentor simplesmente alojado num cluster do Service Fabric.

## <a name="guest-executables"></a>Executáveis de convidado
A [executável convidado](service-fabric-deploy-existing-app.md) é um existente, o executável arbitrário (escritos em qualquer idioma) que pode ser executado como um serviço na sua aplicação. Executáveis convidado não chamam diretamente as APIs do SDK do serviço de recursos de infraestrutura. No entanto, ainda beneficiam das funcionalidades a plataforma oferece, tais como a capacidade de deteção do serviço, estado de funcionamento personalizado e carregar relatórios ao chamar as APIs REST exposta pelo Service Fabric. Também têm o suporte de ciclo de vida de aplicação completa.

Introdução ao executáveis convidado ao implementar o primeiro [aplicação executável convidado](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contentores
Por predefinição, o Service Fabric implementa e ativa serviços como processos. Serviço de recursos de infraestrutura também pode implementar serviços no [contentores](service-fabric-containers-overview.md). Serviço de recursos de infraestrutura suporta a implementação do Linux contentores e contentores do Windows no Windows Server 2016. Imagens de contentor podem ser solicitadas a partir de qualquer repositório de contentor e implementar a máquina. Pode implementar as aplicações existentes como convidado exectuables, Service Fabric sem monitorização de estado ou com monitorização de estado Reliable services ou Reliable Actors nos contentores, e pode combinar os serviços de processos e serviços nos contentores na mesma aplicação.

[Saiba mais sobre containerizing os serviços no Windows ou Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services é uma arquitetura de ponderação leve de escrita de serviços que integram com a plataforma de Service Fabric e beneficiam o conjunto completo de funcionalidades da plataforma. Reliable Services fornecem um conjunto mínimo de APIs que permitem que o tempo de execução do Service Fabric gerir o ciclo de vida dos seus serviços e permitir que os serviços de interagir com o tempo de execução. A estrutura da aplicação é mínima, dando-lhe controlo total sobre as opções de design e implementação e pode ser utilizada para alojar quaisquer outra estrutura da aplicação, por exemplo, o ASP.NET Core.

Reliable Services podem ser sem monitorização de estado, semelhante a maioria das plataformas de serviço, tais como servidores web, em que é criada igual a cada instância do serviço de estado é persistente numa solução externa, como a base de dados do Azure ou o Table Storage do Azure.

Também podem ser com monitorização de estado, Reliable Services exclusivo para o Service Fabric, onde estado é persistente diretamente no serviço de si próprio ao utilizar coleções fiável. O estado é efetuado elevada através da replicação e distribuído através da criação de partições, todos os geridos automaticamente pelo Service Fabric.

[Saiba mais sobre Reliable Services](service-fabric-reliable-services-introduction.md) ou começar por [ao escrever no primeiro serviço fiável](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>Núcleo de ASP.NET
ASP.NET Core é uma arquitetura de open source e plataforma novo para a criação de baseado na nuvem ligada à Internet aplicações modernas, tais como as web apps, aplicações de IoT e back-ends móveis. Service Fabric se integra com o ASP.NET Core pode escrever aplicações que tirar partido das capacidades de orquestração avançadas de Service Fabric e fiável de coleções sem monitorização de estado e com monitorização de estado do ASP.NET Core.

[Saiba mais sobre o ASP.NET Core no Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) ou começar por [ao escrever a sua primeira aplicação de ASP.NET Core Service Fabric](service-fabric-add-a-web-frontend.md).

## <a name="reliable-actors"></a>Reliable Actors
Desenvolvida Reliable Services, a arquitetura de Ator fiável é uma arquitetura de aplicações que implementa o padrão de Ator Virtual, com base num padrão de conceção ator. A estrutura de Ator fiável utiliza independentes unidades de estado e de computação com single-threaded execução chamada atores. A estrutura de Ator fiável fornece comunicação incorporada de atores e configurações de persistência e escalável de estado predefinido.

Como Reliable Actors em si é uma arquitetura de aplicações incorporada no Reliable Services, estão totalmente integrado com a plataforma de Service Fabric e os benefícios do conjunto completo de funcionalidades oferecidas da plataforma.

[Saiba mais sobre Reliable Actors](service-fabric-reliable-actors-introduction.md) ou começar por [ao escrever o seu primeiro serviço de Atores fiável](service-fabric-reliable-actors-get-started.md)


[Criar um serviço de front-end com o ASP.NET Core](service-fabric-add-a-web-frontend.md)

## <a name="next-steps"></a>Passos seguintes
[Descrição geral do Service Fabric e os contentores](service-fabric-containers-overview.md)

[Descrição geral de serviços fiável](service-fabric-reliable-services-introduction.md)

[Descrição geral de Atores fiável](service-fabric-reliable-actors-introduction.md)

[Recursos de infraestrutura de serviço e o ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




