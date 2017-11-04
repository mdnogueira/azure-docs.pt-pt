---
title: "Custo de movimento do Gestor de recursos do Cluster de recursos de infraestrutura do serviço: | Microsoft Docs"
description: "Descrição geral do custo de movimento para serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3063647671fea94da3ce635b887f6f0f7de89f70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-movement-cost"></a>Custo de movimento de serviço
Um fator que considera que o Gestor de recursos de Cluster do serviço de recursos de infraestrutura ao tentar determinar o que é alterado para fazer a um cluster é o custo dessas alterações. A noção de "Custo" é transacionada desativar contra quanto o cluster pode ser melhorado. Custo é factored ao mover os serviços para balanceamento, desfragmentação e outros requisitos. O objetivo é para cumprir os requisitos da forma menos acontece ou dispendiosa. 

Mover o tempo de CPU de custos de serviços e largura de banda de um mínimo de rede. Para os serviços com monitorização de estado, necessita de copiar o estado desses serviços, consumindo memória adicional e disco. Minimizar o custo das soluções que surge o Gestor de recursos de Cluster do Azure Service Fabric ajuda a garantir a que os recursos do cluster não são despende desnecessariamente. No entanto, também não pretender ignorar soluções que seriam significativamente melhorar a alocação de recursos no cluster.

O Gestor de recursos do Cluster tem duas formas de custos de computação e de limitação-los ao tentar gerir o cluster. O mecanismo de primeira é simplesmente contando cada mover que iria tornar. Se duas soluções são geradas com sobre os mesmos equilibrar (pontuação), em seguida, o Gestor de recursos de Cluster prefers um com o menor custo (número total de move).

Esta estratégia funciona bem. Mas, tal como acontece com predefinido ou cargas estáticas, é pouco provável em qualquer sistema complexo que move todos os é igual. Algumas prováveis que estão a ser muito mais dispendiosos.

## <a name="setting-move-costs"></a>Custos de movimentação de definição 
Pode especificar a predefinição mover o custo de um serviço, quando é criado:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Também pode especificar ou atualizar MoveCost dinamicamente para um serviço depois de criar o serviço: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Especificar dinamicamente o custo de movimentação numa base por réplica

Os fragmentos anteriores são todos os sobre como especificar MoveCost para um serviço todo em simultâneo a partir do fora o próprio serviço. No entanto, custo é mais útil é mover quando altera o custo de movimentação de um objeto de serviço específicos ao longo do respetivo tempo de vida. Uma vez que os serviços próprios provavelmente têm a melhor ideia dos dispendiosas como estão a mover num determinado momento, não há uma API para os serviços para os seus próprios indivíduo mover custo durante o tempo de execução do relatório. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto de custo de movimentação
MoveCost tem quatro níveis: Zero, baixa, média e alta. MoveCosts são relativamente à entre si, exceto para Zero. Zero custo de movimentação significa que o movimento está livre e não deve contar contra o modelo de pontuação da solução. Definir a movimentação de custos para alta *não* garantia de que a réplica permanece num único local.

<center>
![Custo de movimentação como um fator na seleção de réplicas de movimento][Image1]
</center>

MoveCost ajuda-o a encontrar soluções que fazer com que a interrupção, pelo menos, geral e que são mais fácil alcançar enquanto ainda a chegar ao saldo equivalente. Pode ser noção de um serviço de custo em relação ao inúmeros aspetos. Os fatores calcular o custo de movimentação mais comuns são:

- A quantidade de dados que o serviço tem de mover ou estado.
- O custo de interrupção de ligação de clientes. Mover uma réplica primária é normalmente mais dispendioso do que o custo de mover uma réplica secundária.
- O custo de interromper uma operação em trânsito. Algumas operações de dados armazenam nível ou operações executadas em resposta a uma chamada de cliente são dispendiosas. Após um certo ponto, não pretender impedi-las, se não tiver a. Por isso, enquanto a operação está a suceder, aumenta o custo de movimentação deste objeto de serviço para reduzir a probabilidade, é movido. Quando terminar a operação, definir o custo para normal.

## <a name="enabling-move-cost-in-your-cluster"></a>Ativar o custo de movimentação do cluster
Por ordem para a MoveCosts mais granular ser levados em consideração, MoveCost tem de estar ativado no seu cluster. Sem esta definição, o modo predefinido da contagem move é utilizado para calcular MoveCost e MoveCost relatórios são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passos seguintes
- Manager de recurso de Cluster do serviço de recursos de infraestrutura utiliza a métrica para gerir consumo e capacidade do cluster. Para saber mais sobre as métricas e como configurá-las, consulte [consumo de recursos de gestão e a carga no Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para saber mais sobre como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte [balanceamento de cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
