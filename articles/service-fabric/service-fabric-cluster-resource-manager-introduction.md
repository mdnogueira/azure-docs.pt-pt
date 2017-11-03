---
title: "O Gestor de recursos de Cluster do serviço de recursos de infraestrutura de introdução | Microsoft Docs"
description: "Uma introdução para o serviço de recursos de infraestrutura Cluster Gestor de recursos."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3e8cd4dc8e960e38ba0e4a9a195b2f61d9ec1924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduzir o Gestor de recursos de cluster do Service Fabric
Tradicionalmente gerir os sistemas de TI ou serviços online pretendem dedicação física específica ou de máquinas virtuais a esses serviços específicos ou sistemas. Foram criados como camadas de serviços. Poderia ser uma camada de "web" e uma camada de "dados" ou "armazenamento". As aplicações seriam ter uma camada de serviço de mensagens onde pedidos em fluxo e terminar, bem como um conjunto de máquinas dedicadas a colocação em cache. Cada camada ou tipo de carga de trabalho tinha máquinas específicas dedicadas para a mesma: a base de dados obteve máquinas alguns dedicadas, os servidores web a poucos. Se as máquinas que estava de surgir um determinado tipo de carga de trabalho execute demasiado frequente, em seguida, adicionar mais máquinas com essa mesma configuração para essa camada. No entanto, nem todas as cargas de trabalho podem ser ampliadas, por isso, facilmente - particularmente com a camada de dados normalmente seriam substituir máquinas com máquinas maiores. Fácil. Se uma máquina falhou, que parte da aplicação global ficou inferior capacidade até que a máquina pode ser restaurada. Ainda bastante fácil (se não necessariamente diversão).

Agora no entanto o universo de arquitetura de serviço e o software foi alterada. É mais comum que aplicações ADOTARAM uma estrutura de escalamento horizontal. Criar aplicações com contentores ou micro-serviços (ou ambos) é comum. Agora, embora ainda possa ter apenas alguns máquinas, não se estiver a executar apenas uma única instância de uma carga de trabalho. Estes podem mesmo estar a executar várias cargas de trabalho diferentes ao mesmo tempo. Tem agora dezenas de tipos diferentes de serviços (nenhum consumir visão de uma máquina total de recursos), talvez centenas de várias instâncias desses serviços. Cada instância nomeada tem uma ou mais instâncias ou réplicas para elevada disponibilidade (ed). Consoante os tamanhos dessas cargas de trabalho e como ocupado são, pode considerar-se com centenas ou milhares de máquinas. 

Subitamente a gerir o ambiente não é por isso, simple como gerir alguns máquinas dedicadas para tipos individuais de cargas de trabalho. Os servidores são virtuais e deixará de ter nomes (terem mudado mindsets de [pets para cattle](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) após todos os). A configuração é menor sobre as máquinas e muito mais sobre os serviços de si próprios. Hardware que está dedicado a uma única instância de uma carga de trabalho é amplamente um passado de the do coisa do. Serviços próprios ficou pequenos sistemas distribuídos que abrangem várias partes mais pequenas do hardware do produto.

Porque a aplicação já não é uma série de monoliths distribuídos por várias camadas, tem agora as combinações mais muitas para lidar com. Que decide que tipos de cargas de trabalho podem ser executados no qual hardware ou quantos? Que cargas de trabalho funcionam bem no mesmo hardware e que estão em conflito? Quando uma máquina entra baixo como fazer sabe o que estava a ser executado existe nessa máquina? Quem é responsável pela certificando-se de que carga de trabalho começa a ser executado novamente? Aguarde para a máquina (virtual?) para voltar atrás ou as cargas de trabalho automaticamente a ativação pós-falha para outras máquinas e manter em execução? É necessária qualquer intervenção humana? O que sobre atualizações neste ambiente?

Como os programadores e operadores lidar neste ambiente, vamos pretende ajuda a gerir este complexidade. Um binge de contratação e tentar ocultam a complexidade com pessoas provavelmente, não é a resposta à direita, por isso, o que podemos fazer?

## <a name="introducing-orchestrators"></a>Introdução ao orchestrators
Um "Orchestrator" é o termo geral de uma peça de software que ajuda os administradores gerir estes tipos de ambientes. Orchestrators são os componentes que tomar em pedidos, tal como "Gostaria de cinco cópias deste serviço em execução no meu ambiente." Se tentarem efetuar o ambiente corresponder o estado pretendido, independentemente do que acontece.

Orchestrators (não humans) são que tome quando uma máquina falha ou uma carga de trabalho termina por alguma razão inesperado. A maioria das orchestrators mais do que apenas lidam com uma falha. Outras funcionalidades têm estiver a gerir novas implementações, atualizações de processamento e lidar com consumo de recursos e governação. Todos os orchestrators fundamentalmente são sobre a manutenção algum estado pretendido de configuração no ambiente. Pretende para saber um orchestrator que pretende e que efetue a lifting pesado. Aurora por cima do Mesos, Docker Datacenter/Docker Swarm, Kubernetes e recursos de infraestrutura de serviço são todos os exemplos de orchestrators. Estes orchestrators estão a ser ativamente desenvolvidas para satisfazer as necessidades das cargas de trabalho reais em ambientes de produção. 

## <a name="orchestration-as-a-service"></a>Orquestração como um serviço
O Gestor de recursos do Cluster é o componente do sistema que processa orquestração no Service Fabric. Tarefa do Gestor de recursos de Cluster está a ser dividida em três partes:

1. Impor regras
2. Otimizar o seu ambiente
3. Ajudar com outros processos

Para ver como funciona o Gestor de recursos de Cluster, veja o vídeo seguinte do Microsoft Virtual Academy:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>O que não se encontra
Em aplicações de camada de N tradicionais, há sempre um [Balanceador de carga](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Normalmente, isto era um balanceador de carga de rede (NLB) ou um balanceador de carga de aplicação (ALB), dependendo de onde DOM na pilha de rede. Alguns balanceadores de carga baseada em Hardware como oferta de BigIP F5, outros são baseadas em software, tais como Microsoft do NLB. Nos outros ambientes, poderá ver algo semelhante HAProxy, nginx, Istio ou Envoy nesta função. Estes arquiteturas, a tarefa de balanceamento de carga consiste em garantir cargas de trabalho sem monitorização de estado (aproximadamente) recebem a mesma quantidade de trabalho. Estratégias para balanceamento de carga variadas. Alguns balanceadores enviaria cada chamada diferente para um servidor diferente. Outras pessoas fornecido afixação/persistência da sessão. Balanceadores de mais avançadas utilizam estimativa de carregamento atual ou relatórios para encaminhar uma chamada com base na respetiva esperado custo e a carga de máquina atual.

Balanceadores de rede ou routers de mensagem tentem Certifique-se de que a camada web/trabalho permanecer aproximadamente equilibrada. Estratégias para balanceamento da camada de dados foram diferente e dependência o mecanismo de armazenamento de dados. A camada de dados de balanceamento relied no fragmentação de dados, a colocação em cache, geridas vistas, procedimentos armazenados e outros mecanismos de arquivo específicos.

Apesar de algumas das estratégias destes serem interessantes, o Gestor de recursos de Cluster do serviço de recursos de infraestrutura não é algo como um balanceador de carga de rede ou de uma cache. Um balanceador de carga de rede equilibra frontends pelo tráfego entre frontends. O Gestor de recursos de Cluster do Service Fabric tem uma estratégia de diferentes. Fundamentalmente, o Service Fabric move *serviços* para onde efetuar mais sentido, era esperado o tráfego ou carregar a seguir. Por exemplo, este poderá mover serviços a nós que não são atualmente amovíveis porque os serviços que existem não estão a fazer muito trabalho. Os nós podem ser amovíveis, uma vez que os serviços que estavam presentes foram eliminados ou movidos noutro local. Outro exemplo: o Gestor de recursos de Cluster também foi possível mover um serviço para fora de uma máquina. Talvez a máquina está prestes a ser atualizado, ou está sobrecarregado devido a um pico de pedidos de consumo pelos serviços em execução no mesmo. Podem ter aumentado Alernatively, requisitos de recursos do serviço. Como resultado não sabemos de recursos suficientes neste computador para continuar a executá-lo. 

Porque o Gestor de recursos do Cluster é responsável por serviços à volta de mover, contém um conjunto de funcionalidades diferente em comparação comparado o poderia localizar um balanceador de carga de rede. Isto acontece porque os balanceadores de carga de rede fornecem o tráfego de rede onde serviços já estão, mesmo que a localização não é ideal para executar o serviço de si próprio. O Gestor de recursos de Cluster do Service Fabric emprega fundamentalmente diferentes estratégias para se certificar de que os recursos no cluster são utilizados de forma eficiente.

## <a name="next-steps"></a>Passos seguintes
- Para obter informações sobre o fluxo de informações e arquitetura dentro do Gestor de recursos do Cluster, consulte [neste artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para saber mais sobre as métricas, consulte este artigo no [que descrevem um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre como configurar serviços](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- As métricas são como o serviço de recursos de infraestrutura Cluster Resource Manager gere consumo e capacidade do cluster. Para saber mais sobre as métricas e como configurá-las modificações [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Gestor de recursos de Cluster funciona com capacidades de gestão do Service Fabric. Para obter mais informações sobre essa integração, leia [neste artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para obter informações sobre a forma como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte o artigo em [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
