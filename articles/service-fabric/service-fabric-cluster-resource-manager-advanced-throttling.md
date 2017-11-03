---
title: "Limitação no Gestor de recursos de cluster de Service Fabric | Microsoft Docs"
description: "Saiba como configurar as limitações fornecidas pelo serviço de recursos de infraestrutura Cluster Gestor de recursos."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Limitação do Gestor de recursos de Cluster do serviço de recursos de infraestrutura
Mesmo se tiver configurado o Gestor de recursos de Cluster corretamente, o cluster pode obter interrompido. Por exemplo, poderia ser simultâneas nó falhas e de falhas domínio - que aconteceria se de que ocorreu durante a atualização? O Gestor de recursos de Cluster tenta sempre corrigir tudo, consumir recursos do cluster tentar reorganize e corrigir o cluster. Limitações ajudam a fornecer um backstop para que o cluster pode utilizar recursos para stabilize - os nós voltar atrás, as partições de rede heal, bits corrigidas implementados.

Para ajudar com estes tipos de situações, o Gestor de recursos de Cluster do Service Fabric inclui várias limitações. Estas limitações são todos os hammers bastante grandes. Geralmente, não deve ser alteradas sem um planeamento cuidadoso e testar.

Se alterar as limitações do Gestor de recursos de Cluster, deve otimizá-los para a carga esperada real. Pode determinar que terá tem algumas limitações, mesmo que significa que o cluster demora mais tempo para stabilize em algumas situações. Os testes são necessárias para determinar os valores corretos para limitações. Limitações têm de ser suficientemente alto para permitir que o cluster responder a alterações num período de tempo razoável e baixas o suficiente para, efetivamente, impedir demasiada consumo de recursos. 

Na maioria das que viu aos clientes utiliza limitações a ter sido porque estes já foram num ambiente restrita recursos. Alguns exemplos seria largura de banda de rede limitado para os nós individuais ou discos que não são capazes de criar várias réplicas com monitorização de estado em paralelo devido a limitações de débito. Sem limitações, operações foi sobrecarregar estes recursos, fazendo com que operações a falhar ou lenta. Nestas situações, os clientes utilizado limitações e deverem que foram expandir a quantidade de tempo que demoraria o atingir um estado estável do cluster. Os clientes compreendido também podem acabar em execução num fiabilidade geral inferior ao que foram limitadas.


## <a name="configuring-the-throttles"></a>Configurar as limitações

Serviço de recursos de infraestrutura tem dois mecanismos de forma a limitação do número de movimentos de réplica. O mecanismo predefinido que existiam antes de Service Fabric 5.7 representa a limitação como um número absoluto de move permitido. Este não funciona para os clusters de todos os tamanhos. Em particular, para grandes clusters o valor predefinido pode ser demasiado pequeno, significativamente abrandamento balanceamento mesmo quando é necessário, ao ter sem qualquer efeito em clusters mais pequenos. Este mecanismo anterior foi substituído pelas baseado em percentagem de limitação, que dimensiona melhor com clusters dinâmicos, na qual o número de nós e os serviços de alterar regularmente.

As limitações baseiam-se numa percentagem do número de réplicas nos clusters de. Limitações de Percetage com base em Ativar expressa a regra: "não mover mais de 10% das réplicas num intervalo de 10 minutos", por exemplo.

As definições de configuração para baseado em percentagem de limitação são:

  - GlobalMovementThrottleThresholdPercentage - o número máximo de movimentos permitido no cluster em qualquer altura, expresso como percentagem do número total de réplicas do cluster. 0 indica sem limite. O valor predefinido é 0. Se esta definição e a GlobalMovementThrottleThreshold forem especificados, o limite mais conservador é utilizado.
  - GlobalMovementThrottleThresholdPercentageForPlacement - número máximo de movimentos permitido durante a fase de posicionamento, expresso como percentagem do número total de réplicas do cluster. 0 indica sem limite. O valor predefinido é 0. Se esta definição e a GlobalMovementThrottleThresholdForPlacement forem especificados, o limite mais conservador é utilizado.
  - GlobalMovementThrottleThresholdPercentageForBalancing - número máximo de movimentos permitido durante a fase de balanceamento, expresso como percentagem do número total de réplicas do cluster. 0 indica sem limite. O valor predefinido é 0. Se esta definição e a GlobalMovementThrottleThresholdForBalancing forem especificados, o limite mais conservador é utilizado.

Ao especificar a percentagem de limitação, tem de especificar 5% como 0,05. O intervalo no qual são regidas estas limitações é GlobalMovementThrottleCountingInterval, que é especificada em segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Limitações de contagem com base predefinido
Estas informações são fornecidas no caso de ter clusters antigos ou ainda manter estas configurações em clusters que uma vez que tenham sido atualizados. Em geral, é recomendado que estes são substituídos por limitações baseado em percentagem acima. Uma vez que a limitação com base em percentagem está desativada por predefinição, estas limitações permanecem as limitações de predefinido para um cluster de até estão desativadas e substituídos com limitações baseado em percentagem. 

  - GlobalMovementThrottleThreshold – esta definição controla o número total de movimentos no cluster através de algum tempo. A quantidade de tempo é especificada em segundos, como o GlobalMovementThrottleCountingInterval. O valor predefinido para o GlobalMovementThrottleThreshold é 1000 e o valor predefinido para o GlobalMovementThrottleCountingInterval é 600.
  - MovementPerPartitionThrottleThreshold – esta definição controla o número total de movimentos para qualquer partição de serviço através de algum tempo. A quantidade de tempo é especificada em segundos, como o MovementPerPartitionThrottleCountingInterval. O valor predefinido para o MovementPerPartitionThrottleThreshold é 50 e o valor predefinido para o MovementPerPartitionThrottleCountingInterval é 600.

A configuração para estas limitações acompanha o mesmo padrão, como a limitação com base em percentagem.

## <a name="next-steps"></a>Passos seguintes
- Para obter informações sobre a forma como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte o artigo em [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos, consulte este artigo no [que descrevem um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
