---
title: "Gestor de recursos de Cluster de recursos de infraestrutura de serviço - integração de gestão | Microsoft Docs"
description: "Uma descrição geral dos pontos de integração entre o Gestor de recursos do Cluster e gestão de recursos de infraestrutura de serviço."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 70c0cc37a1d362c937ab86bd630c5ab051e63870
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração de Gestor de recursos de cluster com a gestão de cluster do Service Fabric
O Gestor de recursos de Cluster do serviço de recursos de infraestrutura não unidade atualizações no Service Fabric, mas está envolvida. A forma primeiro que o Gestor de recursos do Cluster ajuda com a gestão é controlar o estado pretendido do cluster e os serviços no interior-lo. O Gestor de recursos do Cluster de enviar relatórios de estado de funcionamento quando este não é possível colocar o cluster para a configuração pretendida. Por exemplo, se existir capacidade insuficiente o Gestor de recursos de Cluster envia os avisos de estado de funcionamento e erros que possam indicar o problema. Outra informação de integração tem de fazer com o funcionam das atualizações. O Gestor de recursos de Cluster altera o comportamento ligeiramente durante as atualizações.  

## <a name="health-integration"></a>Integração de estado de funcionamento
O Gestor de recursos de Cluster constantemente controla as regras definidas para colocar os serviços. É também controla a capacidade para cada métrica restantes em nós e no cluster e no cluster como um todo. Se não é possível satisfazer essas regras ou se existir capacidade insuficiente, erros e avisos sobre o estado de funcionamento são emitidos. Por exemplo, se um nó é efetuada através de capacidade e o Gestor de recursos do Cluster irá tentar corrigir a situação movendo serviços. Se não é possível corrigir a situação emite um aviso de estado de funcionamento que indica que o nó é através de capacidade de e para as métricas.

Outro exemplo de avisos de estado de funcionamento do Gestor de recursos é violações de restrições de posicionamento. Por exemplo, se definiu uma restrição de posicionamento (tais como `“NodeColor == Blue”`) e o Gestor de recursos Deteta uma violação dessa restrição, emite um aviso de estado de funcionamento. Isto é verdadeiro para restrições personalizadas e as restrições predefinidas (como as restrições de domínio de falhas e de domínio de atualização).

Eis um exemplo de um relatório de estado de funcionamento deste tipo. Neste caso, o relatório de estado de funcionamento é uma das partições do serviço de sistema. A mensagem de estado de funcionamento indica que as réplicas dessa partição estão temporariamente packed em poucos domínios de atualização.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Eis o que esta mensagem de estado de funcionamento está a informar-nos é:

1. Todas as réplicas próprios estão em bom Estadas: cada tem AggregatedHealthState: Ok
2. A restrição de distribuição de domínio de atualização está atualmente a ser violada. Isto significa que um determinado domínio de atualização tem mais réplicas desta partição que devia.
3. Nó que contém a réplica fazendo com que a violação. Neste caso, é o nó com o nome "Node.8"
4. Indica se uma atualização está a acontecer para esta partição ("atualmente atualizar – false")
5. A política de distribuição para este serviço: "--de política de distribuição mais". Isto é regido pelo `RequireDomainDistribution` [política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Packing" indica que neste caso DomainDistribution foi _não_ necessário, para que possamos saber que a política de colocação não foi especificada para este serviço. 
6. Quando o relatório foi efetuada - 8/10/2015 as 19:13:02: 00

Informações de como este alertas de powers acionados em produção para informá-lo algo tornou errado e também é utilizado para detetar e parar atualizações incorretas. Neste caso, seriam queremos ver se podemos descobrir o motivo pelo qual o Gestor de recursos tinha para as réplicas do pacote para o domínio de atualização. Normalmente, packing é transitório porque os nós de outros domínios atualizar foram, por exemplo.

Vamos supor que o Gestor de recursos de Cluster está a tentar colocar alguns serviços, mas não existe qualquer soluções que funcionam. Quando não não possível colocar os serviços, destina-se, normalmente, um dos seguintes motivos:

1. Alguns condição transitória fez com que impossível colocar esta instância de serviço ou a réplica corretamente
2. Requisitos de posicionamento do serviço são unsatisfiable.

Nestes casos, relatórios de estado de funcionamento do Gestor de recursos do Cluster de ajudam a determinar por que motivo não é possível colocar o serviço. Este processo é denominada a sequência de eliminação de restrição. Durante a, o sistema explica as restrições configuradas afetar o serviço e regista o eliminar. Desta forma, quando os serviços não são capazes de ser colocado, pode ver quais os nós que foram eliminados e por que motivo.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada uma das restrições diferentes nestes relatórios de estado de funcionamento. Irá ver mensagens de estado de funcionamento relacionados com a destas restrições quando não não possível colocar as réplicas.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: destas restrições indica que uma solução foi rejeitada porque dois objetos de serviço da partição mesmo procedimento teria de ser colocados no mesmo nó. Isto não é permitido porque, em seguida, falha de nó excessivamente teria um impacto dessa partição. ReplicaExclusionStatic e ReplicaExclusionDynamic são quase a mesma regra e as diferenças realmente não importa. Se vir uma sequência de eliminação de restrição que contém a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Gestor de recursos de Cluster pensa que não nós suficientes. Isto requer soluções restantes para utilizar estes inválido possam ser posicionadas livremente que não é permitidos. As outras restrições na sequência normalmente nos irão dizer motivo pelo qual nós estão a ser eliminados em primeiro lugar.
* **PlacementConstraint**: Se vir esta mensagem, significa que foi eliminado o aperto alguns nós porque estes não correspondiam restrições de posicionamento do serviço. Iremos rastreio terminar as restrições de posicionamento atualmente configurada como parte desta mensagem. Isto é normal se tiver uma restrição de posicionamento definida. No entanto, se a restrição de posicionamento está a causar incorretamente contém demasiados nós para ser eliminado o aperto esta é a forma como iria Repare.
* **NodeCapacity**: esta restrição significa que o Gestor de recursos de Cluster não foi possível ao colocar as réplicas em nós indicados porque que seria colocá-los através de capacidade.
* **Afinidade**: esta restrição indica que iremos não foi possível colocar a réplica em nós afetados porque causaria uma violação da restrição de afinidade. Mais informações sobre a afinidade estão a ser [neste artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: esta restrição elimina nós se colocar a réplica em nós indicado causaria packing num determinado falhas ou domínio de atualização. São vários os exemplos debater esta restrição são apresentados no tópico [restrições de domínio de falhas e a atualização e o comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: normalmente não os deveriam ver esta restrição remover nós da solução, uma vez que é executada como otimização por predefinição. A restrição de localização preferencial também se encontra presente durante as atualizações. Durante a atualização é utilizado para mover os serviços de volta para onde estava quando a atualização foi iniciado.

## <a name="blocklisting-nodes"></a>Blocklisting nós
Outra mensagem de estado de funcionamento os relatórios do Gestor de recursos do Cluster é quando nós são blocklisted. Pode considerar blocklisting como uma restrição temporária que é aplicada automaticamente para si. Nós obtém blocklisted registam falhas repetidas quando iniciar instâncias desse tipo de serviço. Nós são blocklisted numa base por--tipo de serviço. Um nó pode ser blocklisted para o tipo de um serviço, mas não outro. 

Verá blocklisting Iniciar muitas vezes durante o desenvolvimento: alguns erros faz com que o anfitrião do serviço para falhas durante o arranque. Service Fabric tenta criar o anfitrião do serviço algumas vezes e mantém a ocorrer a falha. Após alguns tentativas, o nó obtém blocklisted e o Gestor de recursos do Cluster irá tentar criar o serviço noutro local. Se essa falha mantém a acontecer em vários nós, é possível que todos os nós no cluster end cópias de segurança válidos bloqueadas. Blocklisting pode também remover nós tantas insuficiente pode iniciar com êxito o serviço para satisfazer a escala pretendida. Normalmente, irá ver adicionais erros ou avisos do Gestor de recursos de Cluster que indica que o serviço for inferior a réplica pretendida ou a contagem de instâncias, bem como as mensagens de estado de funcionamento que indica que a falha é o que origina blocklisting na primeira colocar.

Blocklisting não é uma condição permanente. Após alguns minutos, o nó é removido do blocklist e Service Fabric pode ativar os serviços nesse nó novamente. Se os serviços de continuarem a falhar, o nó é blocklisted para esse tipo de serviço novo. 

### <a name="constraint-priorities"></a>Prioridades da restrição

> [!WARNING]
> Alterar as prioridades de restrição não se recomenda e pode ter efeitos adversos significativos no seu cluster. As informações indicadas abaixo é fornecida para a referência de prioridades de restrição predefinida e o comportamento. 
>

Com todas as destas restrições, poderá ter sido pensar "Hei responsável pelo – julgo que restrições de domínio de falhas são mais importante: no meu sistema. Para garantir que a restrição de domínio de falhas não é violada, estou disposto a violar outras restrições. "

Restrições podem ser configuradas com níveis de prioridade diferente. Nomeadamente:

   - "rígido" (0)
   - "soft" (1)
   - "otimização" (2)
   - "off" (-1). 
   
Por predefinição a maioria das restrições é configurados como restrições de disco rígidas.

Alterar a prioridade de restrições é invulgar. Existiram vezes onde prioridades de restrição necessários para alterar, normalmente, para contornar alguns erros ou comportamento que está a afetar o ambiente. Geralmente, a flexibilidade da infraestrutura de prioridade de restrição trabalhou muito bem, mas este não é necessário frequentemente. Na maioria das vezes tudo encontra-se nas respetivas prioridades da predefinição. 

Os níveis de prioridade não significam que uma restrição determinada _será_ ser violado, nem que será sempre ser satisfeita. Prioridades da restrição definem uma ordem na qual as restrições são aplicadas. Prioridades definem fala quando não é possível satisfazer todas as restrições. Normalmente, todas as restrições podem ser satisfeitas a menos que exista um senão acontecimentos no ambiente. Alguns exemplos de cenários que irão causar a violações de restrição são as restrições em conflito, ou grande número de falhas em simultâneo.

Em situações avançadas, pode alterar as prioridades de restrição. Por exemplo, imagine que quer Certifique-se de que a afinidade sempre iria ser violada quando for necessário para resolver problemas de capacidade de nó. Para tal, pode definir a prioridade de restrição de afinidade para "soft" (1) e deixar a restrição de capacidade definida como "disco rígida" (0).

Os valores de prioridade predefinidos para as diferentes restrições estão especificados na configuração seguinte:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

através de Clusterconfig para implementações autónomas ou Template do Azure alojada clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restrições de domínio do domínio e a atualização falha
O Gestor de recursos de Cluster pretende manter serviços distribuir entre domínios de falhas e a atualização. Modelos tal como uma restrição dentro do motor do Gestor de recursos de Cluster. Para obter mais informações sobre como são utilizadas e o respetivo comportamento específico, consulte o artigo em [configuração de cluster](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

O Gestor de recursos de Cluster poderá ter de pacote alguns réplicas para um domínio de atualização para lidar com as atualizações, falhas ou outras violações de restrição. Packing em domínios de falhas ou atualização normalmente ocorre apenas quando existem várias falhas ou outro volume de alterações no sistema de impedir a colocação corretas. Se pretender impedir mais mesmo durante estas situações, pode utilizar o `RequireDomainDistribution` [política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Tenha em atenção que isto pode afetar a disponibilidade do serviço e a fiabilidade como efeito, por isso considere.

Se o ambiente está corretamente configurado, os todas as restrições, completamente são respeitadas, mesmo durante as atualizações. A chave é que o Gestor de recursos de Cluster está a observar para as restrições. Quando Deteta uma violação-lo imediatamente relatórios-lo e tenta corrigir o problema.

## <a name="the-preferred-location-constraint"></a>A restrição de localização preferencial
A restrição de PreferredLocation é ligeiramente diferente, porque tem dois utiliza diferentes. É uma utilização desta restrição durante as atualizações de aplicações. O Gestor de recursos de Cluster gere automaticamente esta restrição durante as atualizações. É utilizado para garantir que, quando as atualizações estiverem concluídas se réplicas devolvem para as respetivas localizações iniciais. A outra utilização da restrição PreferredLocation destina-se a [ `PreferredPrimaryDomain` política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Estes são otimizações e, por conseguinte, a restrição de PreferredLocation é a única restrição definida como "Otimização" por predefinição.

## <a name="upgrades"></a>Atualizações
O Gestor de recursos de Cluster também ajuda a durante a aplicação e atualizações de cluster, durante os quais tem duas tarefas:

* Certifique-se de que as regras do cluster não sejam comprometidas
* Tente ajudar a atualização correm

### <a name="keep-enforcing-the-rules"></a>Manter a impor as regras
A principal coisa a ter em consideração é que as regras – as restrições strict como restrições de posicionamento e as capacidades - ainda são impostas durante as atualizações. Restrições de posicionamento Certifique-se de que as cargas de trabalho executam apenas em que estão autorizados a, mesmo durante as atualizações. Quando os serviços são altamente restrita, as atualizações podem demorar mais tempo. Quando o serviço ou o nó que está a ser executado no seja colocado para baixo para uma atualização poderão existir algumas opções para onde pode aceder.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização é iniciado, o Gestor de recursos tira um instantâneo da disposição atual do cluster. Como cada domínio de atualização estiver concluída, este tenta devolver os serviços que foram nesse domínio de atualização para os respetivos disposição original. Desta forma existem no máximo duas transições para um serviço durante a atualização. Existe uma movimentação fora do nó afetado e um regresse. Devolver o serviço de cluster ou a forma como foi antes da atualização também garante que a atualização não afeta o esquema do cluster. 

### <a name="reduced-churn"></a>Volume de alterações reduzida
Outra coisa a fazer durante atualizações é que activa o Gestor de recursos de Cluster desativado balanceamento. Impedir balanceamento impede reactions desnecessários para a atualização em si, como mover serviços para nós que foram esvaziados para a atualização. Se a atualização em questão for uma atualização do Cluster, todo o cluster não é balanceado durante a atualização. Verificações de restrição permanecerá ativas, movimento apenas com base no balanceamento proativa das métricas está desativado.

### <a name="buffered-capacity--upgrade"></a>Capacidade de memória intermédia & atualização
Geralmente, pretende que a atualização para concluir o mesmo que o cluster é limitado ou próximo completo. Gerir a capacidade do cluster é ainda mais importante durante as atualizações que o habitual. Dependendo do número de domínios de atualização, entre 5 e 20 por cento da capacidade de têm de ser migradas como a atualização se faz através de cluster. Tem que funcionam para ir algures. Este é onde a noção de [colocado na memória intermédia as capacidades](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) é útil. Capacidade de memória intermédia é respeitada durante o funcionamento normal. O Gestor de recursos do Cluster pode preencher nós até as respetivas capacidade total (a memória intermédia de consumo) durante as atualizações, se necessário.

## <a name="next-steps"></a>Passos seguintes
* Iniciar a partir do início e [obtenha uma introdução para o serviço de recursos de infraestrutura Cluster Gestor de recursos](service-fabric-cluster-resource-manager-introduction.md)
