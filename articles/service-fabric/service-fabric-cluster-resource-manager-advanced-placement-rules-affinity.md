---
title: "Gestor de recursos de Cluster de recursos de infraestrutura de serviço - afinidade | Microsoft Docs"
description: "Descrição geral de configuração de afinidade para serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8122f1644da25a9a2ab05291dafc33c77a91147f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurar e utilizar a afinidade de serviço no Service Fabric
Afinidade é um controlo que é fornecido principalmente para ajudar a facilitar a transição das aplicações monolithic superiores para o universo de nuvem e micro-serviços. Também é utilizado como uma otimização para melhorar o desempenho dos serviços, embora se o fizer, pode ter efeitos secundários.

Imaginemos que está a colocar uma aplicação maior, ou que não foi concebido apenas com micro-serviços em mente, para o Service Fabric (ou qualquer ambiente distribuído). Este tipo de transição é comum. Comece por lifting a aplicação completa para o ambiente, o empacotamento-la, e tornando se está em execução facilmente. Em seguida, iniciar danificando a para baixo para diferentes serviços mais pequenos que todos os com entre si.

Eventualmente, pode constatar que a aplicação está a ter alguns problemas. Os problemas normalmente enquadram-se uma destas categorias:

1. Alguns componentes X na aplicação monolithic tinham uma dependência não documentada no componente Y e apenas transformado esses componentes serviços separados. Uma vez que estes serviços são agora em execução em nós diferentes no cluster, está danificadas.
2. Estes componentes comunicam através de (local pipes nomeado | memória partilhada | ficheiros no disco) e realmente têm de conseguir escrever neste momento a um recurso local partilhado por motivos de desempenho. Que dependência rígida obtém removida mais tarde, talvez.
3. Tudo é adequado, mas, fica que estes dois componentes são realmente chatty/desempenho confidencial. Quando movidos-las para os serviços de separado global tanked do desempenho de aplicações ou latência aumentada. Como resultado, a aplicação global não está a cumprir expetativas.

Nestes casos, iremos não deseja perder o nosso trabalho refactoring e não quiser voltar para o monolith. A condição último mesmo pode ser preferível como uma simples otimização. No entanto, até que pode redesenhar os componentes naturalmente funcione como serviços (ou até que pode resolver as expectativas de desempenho outro modo) vamos precisar de alguns sentido de localidade.

O que fazer? Bem, tentar ativar afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar a afinidade
Para configurar a afinidade, é possível definir uma relação de afinidade entre dois serviços diferentes. Pode considerar afinidade como "aponta" um serviço em outro e indicar "este serviço só pode ser executada em que o serviço está em execução." Por vezes, denominamos afinidade uma relação principal/subordinado (onde que ponto o elemento subordinado no principal). A afinidade assegura que as réplicas ou instâncias de um serviço são colocadas em nós do mesmos como de outro serviço.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Um serviço de subordinados só pode participar numa relação de afinidade único. Se pretendesse subordinado para ser com afinidade com dois serviços de principal de uma só vez tem algumas opções:
> - Inverter as relações (ter parentService1 e parentService2 apontar para o atual serviço de subordinados), ou
> - Designar uma os principais como um concentrador por convenção e têm todos os serviços ponto em que o serviço. 
>
> O comportamento resultante de colocação de cluster deve ser o mesmo.
>

## <a name="different-affinity-options"></a>Opções de afinidade diferentes
Afinidade é representada por um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comuns de afinidade é o que apelidamos NonAlignedAffinity. NonAlignedAffinity, as réplicas ou instâncias dos serviços diferentes são colocadas em nós do mesmos. O modo de outro é AlignedAffinity. Afinidade alinhada é útil apenas com os serviços com monitorização de estado. Configurar dois serviços com monitorização de estado a ter alinhadas afinidade assegura que o primaries desses serviços são colocadas em nós do mesmos como entre si. Também faz com que cada par de bases de dados secundárias para esses serviços à colocadas em nós do mesmos. Também é possível (embora menos comum) para configurar NonAlignedAffinity para serviços com monitorização de estado. Para NonAlignedAffinity, as réplicas diferentes dos dois serviços com monitorização de estado iriam executar em nós do mesmos, mas os respetivos primaries podem acabar em nós diferentes.

<center>
![Modos de afinidade e respetivos efeitos][Image1]
</center>

### <a name="best-effort-desired-state"></a>Melhor estado de esforço pretendido
Uma relação de afinidade é melhor esforço. Não fornece as mesmas garantias de colocalização ou fiabilidade que em execução no mesmo processo executável. Os serviços de uma relação de afinidade são entidades fundamentalmente diferentes que podem falhar e a ser movidas de forma independente. Também poderá interromper uma relação de afinidade, embora estes quebras de linha são temporárias. Por exemplo, as limitações de capacidade podem significar que apenas alguns dos objetos de serviço na relação de afinidade podem caber num determinado nó. Nestes casos, mesmo apesar de não existir uma relação de afinidade no local, este não pode ser forçada devido as outras restrições. Se for possível para o fazer, a violação seja automaticamente corrigida mais tarde.

### <a name="chains-vs-stars"></a>Cadeias vs estrelas
Hoje o Gestor de recursos de Cluster não é capaz de cadeias de modelo de relações de afinidade. Isto significa que um serviço que é um subordinado de uma relação de afinidade não pode ser um elemento principal noutra relação de afinidade. Se pretender este tipo de relação do modelo, tiver efetivamente modelá-la como uma estrela, em vez de uma cadeia. Mover de uma cadeia a uma estrela, o subordinado mais inferiores teria de ser associado a principal o primeiro subordinado em vez disso. Consoante a disposição dos seus serviços, poderá ter de fazer neste várias vezes. Se não houver nenhum serviço natural principal, poderá ter de criar um que funciona como um marcador de posição. Dependendo dos requisitos, pode também pretender procurar em [grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Vs de cadeias. Nas estrelas no contexto de relações de afinidade][Image2]
</center>

Outra coisa a ter em atenção hoje sobre relações de afinidade é que estão direcional. Isto significa que a regra de afinidade impõe apenas que o elemento subordinado colocada com o elemento principal. Não garante que o elemento principal está localizado com o subordinado. Também é importante ter em atenção que a relação de afinidade não pode ser perfect ou instantaneamente forçada uma vez que os diferentes serviços com os ciclos de vida de diferentes e podem falhar e mover de forma independente. Por exemplo, vamos supor que o elemento principal subitamente a ativação pós-falha para outro nó porque esta falhou. Processam, o Gestor de recursos do Cluster e o Gestor de ativação pós-falha, a ativação pós-falha em primeiro lugar, desde a manter os serviços de cópias de segurança, consistentes, e a prioridade de está disponível. Uma vez concluída a ativação pós-falha, a relação de afinidade é interrompida, mas o Gestor de recursos de Cluster pensa tudo o que é adequado até-avisos que não encontra o subordinado com o elemento principal. Estes tipos de verificações são executados periodicamente. Obter mais informações sobre como o Gestor de recursos de Cluster avalia restrições estão disponíveis no [neste artigo](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), e [este](service-fabric-cluster-resource-manager-balancing.md) sua mais informações sobre como configurar a cadência no qual são destas restrições avaliada.   


### <a name="partitioning-support"></a>Suporte de criação de partições
A final coisa a reparar sobre a afinidade é essa afinidade não são suportadas relações em que o elemento principal está particionado. Serviços particionada principal podem ser suportados, eventualmente, mas atualmente não é permitido.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para limitar os serviços para um pequeno conjunto de máquinas ou agregar a carga de serviços, utilize [grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png