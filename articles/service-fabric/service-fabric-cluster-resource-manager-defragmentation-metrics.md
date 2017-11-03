---
title: "Desfragmentação de métricas nos recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Uma descrição geral da utilização desfragmentação ou packing como uma estratégia de métricas no Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0b8b2bad967532bb0040407dc6a3a7b9599576d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e a carga no Service Fabric
Os recursos de infraestrutura Cluster recursos do Service Manager estratégia a predefinição para gerir as métricas de carga no cluster é distribuir a carga. Garantir que os nós estão uniformemente utilizados evita oportunidades de acesso frequente e amovíveis que levar a contenção e wasted recursos. Distribuição de cargas de trabalho no cluster também é esta em termos de reiniciadas falhas, uma vez que garante que uma falha não remova uma grande percentagem de uma carga de trabalho especificada. 

O Gestor de recursos de Cluster do serviço de recursos de infraestrutura suporta uma estratégia de diferentes para gerir a carga, o que é a desfragmentação. Desfragmentação significa que em vez de tentar distribuir a utilização de uma métrica no cluster, este é consolidado. Consolidação é apenas um inversion da predefinida balanceamento estratégia – em vez de minimizar o desvio-padrão médio de métrica de carga, o Gestor de recursos de Cluster tenta aumente demasiado.

## <a name="when-to-use-defragmentation"></a>Quando utilizar a desfragmentação
Distribuição de carga no cluster consome alguns dos recursos em cada nó. Algumas cargas de trabalho criar serviços que são exceptionally grandes e consumam a maior parte ou a totalidade de um nó. Nestes casos, é possível que quando existem grandes cargas de trabalho obter criadas que não é suficiente espaço em qualquer nó executá-los. Um problema no Service Fabric; não são de grandes cargas de trabalho Nestes casos, o Gestor de recursos de Cluster determina que precisa de reorganizar o cluster para disponibilizar espaço para esta grande carga de trabalho. No entanto, entretanto que carga de trabalho tem de aguardar para ser agendados no cluster.

Se existirem vários serviços e estado para mover-se, em seguida, pode demorar muito tempo para que a carga de trabalho grande ser colocada no cluster. Este é mais provável se outras cargas de trabalho no cluster também são grande e, por isso, demorar mais tempo a reorganize. A equipa de Service Fabric medida tempos de criação no simulações deste cenário. Detetámos que demorou muito mais criar serviços grande assim que a utilização do cluster obteve acima entre 30% e 50%. Para processar este cenário, introduzimos desfragmentação como uma estratégia de balanceamento. Detetámos que para grandes cargas de trabalho, especialmente aqueles em que a hora de criação foi importante, desfragmentação realmente ajudou a estas novas cargas de trabalho obterem agendadas no cluster.

Pode configurar as métricas de desfragmentação com o Gestor de recursos para tentar proativamente condense a carga dos serviços em menos nós de Cluster. Isto ajuda a garantir que existe quase sempre espaço para grandes serviços sem reorganizar o cluster. Não terem de reorganizar o cluster permite criar rapidamente grandes cargas de trabalho.

A maioria das pessoas não precisam de desfragmentação. Os serviços são normalmente ser pequeno, pelo que não é difícil localizar o espaço para os mesmos no cluster. Quando a reorganização for possível, passa rapidamente, novamente porque a maioria dos serviços são pequenas e pode ser movida rapidamente e em paralelo. No entanto, se tiver serviços grande e precisar deles rapidamente, em seguida, criar a estratégia de desfragmentação é que o utilizador. Vamos abordar fala da utilização de desfragmentação seguinte. 

## <a name="defragmentation-tradeoffs"></a>Desfragmentação fala
Desfragmentação pode aumentar impactfulness de falhas, uma vez que mais serviços estão em execução em nós que não obedeçam a. Desfragmentação também pode aumentar os custos, uma vez que os recursos do cluster tem de ser contidos num reserva, a aguardar a criação de grandes cargas de trabalho.

O diagrama seguinte fornece uma representação visual de dois clusters, um que é defragmented e outro que não se encontra. 

<center>
![Comparar balanceamento e Defragmented Clusters][Image1]
</center>

No caso de equilibrada, considere o número de movimentos seria necessário colocar um dos objetos de serviço maiores. No defragmented cluster, a carga de trabalho grande foi possível colocar em nós quatro ou cinco sem ter de aguardar por quaisquer outros serviços mover.

## <a name="defragmentation-pros-and-cons"></a>Os profissionais de desfragmentação e contras
Por isso, quais são esses outras fala conceptual? Eis uma tabela rápida das ações de pensar em:

| Profissionais de desfragmentação | Contras de desfragmentação |
| --- | --- |
| Permite a criação rápida dos serviços de grande |Carregar concentrates em menos nós, aumentando a contenção |
| Permite reduzir o movimento de dados durante a criação |Falhas podem afetar mais serviços e fazer com que mais de volume de alterações |
| Permite avançada descrição dos requisitos e recuperação de espaço |Configuração de gestão de recursos mais complexa global |

Pode misturar métricas defragmented e normais no mesmo cluster. O Gestor de recursos de Cluster tenta consolidar as métricas de desfragmentação quanto possíveis durante propagando-se os outros. Os resultados da combinação de desfragmentação e balanceamento de estratégias depende de vários fatores, incluindo:
  - o número de balanceamento de métricas vs. o número de métricas de desfragmentação
  - Se o serviço utiliza ambos os tipos de métricas 
  - as ponderações métricas
  - carrega métrica atual
  
Experimentação é necessário para determinar a configuração exata necessária. Recomendamos a medida de detalhado das cargas de trabalho antes de ativar as métricas de desfragmentação na produção. Isto é especialmente verdadeiro quando a combinação de desfragmentação e métricas equilibradas no mesmo serviço. 

## <a name="configuring-defragmentation-metrics"></a>Configuração de desfragmentação de métricas
Configurar as métricas de desfragmentação é uma decisão global no cluster e métricas individuais podem ser selecionadas para desfragmentação. Os fragmentos de configuração seguintes mostram como configurar as métricas de desfragmentação. Neste caso, "Metric1" está configurada como uma métrica de desfragmentação, enquanto "Metric2" irá continuar a ser balanceado normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Passos seguintes
- O Gestor de recursos do Cluster tem opções de man para descrever o cluster. Para saber mais sobre os mesmos, consulte este artigo no [que descrevem um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como o serviço de recursos de infraestrutura Cluster Resource Manager gere consumo e capacidade do cluster. Para saber mais sobre as métricas e como configurá-las, consulte [neste artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
