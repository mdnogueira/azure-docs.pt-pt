---
title: "Questões recorrentes sobre o Microsoft Azure Service Fabric | Microsoft Docs"
description: Perguntas mais frequentes sobre o Service Fabric e as respetivas respostas
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: ee334186dffaa1f88cf05717b6a5ba1e819a8cdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="commonly-asked-service-fabric-questions"></a>Normalmente mais frequentes sobre o Service Fabric

Existem muitos perguntas mais comuns sobre o Service Fabric pode fazer e como devem ser utilizado. Este documento inclui muitas desses perguntas comuns e as respetivas respostas.

## <a name="cluster-setup-and-management"></a>Configuração do cluster e gestão

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Pode criar um cluster que abrange várias regiões do Azure ou o meus próprio centros de dados?

Sim. 

As principais tecnologias de clustering de Service Fabric podem ser utilizada para combinar máquinas em execução em qualquer lugar do mundo, desde que têm conectividade de rede entre si. No entanto, criar e executar cluster em questão podem ser complicadas.

Se estiver interessado neste cenário, Aconselhamo-lo para obter na contacte ou através de [lista de problemas do Service Fabric Github](https://github.com/azure/service-fabric-issues) ou através do seu representante de suporte para obter orientações adicionais. A equipa de Service Fabric está a trabalhar para fornecer os efeitos de clareza adicionais, diretrizes e recomendações para este cenário. 

Alguns aspetos a ter em conta: 

1. O recurso de cluster do Service Fabric no Azure é regional hoje em dia, pois que o dimensionamento da máquina virtual define que o cluster está incorporado no. Isto significa que, se ocorrer uma falha regional poderá perder a capacidade de gerir o cluster através do Azure Resource Manager ou o Portal do Azure. Isto pode acontecer, apesar do cluster permanece em execução e deverá ser capazes de interagir com ele diretamente. Além disso, Azure hoje não oferecem a capacidade de ter uma única rede virtual que pode ser utilizada em regiões. Isto significa que um cluster de multirregião no Azure requer um [endereços IP públicos para cada VM em conjuntos de dimensionamento VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) ou [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Estas opções de rede têm impactos diferentes em custos, desempenho, e a estrutura de aplicação algumas grau, cuidado, por isso, análise e planeamento é necessário antes de colocado configurar um ambiente deste tipo.
2. A manutenção, gestão e monitorização destas máquinas pode tornar-se complexa, especialmente quando abrangido em _tipos_ dos ambientes, como impedindo fornecedores de nuvem diferente ou entre recursos no local e o Azure. Deve ter cuidado para garantir que as atualizações, monitorização, gestão e diagnóstico é compreendido para o cluster e as aplicações antes de executar cargas de trabalho de produção num ambiente deste tipo. Se já tiver muitos experiência de resolução desses problemas no Azure ou nos seus centros de dados, em seguida, é provável que essas soluções mesmas podem ser aplicadas quando a criação de saída ou em execução no cluster do Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Nós de Service Fabric automaticamente a receber atualizações do SO?

Não hoje em dia, mas também se trata de um pedido comuns que Azure pretende fornecer.

Provisório, temos [fornecida uma aplicação](service-fabric-patch-orchestration-application.md) que os sistemas operativos abaixo os nós de Service Fabric permaneça corrigido e atualizado.

O desafio com atualizações do SO é que, normalmente, requer um reinício do computador, o que resultará na perda de disponibilidade temporário. Por si só, que não é um problema, uma vez que o Service Fabric automaticamente irá redirecionar o tráfego para esses serviços para outros nós. No entanto, se a atualizações do SO não são coordenadas no cluster, é o potencial que vários nós forem abaixo de uma só vez. Essas reinícios simultâneos podem causar a perda de disponibilidade completa para um serviço ou, no mínimo, para uma partição específica (para um serviço de monitorização de estado).

No futuro, planeamos suportar uma política de atualização de SO que está totalmente automatizada e coordenada entre domínios de atualização, garantindo que a disponibilidade é mantida apesar reinícios e outras falhas inesperadas.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Pode utilizar grandes conjuntos de dimensionamento de Máquina Virtual em cluster os meus SF? 

**Resposta de curta** - No. 

**Responder a tempo** - embora a grandes conjuntos de dimensionamento de Máquina Virtual permitem-lhe dimensionar uma máquina virtual dimensionar as instâncias VM do conjunto até 1000, isto é feito com a utilização de grupos de posicionamento (PGs). Domínios de falhas (FDs) e domínios de atualização (UDs) apenas são consistentes dentro de um posicionamento grupo Service fabric utiliza FDs e UDs para tomar decisões de colocação das instâncias de serviço/réplicas do serviço. Uma vez que o FDs e UDs comparáveis apenas dentro de um grupo de posicionamento SF não pode ser utilizada. Por exemplo, se VM1 no PG1 tem uma topologia do DF = 0 e VM9 no PG2 tem uma topologia do DF = 4, não significa que VM1 e VM2 estiverem em dois bastidores Hardware diferente, por conseguinte, SF não podem utilizar os valores de DF neste caso para tomar decisões de colocação.

Existem outros problemas com conjuntos de dimensionamento de máquina virtual grande, como a falta de nível 4 suporte balanceamento de carga. Consulte para [detalhes em grande escala conjuntos](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>O que é o tamanho mínimo de um cluster do Service Fabric? Por que motivo não pode ser mais pequeno?

O tamanho mínimo suportado para um cluster do Service Fabric executar cargas de trabalho de produção é cinco nós. Para cenários de desenvolvimento/teste, iremos suportar três clusters de nó.

Estes mínimos existem porque o cluster do Service Fabric executa um conjunto de serviços de monitorização de estado do sistema, incluindo o serviço de atribuição de nomes e o Gestor de ativação pós-falha. Estes serviços controlar os serviços que foram implementados para o cluster e onde está atualmente alojados, dependem da consistência forte. Esse consistência forte, por sua vez, depende a capacidade de adquirir uma *quórum* para qualquer atualização especificada para o estado desses serviços, onde um quórum representa uma maioria das réplicas (N/2 + 1) para um determinado serviço rigorosa.

Com esse em segundo plano, vamos examinar algumas configurações de cluster possíveis:

**Um nó**: esta opção fornece elevada disponibilidade desde a perda de nó único para de qualquer motivo significa a perda de todo o cluster.

**Dois nós**: um quórum de um serviço implementado através de dois nós (N = 2) é 2 (2/2 + 1 = 2). Quando uma única réplica se tenha perdida, é impossível criar um quórum. Uma vez que executar uma atualização de serviço requer temporariamente demorar para baixo de uma réplica, esta configuração não é útil.

**Três nós**: com três nós (N = 3), o requisito para criar um quórum ainda está a dois nós (3/2 + 1 = 2). Isto significa que pode perder um nó individual e ainda manter o quórum.

A configuração de cluster do três nó é suportada para dev/test porque pode efetuar atualizações em segurança e continuam a vigorar após falhas de nó individual, desde não acontecer em simultâneo. Para cargas de trabalho de produção, tem de ser resilientes a essa uma falha simultânea, pelo que são necessários nós de cinco.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Posso desativar os meus cluster em noite/semana para reduzir os custos?

Geralmente, não. Service Fabric armazena o estado de discos locais, efémeras, que significa que se a máquina virtual for movida para outro anfitrião, os dados não move com a mesma. Em funcionamento normal, que não é um problema como o novo nó seja colocado atualizado por outros nós. No entanto, se parar todos os nós e reiniciá-las mais tarde, há a possibilidade de significativa que a maioria de nós começam em novos anfitriões e disponibilizar o sistema não consegue recuperar.

Se gostaria de criar clusters para testar a aplicação antes do que é implementado, recomendamos que crie dinamicamente esses clusters como parte da sua [integração contínua/contínua pipeline de implementação](service-fabric-set-up-continuous-integration.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Como atualizar o meu sistema operativo (por exemplo, do Windows Server 2012 para o Windows Server 2016)?

Enquanto que estamos a trabalhar uma experiência melhorada, hoje em dia, o utilizador é responsável pela atualização. Tem de atualizar a imagem do SO nas máquinas virtuais de cluster de uma VM a uma hora. 

## <a name="container-support"></a>Suporte de contentor

### <a name="why-are-my-containers-that-are-deployed-to-sf-unable-to-resolve-dns-addresses"></a>Por que razão são os meus contentores que são implementadas para SF não é possível resolver endereços DNS?

Este problema foi comunicado em clusters que se encontram num 5.6.204.9494 versão 

**Mitigação** : siga [neste documento](service-fabric-dnsservice.md) para ativar o serviço de recursos de infraestrutura de serviço DNS no seu cluster.

**Corrigir** : atualização para uma versão de cluster suportadas é superior ao 5.6.204.9494, quando estiver disponível. Se o cluster estiver definido como as atualizações automáticas, em seguida, o cluster irá atualizar automaticamente para a versão que tem este problema corrigido.

  
## <a name="application-design"></a>Design da aplicação

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>O que é a melhor forma de consultar os dados entre as partições de uma coleção fiável?

Coleções fiáveis são normalmente [particionada](service-fabric-concepts-partitioning.md) para ativar a ampliação de um desempenho superior e débito. Isto significa que o estado para um determinado serviço pode ser distribuído por 10s ou 100s de máquinas. Para executar operações sobre esse conjunto de dados completo, tem algumas opções:

- Crie um serviço que consulta a todas as partições de outro serviço para obter os dados necessários.
- Crie um serviço que pode receber dados de todas as partições de outro serviço.
- Enviar dados periodicamente de cada serviço para um arquivo de externo. Esta abordagem apenas é apropriada se as consultas que estiver a efetuar não fazem parte da lógica de negócio core.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>O que é a melhor forma de consultar os dados na minha atores?

Atores foram concebidos para serem independentes unidades de estado e computação, pelo que não é recomendado para executar consultas abrangentes do Estado de ator em tempo de execução. Se tiver uma necessidade de consulta entre o conjunto completo de estado de ator, deve considerar o:

- Substituir os serviços de atores com os serviços fiáveis com monitorização de estado, para que o número de rede pedidos para recolher todos os dados do número de atores para o número de partições no seu serviço.
- Conceber a sua atores para emitir periodicamente o respetivo estado para uma loja externa para consultar mais fácil. Como acima, esta abordagem apenas é viável se as consultas que estiver a efetuar não são necessárias para o comportamento de tempo de execução.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quantidade de dados pode armazenar numa coleção fiável

Serviços fiáveis normalmente estiverem particionados, pelo que a quantidade que pode armazenar apenas é limitada, o número de máquinas que ter no cluster e a quantidade de memória disponível dessas máquinas.

Por exemplo, suponha que tem uma coleção fiável num serviço com 100 partições e 3 réplicas, armazenar os objetos que o tempo médio de 1kb de tamanho. Agora, suponha que tem um cluster de 10 máquina com 16gb de memória por máquina. De simplicidade e para ser muito conservador, partem do princípio de que o sistema operativo e serviços do sistema, o tempo de execução do Service Fabric e os serviços de consumam 6gb de que, deixando de 10gb disponíveis por computador ou 100gb para o cluster.

Manter em atenção que cada objeto tem de ser armazenados três horas (um site primário e duas réplicas), terá de memória suficiente para cerca de 35 milhões de objetos na sua coleção quando funciona na capacidade total. No entanto, recomendamos que está a ser resiliente à perda de um domínio de falhas e de um domínio de atualização, que representa cerca de 1/3 da capacidade e pretende reduzir o número para aproximadamente 23 milhões em simultâneo.

Tenha em atenção que este cálculo também parte do princípio de:

- Se a distribuição de dados entre as partições é aproximadamente uniform ou que que está a denunciar métricas de carga para o Gestor de recursos do Cluster. Por predefinição, Service Fabric será o balanceamento de carga com base na contagem de réplica. No nosso exemplo acima, que pretende colocar 10 réplicas primárias e 20 réplicas secundárias em cada nó no cluster. Funciona bem para a carga que é distribuída uniformemente pelas partições. Se a carga não é mesmo, tem de comunicar carga para que o Gestor de recursos possam pacote réplicas mais pequenas em conjunto e permitir que as réplicas maior consuma mais memória num nó individual.

- Que o serviço fiável em questão é o apenas um Estado armazenar no cluster. Uma vez que pode implementar vários serviços para um cluster, tem de ser mindful dos recursos que cada terá de executar e gerir o seu estado.

- Se o próprio cluster não está a crescer ou reduzir. Se adicionar mais máquinas, o Service Fabric será rebalancear as réplicas para tiram partido da capacidade de adicional até que o número de máquinas excede o número de partições no seu serviço, uma vez que uma réplica individuais não pode abranger máquinas. Por outro lado, se reduzir o tamanho do cluster, removendo máquinas, as réplicas serão packed mais rigidamente e tem menos capacidade global.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quantidade de dados pode armazenar num ator

Tal como acontece com os serviços fiável, a quantidade de dados que podem armazenar num serviço ator apenas é limitada pela espaço total em disco e memória disponível em todos os nós do cluster. No entanto, atores individuais são mais eficazes quando são utilizados para encapsular uma pequena quantidade de estado e lógica de negócio associado. Regra geral, um ator individuais deve ter o estado é medido em quilobytes.

## <a name="other-questions"></a>Outras questões

### <a name="how-does-service-fabric-relate-to-containers"></a>Como é que Service Fabric se relacionam com contentores?

Contentores oferecem uma forma simples de serviços de pacote e as respetivas dependências, para que possam executam de forma consistente em todos os ambientes e podem funcionar de forma isolada num único computador. Recursos de infraestrutura de serviço oferece uma forma de implementar e gerir serviços, incluindo [serviços que foram empacotados num contentor](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Está a planear abrir a origem de Service Fabric?

Pretendemos abrir as estruturas de reliable actors no GitHub e serviços fiáveis de origem e aceitará contribuições da Comunidade para os projetos. Siga o [blogue de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para obter mais detalhes, como é que está a ser anunciados.

O estão atualmente não planos para abrir origem o tempo de execução do Service Fabric.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre conceitos de Service Fabric principais e melhores práticas](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
