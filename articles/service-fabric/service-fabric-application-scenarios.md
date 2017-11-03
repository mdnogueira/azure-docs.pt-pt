---
title: "Cenários de aplicações e de design | Microsoft Docs"
description: "Descrição geral das categorias de aplicações em nuvem no Service Fabric. Descreve o design da aplicação que utiliza os serviços com monitorização de estado e sem monitorização de estado."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: mfussell
ms.openlocfilehash: 471ec6f45f4152fbac56242ef3ce906f8af00b54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicação de Service Fabric
Recursos de infraestrutura de serviço do Azure oferece uma plataforma fiável e flexível que permite-lhe escrever e executar muitos tipos de serviços e aplicações empresariais. Estas aplicações e micro-serviços podem ser sem monitorização de estado ou com monitorização de estado e são com balanceamento de recurso em máquinas virtuais para maximizar a eficiência. A arquitetura do Service Fabric exclusiva permite-lhe efetuar perto de análise de dados em tempo real, cálculo dentro da memória, transações paralelas e nas suas aplicações de processamento de eventos. Pode facilmente dimensionar as suas aplicações ou reduzir verticalmente (realmente a entrada ou saída), dependendo dos requisitos de recursos a alteração.

A plataforma de Service Fabric no Azure é ideal nas seguintes categorias de aplicações:

* **Serviços de elevada disponibilidade**: serviços do Service Fabric fornecem ativação pós-falha rápida criando várias réplicas de serviço secundário. Se um nó, o processo ou o serviço individual ficar inativo devido a hardware ou outra falha, uma das réplicas secundárias é promovida para uma réplica primária com perda mínima de serviço.
* **Serviços dimensionáveis**: serviços individuais podem ser particionados, permitindo o estado para ampliar no cluster. Além disso, serviços individuais podem ser criados e removidos no momento. Os serviços podem rápida e facilmente ampliar das instâncias de alguns em alguns nós para milhares de instâncias em vários nós e, em seguida, ampliados no novamente, consoante as suas necessidades de recursos. Pode utilizar o Service Fabric para criar estes serviços e gerir os respetivos ciclos de vida completos.
* **Cálculo em dados nonstatic**: ativa de Service Fabric criar dados, entrada/saída e as aplicações com monitorização de estado intensivas de computação. Service Fabric permite que a colocalização dos dados e processamento (computação) em aplicações. Normalmente, quando a aplicação necessita de acesso aos dados, há latência de rede associada a uma camada de armazenamento ou de cache de dados externas. Com os serviços do Service Fabric com monitorização de estado, esse latência é eliminado o aperto, permitir performant mais lê e escreve. Diga-por exemplo tiver uma aplicação que executa quase seleções de recomendação em tempo real para os clientes com um requisito de tempo reportadas round-trip de inferior a 100 milissegundos. As características de latência e o desempenho dos serviços do Service Fabric (onde o cálculo de seleção de recomendação estiver colocalizado com os dados e as regras) fornece uma experiência de responder ao utilizador comparado com o modelo de implementação padrão de ter de obter os dados necessários do armazenamento remoto.  
* **Baseados em sessão interativas aplicações**: Service Fabric é útil se as suas aplicações, tais como online jogos ou mensagens instantâneas, necessitam de latência baixa leituras e escritas. Recursos de infraestrutura de serviço permite-lhe criar estas aplicações com monitorização de estado, interativas sem ter de criar um arquivo separado ou cache, conforme necessário para as aplicações sem monitorização de estado. (Isto aumenta a latência e potencialmente apresenta problemas de consistência.).
* **Análise de dados e fluxos de trabalho**: O rápido leituras e escritas de Service Fabric permitem que devem processar de forma fiável ou fluxos de dados de eventos de aplicações. Recursos de infraestrutura de serviço também permite que aplicações que descrevem os pipelines de processamento, onde os resultados tem de ser transmitido para a próxima fase de processamento sem perda e fiável. Estes incluem sistemas transacionais e financeiros, onde os dados garantias de consistência e a computação são essenciais.
* **Recolha de dados, processamento e de IoT**: uma vez que o Service Fabric processa grande escala e com baixa latência através destes serviços com monitorização de estado, este é ideal para processamento de dados em milhões de dispositivos onde os dados para o dispositivo e a computação estão localizados conjuntamente.
Podemos ter visto vários clientes que criada sistemas de IoT com o Service Fabric, incluindo [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) e [Mesh sistemas](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Estrutura de aplicação casos práticos
Um número de casos práticos que mostra como o Service Fabric é utilizado para a estrutura de aplicações é publicado no [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) e [site de soluções micro-serviços](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Conceber aplicações compostas micro-serviços sem monitorização de estado e com monitorização de estado
Criar aplicações com as funções de trabalho do serviço de nuvem do Azure é um exemplo de um serviço sem estado. Em contrapartida, micro-serviços com monitorização de estado mantêm o respetivo estado autoritativo para além do pedido e a respetiva resposta. Esta opção fornece elevada disponibilidade e a consistência do Estado através de APIs simples que fornecem transacionais garantias de segurança através da replicação. Serviços de monitorização de estado de Service Fabric democratize a elevada disponibilidade, colocá-lo a todos os tipos de aplicações, não apenas bases de dados e outros arquivos de dados. Esta é uma progressão natural. As aplicações já terem da utilização de bases de dados relacionais puramente para elevada disponibilidade para bases de dados NoSQL. Agora as aplicações próprios podem ter o respetivo estado "ativos" e os dados geridos dentro delas para ganhos de desempenho adicionais, sem sacrificar fiabilidade, a consistência ou a disponibilidade.

Quando a criação de aplicações consiste em micro-serviços, normalmente, tem uma combinação de aplicações web sem monitorização de estado (ASP.NET, Node.js, etc.) chamar nos serviços de camada média de empresariais sem monitorização de estado e com monitorização de estado, implementados no mesmo cluster Service Fabric utilizando os comandos de implementação do Service Fabric. Cada um destes serviços é independente em relação a utilização de recurso, fiabilidade e escala, melhorando em grande medida agilidade no ciclo de vida de desenvolvimento e gestão.

Com monitorização de estado micro-serviços simplificam estruturas de aplicações porque podem remover a necessidade do filas adicionais e caches tradicionalmente tem sido necessários para abordar os requisitos de disponibilidade e a latência das aplicações puramente sem estado. Uma vez que os serviços com monitorização de estado naturalmente estão altamente disponíveis e baixa latência, isto significa que existem menos partes mover para gerir na sua aplicação como um todo. Os diagramas abaixo mostram as diferenças entre a conceber uma aplicação sem monitorização de estado e outro que é com monitorização de estado. Ao tirar partido do [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md) modelos de programação, serviços com monitorização de estado reduzir a complexidade da aplicação ao alcançar o débito alto e baixa latência.

## <a name="an-application-built-using-stateless-services"></a>Uma aplicação construída utilizando os serviços sem monitorização de estado
![Aplicação utilizando o serviço sem monitorização de estado][Image1]

## <a name="an-application-built-using-stateful-services"></a>Uma aplicação construída utilizando os serviços de monitorização de estado
![Aplicação utilizando o serviço sem monitorização de estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes

* Escutar [casos práticos de clientes](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
)
* Leia sobre [casos práticos de clientes](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Saiba mais sobre [padrões e cenários](service-fabric-patterns-and-scenarios.md)

* Introdução à criação de serviços sem monitorização de estado e com monitorização de estado com o Service Fabric [serviços fiáveis](service-fabric-reliable-services-quick-start.md) e [reliable actors](service-fabric-reliable-actors-get-started.md) modelos de programação.
* Consulte também os tópicos seguintes:
  * [Conte-me micro-serviços](service-fabric-overview-microservices.md)
  * [Definir e gerir o estado do serviço](service-fabric-concepts-state.md)
  * [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
  * [Serviços do Service Fabric de escala](service-fabric-concepts-scalability.md)
  * [Serviços do Service Fabric de partição](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
