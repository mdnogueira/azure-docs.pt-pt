---
title: Equilibrar o cluster do Service Fabric do Azure | Microsoft Docs
description: "Uma introdução ao seu cluster com o serviço de recursos de infraestrutura recursos Gestor de clusters de balanceamento."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 06d65878d84fb845cf0c4c333a1e2d12b0aaec2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="balancing-your-service-fabric-cluster"></a>Balanceamento do cluster do service fabric
O Gestor de recursos de Cluster do serviço de recursos de infraestrutura suporta alterações de carga dinâmico, reagir a adições ou remoção de nós ou serviços. Também automaticamente corrige violações de restrição e proativamente efetua novamente o balanceamento de cluster. Mas frequência destas ações efetuadas e o que é acionado-las?

Existem três categorias diferentes de trabalho que executa o Gestor de recursos do Cluster. São:

1. Posicionamento-esta fase lida com colocar qualquer réplicas com monitorização de estado ou instâncias sem monitorização de estado que estão em falta. Colocação inclui os novos serviços e o processamento de réplicas com monitorização de estado ou instâncias sem monitorização de estado que falharam. Eliminar e remover as réplicas ou instâncias são processadas aqui.
2. Restrição verifica – nesta fase verifica e corrige violações diferentes restrições de posicionamento (regras) no sistema. Exemplos de regras são coisas como garantir que nós não estão através de capacidade e que as restrições de posicionamento de um serviço são cumpridas.
3. Balanceamento – nesta fase verifica para ver se reequilíbrio é necessário com base no nível pretendido configurado de saldo diferentes com base nas métricas. Se assim for, este tenta localizar uma disposição no cluster que é mais com balanceamento de.

## <a name="configuring-cluster-resource-manager-timers"></a>Configurar os temporizadores de Gestor de recursos de Cluster
O primeiro conjunto de controlos em torno balanceamento são um conjunto de contadores. Estes temporizadores governem com que frequência o Gestor de recursos de Cluster examina o cluster e executa as ações corretivas.

Cada um destes tipos diferentes de correções pode tornar o Gestor de recursos do Cluster é controlada por um temporizador diferentes que regulam a respetiva frequência. Quando cada temporizador é acionado, a tarefa é agendada. Por predefinição o Gestor de recursos:

* analisa o estado e aplica atualizações (como gravação é de um nó para baixo) cada 1/10 de um segundo
* Define o sinalizador de verificação de colocação 
* Define o sinalizador de verificação de restrição a cada segundo
* Define o sinalizador balanceamento a cada cinco segundos.

Exemplos de configuração que rege estes temporizadores são abaixo:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Hoje o Gestor de recursos de Cluster só efetua uma destas ações numa altura, sequencialmente. É por isso, consulte estes temporizadores como "intervalos mínimos" e as ações que obterem tomadas quando os temporizadores aceda como "sinalizadores de definição". Por exemplo, o Gestor de recursos de Cluster encarrega-se de pedidos pendentes para criar serviços antes do cluster de balanceamento. Como pode ver, os intervalos de tempo predefinido especificados, o Gestor de recursos de Cluster verifica a existência de nada tem de fazer com frequência. Normalmente, isto significa que o conjunto de alterações efetuadas durante cada passo pequeno. Pequenas alterações de frequência permite que o Gestor de recursos de Cluster ser reativo quando ocorrem erros no cluster. Os temporizadores de predefinição fornecem algumas criação de batches, uma vez que muitos dos mesmos tipos de eventos tendem a ocorrer em simultâneo. 

Por exemplo, quando nós falharem que podem fazer os domínios de falhas, por isso, todo cada vez. Todas estas falhas são capturadas durante o estado do seguinte atualizado após a *PLBRefreshGap*. As correções são determinadas durante o posicionamento seguinte, a verificação da restrição, e balanceamento é executado. Por predefinição o Gestor de recursos do Cluster é não análise através de horas de alterações no cluster e está a tentar resolver todas as alterações em simultâneo. Se o fizer, iria originar bursts do volume de alterações.

O Gestor de recursos do Cluster tem também de algumas informações adicionais para determinar se o cluster imbalanced. Para que temos duas outras partes da configuração: *BalancingThresholds* e *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Balanceamento de limiares
Um limiar balanceamento é o controlo principal para acionar reequilíbrio. O limite de balanceamento de mensagens em fila para uma métrica é um _rácio_. Se a carga de uma métrica no nó mais carregado dividido pela quantidade de carga no nó menos carregado exceder esse métrica *BalancingThreshold*, em seguida, o cluster é imbalanced. Como resultado balanceamento é acionado da próxima vez que verifica o Gestor de recursos do Cluster. O *MinLoadBalancingInterval* temporizador define a frequência do Gestor de recursos de Cluster deve verificar se reequilíbrio é necessário. A verificação não significa que ocorra algum problema. 

Limiares balanceamento são definidos numa base por métrica como parte da definição de cluster. Para obter mais informações sobre as métricas, veja [neste artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>
![Balanceamento de limiar de exemplo][Image1]
</center>

Neste exemplo, cada serviço está a consumir uma unidade de algumas métrica. No exemplo superior, a carga máxima num nó for igual a cinco e o mínimo é dois. Imaginemos que o limiar de balanceamento desta métrica é três. Uma vez que o rácio do cluster é 5/2 = 2.5 e que é menor do que o especificado o cluster de balanceamento de limiar de três, é balanceamento. Sem balanceamento é acionado quando verifica o Gestor de recursos do Cluster.

No exemplo na parte inferior, a carga máxima num nó é 10, enquanto o mínimo é dois, resultando num rácio de cinco. Cinco é superior ao limiar balanceamento designado de três para esse métrica. Como resultado, uma reequilíbrio executar será agendada próxima vez que é acionado o temporizador balanceamento. Numa situação como esta algumas carga normalmente é distribuída aos Nó3. Porque o Gestor de recursos de Cluster do serviço de recursos de infraestrutura não utiliza uma abordagem abrangente, alguns carga também pode ser distribuída para Nó2. 

<center>
![Balanceamento de ações de exemplo do limiar][Image2]
</center>

> [!NOTE]
> "Balanceamento" processa dois estratégias diferentes para gerir a carga no seu cluster. A estratégia de predefinido que utilize o Gestor de recursos do Cluster é distribuam a carga em todos os nós do cluster. A estratégia de outra é [desfragmentação](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Desfragmentação é efetuada durante o balanceamento mesmo executar. As estratégias de desfragmentação de balanceamento e podem ser utilizadas com base nas métricas diferente dentro do mesmo cluster. Um serviço pode ter balanceamento e desfragmentação métricas. Desfragmentação com base nas métricas, é o rácio entre as cargas do cluster acionado reequilíbrio quando for _abaixo_ o limiar balanceamento. 
>

Obter abaixo do limiar balanceamento não é um objetivo explícito. Limiares balanceamento são apenas um *acionador*. Quando balanceamento é executado, o Gestor de recursos de Cluster determina que melhorias pode tornar, se existirem. Apenas porque uma pesquisa balanceamento é arrancou não significa que algo move. Por vezes, o cluster é imbalanced mas demasiado restrita para corrigir. Em alternativa, os melhoramentos necessitam de movimentos são demasiado [dispendiosas](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Limiares de atividade
Por vezes, apesar dos nós serem relativamente imbalanced, o *total* quantidade de carga no cluster é baixa. A falta de carga pode ser um dip transitório ou porque o cluster é novo e apenas ao obter bootstrapped. Em ambos os casos, poderá não querer demora tempo o cluster de balanceamento porque não há pouco para ser adquiridos. Se o cluster underwent balanceamento, teria de gastam a rede e recursos de computação para mover as coisas-sem efetuar qualquer grande *absoluto* diferença. Para evitar desnecessários move, há outro controlo conhecido como limiares de atividade. Limiares de atividade permite-lhe especificar algum limite inferior absoluto para a atividade. Se nenhum nó está acima deste limiar, balanceamento não é acionado, mesmo se é cumprido o limiar de balanceamento.

Imaginemos que iremos manter nosso limiar balanceamento de três para esta métrica. Vamos também supor que temos um limite de atividade de 1536. No primeiro caso, enquanto o cluster é imbalanced pelo limiar de balanceamento de mensagens em fila não existe nenhum nó cumpre esse limiar de atividade, pelo que não acontece nada. No exemplo na parte inferior, Nó1 é superior ao limiar de atividade. Uma vez que o limiar de balanceamento e o limite de atividade para a métrica for excedido, o balanceamento está agendado. Por exemplo, vamos ver o diagrama a seguir: 

<center>
![Exemplo de limiar de atividade][Image3]
</center>

Tal como balanceamento limiares, limiares de atividade são definido por-métrica através da definição de cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Limiares de balanceamento e a atividade estão ambas associadas a métrica específica - balanceamento é acionado apenas se o limiar de balanceamento e a atividade limiar for excedido para a métrica mesma.

> [!NOTE]
> Quando não especificado, o limite de balanceamento de mensagens em fila para uma métrica é 1 e o limite de atividade é 0. Isto significa que o Gestor de recursos do Cluster irá tentar manter essa métrica perfeitamente com balanceamento de qualquer carga fornecida. Se estiver a utilizar métricas personalizadas é recomendado que definir explicitamente os suas próprias limiares de balanceamento e a atividade para as métricas. 
>

## <a name="balancing-services-together"></a>Serviços de balanceamento em conjunto
Se o cluster é imbalanced ou não é uma decisão em todo o cluster. No entanto, a forma como podemos aceda sobre corrigir-está a mover réplicas serviço individuais e as instâncias em torno. Isto faz sentido, certo? Se a memória é empilhada cópias de segurança num nó, várias réplicas ou instâncias podem contribuir para a mesma. Corrigir o desequilíbrio pode exigir a qualquer um dos réplicas com monitorização de estado ou instâncias sem monitorização de estado que utilizam a métrica imbalanced mover.

Ocasionalmente, embora, um serviço que não pertença como próprio imbalanced obtém moveu (Lembre-se o debate da local e global ponderações anteriormente). Por que motivo seria um serviço obter movido quando tudo o que foram com balanceamento de métricas do serviço? Vamos ver um exemplo:

- Vamos supor que existem quatro serviços, Service1, Service2, Service3 e Service4. 
- Service1 relatórios métricas Metric1 e Metric2. 
- Service2 relatórios métricas Metric2 e Metric3. 
- Service3 relatórios métricas Metric3 e Metric4.
- Service4 relatórios Metric99 métrica. 

Surely pode ver onde iremos aqui: há uma cadeia! Não temos realmente quatro serviços independentes, temos de três serviços que estão relacionados e um arquivo que está desativada no seu próprio.

<center>
![Serviços de balanceamento em conjunto][Image4]
</center>

Devido a esta cadeia, é possível que um desequilíbrio nas métricas de 1 a 4 pode fazer com que as réplicas ou instâncias que pertençam a serviços de 1 a 3 para mover-se. Também Sabemos que um desequilíbrio no métricas 1, 2 ou 3 não pode fazer com que movimentos na Service4. Não haveria nenhum ponto desde mover as réplicas ou instâncias que pertençam a Service4 em torno fazer absolutamente nada a afetar o equilíbrio de métricas de 1 a 3.

O Gestor de recursos do Cluster automaticamente figuras enviados os serviços que estão relacionados. Adicionar, remover ou alterar as métricas para os serviços pode afetar as respetivas relações. Por exemplo, entre duas execuções do balanceamento Service2 poderão foram atualizadas para remover Metric2. Isto interrompe a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços relacionados, existem três:

<center>
![Serviços de balanceamento em conjunto][Image5]
</center>

## <a name="next-steps"></a>Passos seguintes
* As métricas são como o serviço de recursos de infraestrutura Cluster Resource Manager gere consumo e capacidade do cluster. Para saber mais sobre as métricas e como configurá-las, consulte [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
* O custo de movimento é uma forma de sinalização o Gestor de recursos do Cluster que são mais dispendiosos de mover que outros determinados serviços. Para mais informações sobre o custo de movimento, consulte [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Gestor de recursos do Cluster tem várias limitações que pode configurar abrandar o volume de alterações no cluster. Não está normalmente necessárias, mas se necessário pode saber mais sobre os mesmos [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
