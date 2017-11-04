---
title: "Disponibilidade dos serviços do Service Fabric | Microsoft Docs"
description: "Descreve a deteção de falhas, a ativação pós-falha e a recuperação de serviços"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6c23a56df48434db3b82bce70cbd3a23941a077a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="availability-of-service-fabric-services"></a>Disponibilidade dos serviços do Service Fabric
Este artigo fornece uma descrição geral de como o Azure Service Fabric mantém a disponibilidade de um serviço.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade dos serviços sem monitorização de estado do Service Fabric
Serviços do Service Fabric podem ser com monitorização de estado ou sem monitorização de estado. Um serviço sem monitorização de estado é um serviço de aplicações que não tem um [estado local](service-fabric-concepts-state.md) que tem de ser fiável ou altamente disponível.

A criação de um serviço sem estado requer a definir um `InstanceCount`. A contagem de instâncias define o número de instâncias de lógica de aplicação do serviço sem monitorização de estado que deve estar em execução no cluster. Aumentar o número de instâncias é a forma recomendada de ampliar um serviço sem estado.

Quando uma instância de um sem monitorização de estado com o nome de um serviço falha, é criada uma nova instância num nó do cluster elegível. Por exemplo, uma instância de serviço sem monitorização de estado pode falhar no Nó1 e ser recriada no Nó5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade dos serviços de monitorização de estado de Service Fabric
Um serviço com estado tem um Estado associado. No Service Fabric, um serviço com estado é modelado como um conjunto de réplicas. Cada réplica é uma instância em execução do código do serviço. A réplica tem também uma cópia do Estado para que o serviço. Ler e escrever operações são efetuadas a uma réplica, denominada o *primário*. As operações de escrita de alterações para o estado de *replicado* para as outras réplicas no conjunto de réplicas, chamado *secundárias Active Directory*e aplicada. 

Podem existir apenas uma réplica primária, mas podem existir várias réplicas secundárias do Active Directory. O número de réplicas secundárias Active Directory é configurável e um número mais elevado de réplicas pode tolerar um maior número de falhas de hardware e software em simultâneo.

Se a réplica primária ficar inativo, Service Fabric faz uma das réplicas secundárias do Active Directory primário nova réplica. Esta réplica secundária, o Active Directory já tem a versão atualizada do Estado, através de *replicação*, e pode continuar a processar mais operações de leitura/escrita. Este processo é conhecido como *reconfiguração* e é descrito mais no [reconfiguração](service-fabric-concepts-reconfiguration.md) artigo.

O conceito de uma réplica a ser um principal ou secundária ativa, é conhecido como o *função de réplica*. Estas réplicas são descritas mais no [réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md) artigo. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Service Fabric, consulte os artigos seguintes:

- [Dimensionamento serviços do Service Fabric](service-fabric-concepts-scalability.md)
- [A criação de partições de serviços do Service Fabric](service-fabric-concepts-partitioning.md)
- [Definir e gerir o Estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

