---
title: "Resolver problemas com os relatórios de estado de funcionamento do sistema | Microsoft Docs"
description: "Descreve os relatórios de estado de funcionamento enviados por componentes do Service Fabric do Azure e respetiva utilização para resolução de problemas de cluster ou problemas da aplicação"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.openlocfilehash: b02b1260cedcade9bf69a99453ab0f5aa2c3c7b1
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Utilizar relatórios de estado de funcionamento do sistema para resolver problemas
Componentes do Service Fabric do Azure fornecem relatórios de estado de funcionamento do sistema em todas as entidades do cluster à direita da box. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) cria e elimina as entidades com base nos relatórios de sistema. É também organiza-los numa hierarquia que capture as interações de entidade.

> [!NOTE]
> Para compreender conceitos relacionados com o estado de funcionamento, leia mais em [modelo de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md).
> 
> 

Relatórios de estado de funcionamento do sistema proporcionam visibilidade de cluster e a funcionalidade da aplicação e problemas de sinalizador. Para aplicações e serviços, relatórios de estado de funcionamento do sistema Certifique-se de que as entidades são implementadas e se comportam corretamente a partir da perspetiva de Service Fabric. Os relatórios não fornecem qualquer monitorização de estado de funcionamento de lógica de negócio do serviço ou a deteção de processos bloqueados. Serviços de utilizador podem enriquecer os dados de estado de funcionamento com informações específicas para a respetiva lógica.

> [!NOTE]
> Relatórios de estado de funcionamento enviados por watchdogs do utilizador estão visíveis apenas *depois* os componentes do sistema criar uma entidade. Quando uma entidade é eliminada, o arquivo de estado de funcionamento elimina automaticamente todos os relatórios de estado de funcionamento com associados. O mesmo acontece quando é criada uma nova instância da entidade, por exemplo, quando uma novo com monitorização de estado persistente instância da réplica de serviço é criada. Associada com a instância antiga de todos os relatórios são eliminados e limpar a partir da loja.
> 
> 

O componente do sistema relatórios são identificados pela origem, que começa com o "**System.**" prefixo. Watchdogs não é possível utilizar o mesmo prefixo para as respetivas origens, como os relatórios com parâmetros inválidos são rejeitados.

Vamos ver alguns relatórios de sistema para compreender o que é acionado-los e para saber como corrigir problemas potenciais que representam.

> [!NOTE]
> Recursos de infraestrutura de serviço continua a adicionar relatórios em condições de interesse que melhoram a visibilidade sobre o que acontece no cluster e as aplicações existentes relatórios podem ser melhoradas com mais detalhes, para ajudar a resolver o problema mais rapidamente.
> 
> 

## <a name="cluster-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de cluster
A entidade de estado de funcionamento do cluster é criada automaticamente no arquivo de estado de funcionamento. Se tudo funcionar corretamente, não tem um relatório do sistema.

### <a name="neighborhood-loss"></a>Perda de Neighborhood
**System.Federation** comunica um erro quando Deteta uma perda de neighborhood. O relatório é de nós individuais e o ID de nó está incluído no nome da propriedade. Se um neighborhood é perdido no anel completo do Service Fabric, normalmente, que pode esperar dois eventos que representam ambos os lados do relatório de intervalo. Se mais neighborhoods são perdidas, existem mais eventos.

O relatório Especifica o tempo limite de concessão global, como o time-to-live (TTL). O relatório é reenviar cada meio da duração do TTL para, desde que a condição permanece ativa. O evento é removido automaticamente quando este expirar. Remover quando expirado comportamento assegura que o relatório é limpa do health store corretamente, mesmo que o nó de relatórios está inativo.

* **SourceId**: System.Federation
* **Propriedade**: começa com **Neighborhood** e inclui informações do nó.
* **Próximos passos**: investigar por que motivo o neighborhood é perdido, por exemplo, verifique a comunicação entre nós de cluster.

## <a name="node-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de nó
**System.FM**, que representa o serviço do Gestor de ativação pós-falha, é a autoridade que gere informações sobre nós de cluster. Cada nó deve ter um relatório de System.FM que mostra o estado. As entidades de nó são removidas quando o estado do nó é removido. Para obter mais informações, consulte [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Nó para cima/para baixo
System.FM relatórios como OK quando o nó se associa a coroa (está em execução). Esta comunica um erro quando o nó departs da coroa (estiver em baixo, quer para atualizar ou simplesmente porque não conseguiu). A hierarquia de estado de funcionamento criada pelo arquivo de estado de funcionamento funciona entidades implementado no correlação com System.FM relatórios de nó. Considera o nó virtual principal de todas as entidades implementadas. As entidades implementadas nesse nó são expostas através de consultas, se o nó é reportado como até por System.FM, com a mesma instância que a instância associada as entidades. Quando System.FM relatórios que o nó está inativo ou reiniciado, como uma nova instância, o arquivo de estado de funcionamento limpa automaticamente as entidades implementadas que podem existir apenas no nó para baixo ou na instância anterior do nó.

* **SourceId**: System.FM
* **Propriedade**: Estado.
* **Próximos passos**: se o nó para baixo para uma atualização, deve voltar atrás cópias de segurança depois-é foi atualizado. Neste caso, deverá mudar o estado de funcionamento para OK. Se o nó não volte ou falhar, o problema tem de investigação mais.

O exemplo seguinte mostra o evento de System.FM com um Estado de funcionamento de OK para o nó:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Expiração do certificado
**System.FabricNode** produz um aviso quando certificados utilizados pelo nó de estão prestes a expirar. Existem três certificados por nó: **Certificate_cluster**, **Certificate_server**, e **Certificate_default_client**. Quando a expiração estiver ausente, pelo menos, duas semanas, o estado de funcionamento do relatório está OK. Quando a expiração estiver dentro do prazo de duas semanas, o tipo de relatório é um aviso. TTL destes eventos é infinita e estes são removidos quando um nó deixa o cluster.

* **SourceId**: System.FabricNode
* **Propriedade**: começa com **certificado** e contém mais informações sobre o tipo de certificado.
* **Próximos passos**: atualizar certificados, se estiverem prestes a expirar.

### <a name="load-capacity-violation"></a>Violação de capacidade de carga
O Balanceador de carga do Service Fabric produz um aviso quando Deteta uma violação da capacidade de nó.

* **SourceId**: System.PLB
* **Propriedade**: começa com **capacidade**.
* **Próximos passos**: Verifique as métricas fornecidas e ver a capacidade atual no nó.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Erro de correspondência de capacidade de nó de métricas de governação de recursos
Relatórios de System.Hosting um aviso se as capacidades de nó não definido no manifesto do cluster são maiores do que o capacidades nó real de métricas de governação de recursos (núcleos de memória e cpu). Relatório de estado de funcionamento será apresentado quando o primeiro pacote de serviço que utiliza [governação de recursos](service-fabric-resource-governance.md) regista num nó especificado.

* **SourceId**: System.Hosting
* **Propriedade**: ResourceGovernance
* **Próximos passos**: Isto pode ser um problema como pacotes de serviço que rege não serão impostos conforme esperado e [governação de recursos](service-fabric-resource-governance.md) não funcionará corretamente. Atualize o manifesto do cluster com as capacidades de nó correto para estas métricas ou não especificá-las em todas as e permitir que o Service Fabric para detetar automaticamente recursos disponíveis.

## <a name="application-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de aplicação
**System.CM**, que representa o serviço do Gestor de clusters, está a autoridade que gere informações sobre uma aplicação.

### <a name="state"></a>Estado
System.CM relatórios como OK quando a aplicação foi criada ou atualizada. Informa o arquivo de estado de funcionamento quando a aplicação foi eliminada, para que pode ser removido a partir da loja.

* **SourceId**: System.CM
* **Propriedade**: Estado.
* **Próximos passos**: se a aplicação foi criada ou atualizada, deve incluir o relatório de estado de funcionamento do Gestor de clusters. Caso contrário, verifique o estado da aplicação através da emissão de uma consulta, por exemplo, o cmdlet do PowerShell **Get ServiceFabricApplication - ApplicationName** *applicationName*.

O exemplo seguinte mostra o evento de estado no **fabric: / WordCount** aplicação:

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de serviço
**System.FM**, que representa o serviço do Gestor de ativação pós-falha, é a autoridade que gere informações sobre os serviços.

### <a name="state"></a>Estado
System.FM relatórios como OK quando o serviço foi criado. Elimina a entidade do arquivo de estado de funcionamento quando o serviço foi eliminado.

* **SourceId**: System.FM
* **Propriedade**: Estado.

O exemplo seguinte mostra o evento de estado do serviço de **fabric: / WordCount/WordCountWebService**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Erro de correlação de serviço
**System.PLB** comunica um erro quando Deteta que um serviço de atualização é correlacionada com outro serviço que cria uma cadeia de afinidade. O relatório será eliminado quando ocorre uma atualização com êxito.

* **SourceId**: System.PLB
* **Propriedade**: ServiceDescription.
* **Próximos passos**: consulte as descrições de serviço correlacionado.

## <a name="partition-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de partição
**System.FM**, que representa o serviço do Gestor de ativação pós-falha, é a autoridade que gere informações sobre as partições de serviço.

### <a name="state"></a>Estado
System.FM relatórios como OK quando a partição tiver sido criada e está em bom estada. Elimina a entidade do arquivo de estado de funcionamento quando a partição é eliminada.

Se a partição é a contagem mínima de réplica, esta comunica um erro. Se a partição não está a contagem mínima de réplica, mas é inferior a contagem de réplica de destino, produz um aviso. Se a partição está em perda de quórum, System.FM reportou um erro.

Outros eventos relevantes de incluir um aviso quando a reconfiguração demora mais tempo do que o esperado e quando a compilação demora mais tempo do que o esperado. Esperados vezes para a compilação e a reconfiguração são configuráveis com base nos cenários de serviço. Por exemplo, se um serviço tiver um terabyte do Estado, como a base de dados SQL do Azure, a compilação demora mais de um serviço com uma pequena quantidade de estado.

* **SourceId**: System.FM
* **Propriedade**: Estado.
* **Próximos passos**: se o estado de funcionamento não está OK, é possível que algumas réplicas não foram criadas, abrir ou promovidas a principal ou secundário corretamente. 

Se a descrição descreve perda de quórum, em seguida, examinando o relatório de estado de funcionamento de detalhado para réplicas que estão em baixo e colocá-los a cópia de segurança ajuda-o para colocar a partição faça uma cópia online.

Se a descrição descreve uma partição bloqueada no [reconfiguração](service-fabric-concepts-reconfiguration.md), em seguida, o relatório de estado de funcionamento na réplica primária fornece informações adicionais.

Para outros relatórios de estado de funcionamento System.FM, seria possível relatórios sobre as réplicas ou a partição ou o serviço a partir de outros componentes do sistema. 

Os exemplos seguintes descrevem algumas destes relatórios. 

O exemplo seguinte mostra uma partição de bom estado de funcionamento:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O exemplo seguinte mostra o estado de funcionamento de uma partição que é inferior a contagem de réplicas de destino. O próximo passo é obter a descrição de partição, que mostra como é configurado: **MinReplicaSetSize** é três e **TargetReplicaSetSize** é sete. Em seguida, obter o número de nós no cluster, que neste caso é cinco. Por isso, neste caso, duas réplicas não não possível colocar, porque o número de destino de réplicas é superior ao número de nós disponíveis.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

O exemplo seguinte mostra o estado de funcionamento de uma partição que é bloqueada no reconfiguração devido ao utilizador para não respeitar o cancelamento token no **runasync com** método. Investigar o relatório de estado de funcionamento de qualquer réplica assinalado como principal (P) pode ajudar a pesquisar para baixo ainda mais o problema.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Este relatório de estado de funcionamento mostra o estado das réplicas de partição em reconfiguração: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Para cada réplica, o relatório de estado de funcionamento contém:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- Nó no qual a réplica está em execução
- ID de réplica

Mais caso semelhante ao exemplo, é necessário investigação. Investigar o estado de funcionamento de cada réplica individuais a partir das réplicas marcadas como `Primary` e `Secondary` (131482789658160654 e 131482789688598467) no exemplo anterior.

### <a name="replica-constraint-violation"></a>Violação de restrição de réplica
**System.PLB** produz um aviso se Deteta uma violação de restrição de réplica e não é possível colocar a todas as réplicas de partição. Os detalhes do relatório mostram as restrições e propriedades impedem a colocação de réplica.

* **SourceId**: System.PLB
* **Propriedade**: começa com **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de réplica
**System.RA**, que representa o componente reconfiguration agent, é a autoridade para o estado da réplica.

### <a name="state"></a>Estado
System.RA relatórios OK quando a réplica tiver sido criada.

* **SourceId**: System.RA
* **Propriedade**: Estado.

O exemplo seguinte mostra uma réplica de bom estado de funcionamento:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaChangeRoleStatus ReplicaOpenStatus, ReplicaCloseStatus,
Esta propriedade é utilizada para indicar os avisos ou falhas ao tentar abrir uma réplica, feche uma réplica ou efetuar a transição de uma réplica de uma função para outro. Para obter mais informações, consulte [ciclo de vida de réplica](service-fabric-concepts-replica-lifecycle.md). As falhas poderão ser exceções acionadas a partir de chamadas à API ou falhas do processo de anfitrião de serviço durante este período. Para falhas devido a chamadas de API a partir do código c#, o Service Fabric adiciona a exceção e rastreio de pilha para o relatório de estado de funcionamento.

Estes avisos de estado de funcionamento são desencadeados depois de repetir a ação localmente algumas número de vezes (dependendo da política). Service Fabric repete a ação até um limite máximo. Após ter sido atingido o limiar máximo, poderá tentar atuar para corrigir a situação. Esta tentativa pode fazer com que estes avisos obter limpo como desistir na ação neste nó. Por exemplo, se uma réplica está a conseguir abrir num nó, o Service Fabric gera um aviso de estado de funcionamento. Se a réplica continua a não conseguir abrir, Service Fabric funciona para reparar personalizada. Esta ação poderão envolver a tentar a mesma operação noutro nó. Isto faz com que o aviso gerado para esta réplica ser limpo. 

* **SourceId**: System.RA
* **Propriedade**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, e **ReplicaChangeRoleStatus**.
* **Próximos passos**: investigar as capturas de código ou falhas de serviço para identificar a razão pela qual a operação está a falhar.

O exemplo seguinte mostra o estado de funcionamento de uma réplica que está a gerar `TargetInvocationException` do respetivo método aberto. A descrição contém o ponto de falha, **IStatefulServiceReplica.Open**, o tipo de exceção **TargetInvocationException**e o rastreio da pilha.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

O exemplo seguinte mostra uma réplica que está constantemente a falhar durante o encerramento:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguração
Esta propriedade é utilizada para indicar quando efetuar uma réplica um [reconfiguração](service-fabric-concepts-reconfiguration.md) Deteta que a reconfiguração está parada ou bloqueada. Este relatório de estado de funcionamento pode ser na réplica cuja função atual é primária, exceto nos casos de uma reconfiguração de principais de comutação, onde poderá ser na réplica que está a ser despromovida de secundário do primário para o Active Directory.

A reconfiguração pode estar bloqueada por uma das seguintes razões:

- Uma ação na réplica local, a mesma réplica que efetuar a reconfiguração não está a ser concluída. Neste caso, a investigar os relatórios de estado de funcionamento desta réplica de outros componentes, System.RAP ou System.RE, pode fornecer informações adicionais.

- Uma ação não está a ser concluída uma réplica remota. As réplicas para os quais as ações são pendentes são listadas no relatório de estado de funcionamento. Investigação adicional deve ser feita em relatórios de estado de funcionamento para esses réplicas remotos. Também poderá haver problemas de comunicação entre este nó e o nó remoto.

Em casos raros, a reconfiguração pode estar bloqueada devido a comunicação ou outros problemas entre este nó e o serviço do Gestor de ativação pós-falha.

* **SourceId**: System.RA
* **Propriedade**: **reconfiguração**.
* **Próximos passos**: investigar réplicas locais ou remotas, consoante a descrição do relatório de estado de funcionamento.

O exemplo seguinte mostra um relatório de estado de funcionamento em que uma reconfiguração de ficar presa na réplica local. Neste exemplo, este é devido a um serviço não para respeitar o token de cancelamento.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

O seguinte exemplo mostra um Estado de funcionamento de relatórios sempre que uma reconfiguração está encravada aguardar uma resposta de duas réplicas remotas. Neste exemplo, existem três réplicas de partição, incluindo o principal atual. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Este relatório de estado de funcionamento mostra que a reconfiguração está bloqueada para uma resposta de duas réplicas: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Para cada réplica é dado as seguintes informações:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- ID de nó
- ID de réplica

Para desbloquear a reconfiguração:
- O **baixo** réplicas devem ser colocadas cópias de segurança. 
- O **inbuild** réplicas devem concluir a compilação e efetuar a transição para pronto.

### <a name="slow-service-api-call"></a>Chamada de API do serviço lenta
**System.RAP** e **System.Replicator** reportar um aviso se a uma chamada para o código de utilizador do serviço demora mais tempo do que o tempo configurado. O aviso será eliminado quando a chamada seja concluída.

* **SourceId**: System.RAP ou System.Replicator
* **Propriedade**: O nome da API lenta. A descrição fornece mais detalhes sobre a hora que a API foi pendente.
* **Próximos passos**: investigar por que razão a chamada demora mais tempo do que o esperado.

O exemplo seguinte mostra o evento de estado de funcionamento do System.RAP para um serviço fiável que não é para respeitar o cancelamento token no **runasync com**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

A propriedade e o texto indicam que API foi bloqueada. Os passos para diferentes APIs bloqueadas são diferentes. Qualquer API de *IStatefulServiceReplica* ou *IStatelessServiceInstance* é, normalmente, um erro com o código do serviço. A secção seguinte descreve a forma como estas convertidos para o [modelo Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: este aviso indica que uma chamada para `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, ou se tiverem sido substituídas, `OnOpenAsync` está bloqueada.

- **IStatefulServiceReplica.Close** e **IStatefulServiceReplica.Abort**: O cenário mais comum é um serviço para não respeitar o token de cancelamento transmitido ao `RunAsync`. Também pode ser que `ICommunicationListener.CloseAsync`, ou se tiverem sido substituídas, `OnCloseAsync` está bloqueada.

- **IStatefulServiceReplica.ChangeRole (S)** e **IStatefulServiceReplica.ChangeRole(N)**: O cenário mais comum é um serviço para não respeitar o token de cancelamento transmitido ao `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: O cenário mais comum é que o serviço não devolveu uma tarefa de `RunAsync`.

Outras chamadas de API que podem ficar bloqueadas estão no **IReplicator** interface. Por exemplo:

- **IReplicator.CatchupReplicaSet**: este aviso indica uma das duas coisas. Se existem insuficientes segurança de réplicas, que podem ser determinadas ao observar o estado da réplica das réplicas de partição ou o relatório de estado de funcionamento System.FM para uma reconfiguração de bloqueados. Ou, as réplicas são não confirmar operações. O comando do PowerShell-lhe permitem `Get-ServiceFabricDeployedReplicaDetail` pode ser utilizado para determinar o progresso de todas as réplicas. O problema se situa com réplicas cujos `LastAppliedReplicationSequenceNumber` está atrás do primário `CommittedSequenceNumber`.

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: este aviso indica um problema no processo de compilação. Para obter mais informações, consulte [ciclo de vida de réplica](service-fabric-concepts-replica-lifecycle.md). Tal poderá dever-se uma configuração incorreta do endereço replicador. Para obter mais informações, consulte [configurar Reliable Services com monitorização de estado](service-fabric-reliable-services-configuration.md) e [especificar recursos no manifesto serviço](service-fabric-service-manifest-resources.md). Também pode ser um problema no nó remoto.

### <a name="replication-queue-full"></a>Total de fila de replicação
**System.Replicator** produz um aviso quando a fila de replicação está cheia. Principal, a fila de replicação normalmente fica completa porque um ou mais réplicas secundárias são lentas reconhecer operações. No secundário, normalmente, isto acontece quando o serviço está lento aplicar as operações. O aviso será eliminado quando a fila já não está completa.

* **SourceId**: System.Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, consoante a função de réplica.

### <a name="slow-naming-operations"></a>Operações de nomenclatura lentas
**System.NamingService** reporta o estado de funcionamento no respetiva réplica primária, quando uma operação de nomenclatura demora mais que aceitável. Os exemplos de operações de nomenclatura [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) ou [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Mais métodos podem ser encontrados em FabricClient, por exemplo em [métodos de gestão de serviço](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) ou [métodos de gestão de propriedade](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> O serviço de nomenclatura resolve os nomes de serviço para uma localização no cluster e permite aos utilizadores gerir nomes de serviço e propriedades. É um serviço do Service Fabric persistente particionada. Uma das partições de representa o *Authority Owner*, que contém metadados sobre todos os nomes do Service Fabric e serviços. Os nomes do Service Fabric estão mapeados para diferentes partições, denominadas *Name Owner* partições, pelo que o serviço é extensível. Leia mais sobre o [Naming service](service-fabric-architecture.md).
> 
> 

Quando uma operação de nomenclatura demora mais que o esperado, a operação está assinalada com um relatório de aviso na réplica primária da partição de serviço de nomes que funciona a operação. Se a operação for concluída com êxito, o aviso está desmarcado. Se concluir a operação com um erro, o relatório de estado de funcionamento inclui detalhes sobre o erro.

* **SourceId**: System.NamingService
* **Propriedade**: começa com o prefixo "**Duration_**" e identifica a operação lenta e o nome de Service Fabric no qual é aplicada a operação. Por exemplo, se criar serviço em nome **fabric: / MyApp/MyService** demora demasiado tempo, a propriedade é **Duration_AOCreateService.fabric:/MyApp/MyService**. "Pedidos" aponta para a função de partição de nomenclatura para este nome e a operação.
* **Próximos passos**: verificação para ver o motivo pelo qual a operação de nomenclatura falhar. Cada operação pode ter causas raiz diferentes. Por exemplo, o serviço de eliminação pode estar bloqueado. O serviço pode estar bloqueado, porque o anfitrião de aplicações mantém falhar num nó devido a um erro de utilizador com o código do serviço.

O exemplo seguinte mostra uma operação de serviço de criar. A operação demorou mais do que a duração configurada. "Pedidos" repetem e envia o trabalho para "não". "Não" concluiu a última operação com tempo limite. Neste caso, a mesma réplica é principal para "Pedidos" e "Não" funções.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Relatórios de estado de funcionamento do sistema DeployedApplication
**System.Hosting** é a autoridade de entidades implementado.

### <a name="activation"></a>ativação
System.Hosting relatórios como OK quando uma aplicação foi ativada com êxito no nó. Caso contrário, esta comunica um erro.

* **SourceId**: System.Hosting
* **Propriedade**: ativação, incluindo a versão de implementação.
* **Próximos passos**: se a aplicação está em mau estado de funcionamento, investigue por que razão a ativação falha.

O exemplo seguinte mostra uma ativação com êxito:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Transferência
System.Hosting reportou um erro se falhar a transferência do pacote de aplicação.

* **SourceId**: System.Hosting
* **Propriedade**: **transferir:***RolloutVersion*.
* **Próximos passos**: investigue o motivo pelo qual a transferência falhou no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de estado de funcionamento do sistema DeployedServicePackage
**System.Hosting** é a autoridade de entidades implementado.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System.Hosting relatórios como OK se a ativação do pacote de serviço no nó é efetuada com êxito. Caso contrário, esta comunica um erro.

* **SourceId**: System.Hosting
* **Propriedade**: ativação.
* **Próximos passos**: investigue o motivo pelo qual a ativação falhou.

### <a name="code-package-activation"></a>Ativação de pacote do código
System.Hosting relatórios como OK para cada pacote do código se a ativação é efetuada com êxito. Se a ativação falhar, produz um aviso conforme configuradas. Se **CodePackage** falhar a ativação ou termina com um erro maior configurada **CodePackageHealthErrorThreshold**, alojar relatórios de erro. Se um pacote de serviço contém vários pacotes de código, é gerado um relatório de ativação para cada um deles.

* **SourceId**: System.Hosting
* **Propriedade**: utiliza o prefixo **CodePackageActivation** e contém o nome do pacote do código e o ponto de entrada como **CodePackageActivation:***CodePackageName* :*SetupEntryPoint/EntryPoint*. Por exemplo, **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registo do tipo de serviço
System.Hosting relatórios como OK se o tipo de serviço foi registado com êxito. Esta comunica um erro se o registo não foi concluído no tempo, como configurado através da utilização de **ServiceTypeRegistrationTimeout**. Se o tempo de execução é fechado, o tipo de serviço não está registado a partir do nó e de alojamento produz um aviso.

* **SourceId**: System.Hosting
* **Propriedade**: utiliza o prefixo **ServiceTypeRegistration** e contém o nome de tipo de serviço. Por exemplo, **ServiceTypeRegistration:FileStoreServiceType**.

O exemplo seguinte mostra um pacote de serviço implementado bom estado de funcionamento:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Transferência
System.Hosting reportou um erro se falhar a transferência do pacote de serviço.

* **SourceId**: System.Hosting
* **Propriedade**: **transferir:***RolloutVersion*.
* **Próximos passos**: investigue o motivo pelo qual a transferência falhou no nó.

### <a name="upgrade-validation"></a>Validação da atualização
System.Hosting reportou um erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

* **SourceId**: System.Hosting
* **Propriedade**: utiliza o prefixo **FabricUpgradeValidation** e contém a versão de atualização.
* **Descrição**: aponta para o erro encontrado.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Capacidade de nó definido para as métricas de governação de recursos
System.Hosting produz um aviso se as capacidades de nó não estão definidas no manifesto do cluster e configuração para a deteção automática está desativada. Service Fabric irá emitir um aviso de estado de funcionamento sempre que o pacote de serviço que utiliza [governação de recursos](service-fabric-resource-governance.md) regista num nó especificado.

* **SourceId**: System.Hosting
* **Propriedade**: ResourceGovernance
* **Próximos passos**: A forma preferencial para ultrapassar o problema está a mudar o manifesto do cluster para ativar a deteção automática de recursos disponíveis. Outra forma de está a atualizar o manifesto do cluster com o nó especificado corretamente as capacidades para estas métricas.

## <a name="next-steps"></a>Passos seguintes
[Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Como comunicar e verifique o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

