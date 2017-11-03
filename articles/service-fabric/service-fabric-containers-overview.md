---
title: "Descrição geral do Service Fabric e contentores | Microsoft Docs"
description: "Uma descrição geral do Service Fabric e a utilização de contentores para implementar aplicações de microsserviço. Este artigo fornece uma descrição geral de como contentores podem ser utilizados e as funcionalidades disponíveis no Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: f47a855b94a29a2e9bbf4ca509e68612423aa65d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-and-containers"></a>Serviço de recursos de infraestrutura e de contentores
> [!NOTE]
> Implementar contentores para um cluster do Service Fabric no Windows 10 ainda não é suportado. 
>   

## <a name="introduction"></a>Introdução
Azure Service Fabric é uma [orchestrator](service-fabric-cluster-resource-manager-introduction.md) dos serviços de um cluster de máquinas, com anos de utilização e a otimização de grande escala para serviços Microsoft. Os serviços podem ser desenvolvidos de várias maneiras de utilizar o [modelos de programação do Service Fabric](service-fabric-choose-framework.md) para implementar [convidado executáveis](service-fabric-deploy-existing-app.md). Por predefinição, o Service Fabric implementa e ativa estes serviços como processos. Processos de fornecem o ativação mais rápida e mais alta densidade utilização de recursos num cluster. Serviço de recursos de infraestrutura também pode implementar serviços nas imagens de contentor. Importante ainda, pode combinar os serviços de processos e serviços nos contentores na mesma aplicação.   

## <a name="what-are-containers"></a>Quais são contentores?
Contentores são encapsulados, individualmente implementáveis componentes que são executados como instâncias isoladas no mesmo kernel para tirar partido de Virtualização que fornece um sistema operativo. Assim, cada aplicação e respetivos bibliotecas de tempo de execução, dependências e do sistema executam dentro de um contentor com acesso total, privado para a vista de contentor próprio isolada das construções de sistema operativo. Juntamente com a portabilidade, este nível de isolamento de segurança e de recursos é a vantagem principal para utilizar contentores com o Service Fabric, caso contrário, que executa serviços em processos.

Contentores são uma tecnologia de Virtualização que Virtualiza do sistema operativo subjacente de aplicações. Contentores fornecem um ambiente imutável para as aplicações sejam executadas com variando graus de isolamento. Contentores são executados diretamente sobre o kernel e terem uma vista isolada do sistema de ficheiros e outros recursos. Em comparação com as máquinas virtuais, contentores tem as seguintes vantagens:

* **Pequeno**: contentores utilizarem um espaço de armazenamento única e versões de camada e as atualizações para aumentar a eficiência.
* **Rápido**: contentores não tem de efetuar o arranque de um sistema operativo completo, para poderem iniciar muito mais rapidamente, normalmente, em segundos.
* **Portabilidade**: uma imagem de aplicação pode ser convertidos para serem executados na nuvem, no local, dentro de máquinas virtuais, nem diretamente em máquinas físicas.
* **Governação de recursos**: um contentor pode limitar os recursos físicos que se pode consumir no respetivo anfitrião.

## <a name="container-types-and-supported-environments"></a>Tipos de contentor e Ambientes suportados
Service Fabric suporta contentores no Linux e Windows e também suporta o modo de isolamento de Hyper-V em que esta. 

> [!NOTE]
> Implementar contentores para um cluster do Service Fabric no Windows 10 não é atualmente suportado. 
> 

### <a name="docker-containers-on-linux"></a>Contentores de docker no Linux
Docker fornece APIs de alto nível para criar e gerir contentores por cima de contentores de kernel do Linux. Hub de docker é um repositório central para armazenar e obter as imagens de contentor.
Para um tutorial, consulte [implementar um contentor de Docker Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Contentores do Windows Server
Windows Server 2016 fornece dois tipos diferentes de contentores que diferem no nível de isolamento indicado. Contentores do Windows Server e contentores de Docker são semelhantes porque ambos têm o isolamento de sistema de ficheiros e espaço de nomes mas partilham o kernel com o anfitrião que estão em execução. No Linux, este isolamento tradicionalmente tiver sido fornecido pelo `cgroups` e `namespaces`, e contentores do Windows Server comportam-se da mesma forma.

Contentores do Windows com suporte de Hyper fornecem mais segurança e isolamento, porque cada contentor não partilha o kernel do sistema operativo com outros contentores ou com o anfitrião. Com este nível de isolamento de segurança mais elevado, contentores de Hyper-V ativada destinam-se cenários hostile, multi-inquilino.
Para um tutorial, consulte [implementar um contentor do Windows para o Service Fabric](service-fabric-get-started-containers.md).

A figura seguinte mostra os diferentes tipos de Virtualização e isolamento níveis disponíveis no sistema operativo.
![Plataforma de Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários para utilizar contentores
Seguem-se exemplos típicos onde um contentor é uma boa opção:

* **IIS de comparação de precisão e deslocar**: Se tiver existente [ASP.NET MVC](https://www.asp.net/mvc) aplicações que pretende continuar a utilizar, colocá-los num contentor em vez de a migrá-las para o ASP.NET Core. Estas aplicações de ASP.NET MVC dependem de serviços de informação Internet (IIS). Pode estas aplicações nas imagens de contentor da imagem de IIS precreated do pacote e implementá-los com o Service Fabric. Consulte [imagens do contentor no Windows Server](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server) para obter informações sobre contentores do Windows.
* **Combinar contentores e micro-serviços de Service Fabric**: utilizar uma imagem de contentor existente para a parte da sua aplicação. Por exemplo, poderá utilizar o [contentor NGINX](https://hub.docker.com/_/nginx/) para web front-end da sua aplicação e a serviços com monitorização de estado para o cálculo de back-end mais intensiva.
* **Reduzir o impacto dos serviços de "vizinhos inúteis"**: pode utilizar a capacidade de governação de recursos de contentores para restringir os recursos que utiliza um serviço num anfitrião. Se os serviços podem consumir muitos recursos e afetar o desempenho de terceiros (como uma operação de execução longa, semelhante de consulta), considere colocar estes serviços ao contentores que tenham a governação de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte de Service Fabric para contentores
Serviço de recursos de infraestrutura suporta a implementação de contentores de Docker em contentores de Linux e Windows Server no Windows Server 2016, juntamente com suporte para o modo de isolamento de Hyper-V. 

No Service Fabric [modelo de aplicação](service-fabric-application-model.md), um contentor representa um anfitrião de aplicações no serviço várias réplicas são colocadas. Service Fabric pode executar quaisquer contentores, e o cenário é semelhante a [cenário executável convidado](service-fabric-deploy-existing-app.md), onde o pacote uma aplicação existente no interior de um contentor. Este cenário é o caso de utilização comuns para contentores, e os exemplos incluem a executar uma aplicação de escrita em qualquer idioma ou estruturas, mas não a utilizar os modelos de programação do Service Fabric incorporados.

Além disso, pode executar [serviços do Service Fabric no interior de contentores](service-fabric-services-inside-containers.md) bem. Suporte para os serviços do Service Fabric em execução no interior de contentores está actualmente limitado e irá ser melhorado em versões futuras.

Service Fabric tem várias capacidades de contentor que o ajudam a criarem aplicações que são compostas de micro-serviços que são de. Recursos de infraestrutura de serviço oferece as seguintes capacidades de serviços:

* Implementação de imagem de contentor e a ativação.
* Governação de recursos, incluindo a definir valores de recursos por predefinição em clusters do Azure.
* Autenticação do repositório.
* Porta do contentor para o mapeamento de porta do anfitrião.
* Deteção de contentor de contentor e comunicação.
* Capacidade para configurar e definir variáveis de ambiente.
* Capacidade para definir as credenciais de segurança no contentor.
* Escolher entre diferentes modos de funcionamento em rede para contentores.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre contentores, que Service Fabric é orchestrator um contentor e de que o Service Fabric tem funcionalidades que suportam contentores. Como passo seguinte, iremos abordará exemplos de cada uma das funcionalidades para lhe mostrar como utilizá-los.

[Criar a sua primeira aplicação de contentor do Service Fabric no Windows](service-fabric-get-started-containers.md)

[Criar a primeira aplicação de contentor do Service Fabric no Linux](service-fabric-get-started-containers-linux.md)

[Saiba mais sobre os contentores do Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
