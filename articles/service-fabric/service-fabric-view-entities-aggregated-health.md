---
title: Estado de funcionamento de agregado como ver as entidades de Service Fabric do Azure | Microsoft Docs
description: "Descreve como consultar, ver e avaliar agregados estado de funcionamento as entidades do Azure Service Fabric, através de consultas de estado de funcionamento e consultas gerais."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.openlocfilehash: 8815d4409b9b18d34fae737c1c0dc22d2ed633a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-service-fabric-health-reports"></a>Ver relatórios de estado de funcionamento do Service Fabric
Azure Service Fabric apresenta um [modelo de estado de funcionamento](service-fabric-health-introduction.md) com entidades de estado de funcionamento que componentes de sistema e watchdogs pode relatório local as condições que está a monitorizar. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega todos os dados de estado de funcionamento para determinar se entidades estão em bom estadas.

O cluster é preenchido automaticamente com os relatórios de estado de funcionamento enviados pelos componentes do sistema. Leia mais em [utilizar relatórios de estado de funcionamento do sistema para resolver](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

O Service Fabric fornece várias formas de obter o estado de funcionamento agregado das entidades:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização
* Consultas de estado de funcionamento (através do PowerShell, API ou REST)
* Geral consultas que retorno uma lista de entidades que tenham o estado de funcionamento como uma das propriedades (através do PowerShell, API ou REST)

Para demonstrar estas opções, vamos utilizar um cluster local com cinco nós e o [fabric: / WordCount aplicação](http://aka.ms/servicefabric-wordcountapp). O **fabric: / WordCount** aplicação contém dois serviços de predefinição, um serviço com monitorização de estado do tipo `WordCountServiceType`e um serviço sem monitorização de estado do tipo `WordCountWebServiceType`. Posso alterar a `ApplicationManifest.xml` para exigir sete réplicas para o serviço de monitorização de estado e uma partição de destino. Porque existem apenas cinco nós no cluster, os componentes do sistema comunicam um aviso na partição de serviço porque está a contagem de destino.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Estado de funcionamento no Service Fabric Explorer
Service Fabric Explorer proporciona uma vista visual do cluster. Na imagem abaixo, pode ver que:

* A aplicação **fabric: / WordCount** está vermelho (na erro), porque tem um evento de erro comunicado pelo **MyWatchdog** para a propriedade **disponibilidade**.
* Um dos respetivos serviços, **fabric: / WordCount/WordCountService** for amarelo (em aviso). O serviço está configurado com sete réplicas e o cluster com cinco nós, pelo que não é possível colocar duas repicas. Apesar de não ser apresentado aqui, a partição de serviço for amarela devido a um relatório do sistema de `System.FM` indicando que `Partition is below target replica or instance count`. A partição amarela aciona o serviço amarelo.
* O cluster é vermelho devido à aplicação vermelha.

A avaliação utiliza políticas predefinidas do manifesto do cluster e o manifesto da aplicação. Estão a políticas strict e não tolerar qualquer falha.

Vista do cluster com o Service Fabric Explorer:

![Vista do cluster com o Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Leia mais sobre [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Consultas de estado de funcionamento
Service Fabric expõe consultas de estado de funcionamento para cada um dos suportado [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy). Pode ser acedidos através da API, através de métodos em [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), cmdlets do PowerShell e REST. Estas consultas devolvem informações de estado de funcionamento completo sobre a entidade: o estado de funcionamento agregados, eventos de estado de funcionamento da entidade, os Estados de funcionamento de subordinados (quando aplicável), avaliações mau estado de funcionamento (quando a entidade não é bom estado de funcionamento) e estatísticas de estado de funcionamento de elementos subordinados (quando aplicável).

> [!NOTE]
> Uma entidade de estado de funcionamento é devolvida quando for povoado completamente no arquivo de estado de funcionamento. A entidade tem de ser Active Directory (não eliminado) e tem um relatório de sistema. As entidades principais na cadeia de hierarquia também tem de ter relatórios de sistema. Se qualquer uma das seguintes condições não forem satisfeitas, o estado de funcionamento consulta devolver um [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) com [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` que mostra o motivo pelo qual a entidade não é devolvida.
>
>

As consultas de estado de funcionamento tem de passar no identificador de entidade, que depende do tipo de entidade. As consultas de aceitar os parâmetros de política de estado de funcionamento opcionais. Se não existem políticas de estado de funcionamento forem especificadas, o [políticas de estado de funcionamento](service-fabric-health-introduction.md#health-policies) do manifesto de cluster ou aplicações são utilizadas para avaliação. Se os manifestos não contém uma definição para políticas de estado de funcionamento, as políticas de estado de funcionamento predefinida são utilizadas para avaliação. As políticas de estado de funcionamento predefinida não tolerar eventuais falhas. As consultas também aceitam filtros para devolver apenas os elementos subordinados parciais ou eventos – aqueles que respeitem os filtros especificados. Filtro de outro permite excluir as estatísticas de elementos subordinados.

> [!NOTE]
> Os filtros de saída são aplicados no lado do servidor, pelo que o tamanho da mensagem de resposta é reduzido. Recomendamos que pode utiliza os filtros de saída para limitar os dados devolvidos, em vez de aplicar os filtros do lado do cliente.
>
>

Estado de funcionamento de uma entidade contém:

* O estado agregado de estado de funcionamento da entidade. Calculado pelo arquivo de estado de funcionamento com base em relatórios de estado de funcionamento da entidade, os Estados de funcionamento de subordinados (quando aplicável) e políticas de estado de funcionamento. Leia mais sobre [avaliação de estado de funcionamento da entidade](service-fabric-health-introduction.md#health-evaluation).  
* Os eventos de estado de funcionamento na entidade.
* A coleção de Estados de funcionamento de todos os elementos subordinados para as entidades que podem ter elementos subordinados. Estados de funcionamento contenham identificadores de entidade e o estado de funcionamento agregada. Para obter o estado de funcionamento completo para um elemento subordinado, chamar o estado de funcionamento de consulta para o tipo de entidade subordinada e atribua o identificador de subordinados.
* As avaliações de mau estado de funcionamento que apontam para o relatório que acionou o estado da entidade, se a entidade não está em bom estada. As avaliações são recursiva, que contém as avaliações de estado de funcionamento de elementos subordinados que acionou o estado de funcionamento atual. Por exemplo, um watchdog do comunicou um erro em relação a uma réplica. O estado de funcionamento de aplicações apresenta uma mau avaliação devido a um serviço de estado de funcionamento incorreto; o serviço está danificado devido a uma partição na erro; a partição está danificada devido a uma réplica no registo de erros; a réplica está danificada devido ao relatório de estado de funcionamento de erro watchdog.
* As estatísticas de estado de funcionamento de todos os tipos de elementos subordinados de entidades com elementos subordinados. Por exemplo, o estado de funcionamento do cluster mostra o número total de aplicações, serviços, partições, as réplicas e implementar entidades do cluster. Estado de funcionamento de serviço mostra o número total de partições e réplicas do serviço especificado.

## <a name="get-cluster-health"></a>Obter o estado de funcionamento do cluster
Devolve o estado de funcionamento da entidade de cluster e contém os Estados de funcionamento das aplicações e nós (subordinados do cluster). Entrada:

* [Opcional] A política de estado de funcionamento do cluster utilizada para avaliar os nós e os eventos de cluster.
* [Opcional] O estado de funcionamento política o mapeamento de aplicações, com as políticas de estado de funcionamento utilizada para substituir as políticas de manifesto de aplicação.
* [Opcional] Filtros de eventos, nós e aplicações que especificam quais entradas são úteis e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos, nós e aplicações são utilizadas para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.
* [Opcional] Filtro excluir estatísticas de estado de funcionamento.
* [Opcional] Filtro para incluir fabric: / estatísticas de estado de funcionamento do sistema nas estatísticas de estado de funcionamento. Apenas aplicável quando as estatísticas de estado de funcionamento não são excluídas. Por predefinição, as estatísticas de estado de funcionamento incluem apenas estatísticas para as aplicações de utilizador e não a aplicação de sistema.

### <a name="api"></a>API
Para obter o estado de funcionamento do cluster, crie um `FabricClient` e chame o [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) método no respetivo **HealthManager**.

A seguinte chamada obtém o estado de funcionamento do cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O código seguinte obtém o estado de funcionamento do cluster utilizando uma política de estado de funcionamento de cluster personalizado e os filtros para nós e aplicações. Especifica que as estatísticas de estado de funcionamento incluem o fabric: / estatísticas de sistema. Cria [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), que contém as informações de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do cluster é [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O estado do cluster é cinco nós, a aplicação de sistema e fabric: / WordCount configurado, tal como descrito.

O seguinte cmdlet obtém o estado de funcionamento do cluster através de políticas de estado de funcionamento de predefinição. O estado de funcionamento agregados indica um aviso, porque os recursos de infraestrutura: / WordCount aplicação está em aviso. Tenha em atenção a como as avaliações de mau estado de funcionamento fornecem detalhes sobre as condições que acionou o estado de funcionamento agregado.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                          
                          
NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_0
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

O cmdlet PowerShell seguinte obtém o estado de funcionamento do cluster utilizando uma política de aplicação personalizada. -Filtra os resultados para obter apenas aplicações e os nós no erro ou aviso. Como resultado, nenhum nó é devolvidos, dado que estão todos em bom Estados. Apenas os recursos de infraestrutura: / WordCount aplicação respeita o filtro de aplicações. Porque a política personalizada Especifica a ter em consideração avisos como erros para os recursos de infraestrutura: / WordCount aplicação, a aplicação é avaliada como erro e, por isso, é o cluster.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                          
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento do cluster com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-node-health"></a>Obter o estado de funcionamento do nó
Devolve o estado de funcionamento da entidade de um nó e contém os eventos de estado de funcionamento comunicados no nó. Entrada:

* [Necessário] O nome de nó que identifica o nó.
* [Opcional] As definições de política de estado de funcionamento cluster utilizadas para avaliar o estado de funcionamento.
* [Opcional] Filtros para os eventos que especificam as entradas de interesse e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento do nó através da API, crie um `FabricClient` e chame o [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) método no respetivo HealthManager.

O código seguinte obtém o estado de funcionamento do nó para o nome de nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O código seguinte obtém o estado de funcionamento do nó para o nome de nó especificado e passa no filtro de eventos e uma política personalizada através de [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do nó é [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.
O seguinte cmdlet obtém o estado de funcionamento do nó ao utilizar políticas de estado de funcionamento de predefinição:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O seguinte cmdlet obtém o estado de funcionamento de todos os nós do cluster:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento do nó com uma [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-application-health"></a>Obter o estado de funcionamento da aplicação
Devolve o estado de funcionamento de uma entidade de aplicação. Contém os Estados de funcionamento da aplicação implementada e subordinados do serviço. Entrada:

* [Necessário] O nome da aplicação (URI) que identifica a aplicação.
* [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir as políticas de manifesto de aplicação.
* [Opcional] Filtros de eventos, serviços e aplicações implementadas que especificam quais entradas são úteis e devem ser devolvidos nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos, serviços e aplicações implementadas são utilizadas para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.
* [Opcional] Filtro excluir as estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento incluem o ok, aviso e contagem de erros para todos os elementos subordinados de aplicação: serviços de partições, réplicas, aplicações implementadas e implementar pacotes de serviços.

### <a name="api"></a>API
Para obter o estado de funcionamento da aplicação, crie um `FabricClient` e chame o [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) método no respetivo HealthManager.

O código seguinte obtém o estado de funcionamento da aplicação para o nome de aplicação especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código seguinte obtém o estado de funcionamento da aplicação para o nome de aplicação especificado (URI), com os filtros e as políticas personalizadas especificado através do [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento da aplicação é [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O seguinte cmdlet devolve o estado de funcionamento a **fabric: / WordCount** aplicação:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok
                                  
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning
                                  
DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok
                                  
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
                                  
HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

O seguinte cmdlet do PowerShell passa em políticas personalizadas. Filtra também eventos e subordinados.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error
                                  
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento de aplicação com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-service-health"></a>Obter o estado de funcionamento do serviço
Devolve o estado de funcionamento de uma entidade de serviço. Contém os Estados de funcionamento de partição. Entrada:

* [Necessário] O nome do serviço (URI) que identifica o serviço.
* [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir a política de manifesto de aplicação.
* [Opcional] Filtros de eventos e partições que especificam quais entradas são úteis e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos e as partições são utilizadas para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.
* [Opcional] Filtro excluir estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento mostram ok, aviso, e contagem de erros para todas as partições e réplicas do serviço.

### <a name="api"></a>API
Para obter o estado de funcionamento do serviço através da API, crie um `FabricClient` e chame o [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) método no respetivo HealthManager.

O exemplo seguinte obtém o estado de funcionamento de um serviço com o nome de serviço especificado (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O código seguinte obtém o estado de funcionamento do serviço o nome do serviço especificado (URI), especificar os filtros e a política personalizada através de [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do serviço é [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O seguinte cmdlet obtém o estado de funcionamento do serviço através de políticas de estado de funcionamento de predefinição:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                        
                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                        
                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento do serviço com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-partition-health"></a>Obter o estado de funcionamento de partição
Devolve o estado de funcionamento de uma entidade de partição. Contém os Estados de funcionamento de réplica. Entrada:

* [Necessário] A ID (GUID) que identifica a partição de partição.
* [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir a política de manifesto de aplicação.
* [Opcional] Filtros de eventos e as réplicas que especificam quais entradas são úteis e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos e as réplicas são utilizadas para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.
* [Opcional] Filtro excluir estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento mostram quantos réplicas estão em ok, aviso e erro Estados.

### <a name="api"></a>API
Para obter o estado de funcionamento de partição através da API, crie um `FabricClient` e chame o [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) método no respetivo HealthManager. Para especificar os parâmetros opcionais, criar [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento de partição é [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O seguinte cmdlet obtém o estado de funcionamento para todas as partições do **fabric: / WordCount/WordCountService** serviço e os filtros de saída Estados de funcionamento de réplica:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
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
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento de partição com uma [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-replica-health"></a>Obter o estado de funcionamento de réplica
Devolve o estado de funcionamento de uma réplica de monitorização de estado de serviço ou uma instância de serviço sem estado. Entrada:

* [Necessário] O ID de ID (GUID) e a réplica da partição que identifica a réplica.
* [Opcional] Os parâmetros de política de estado de funcionamento aplicação utilizados para substituir as políticas de manifesto de aplicação.
* [Opcional] Filtros para os eventos que especificam as entradas de interesse e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento da réplica através da API, crie um `FabricClient` e chame o [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) método no respetivo HealthManager. Para especificar parâmetros avançados, utilize [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento de réplica é [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O seguinte cmdlet obtém o estado de funcionamento da réplica primária para todas as partições do serviço:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento de réplica com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-deployed-application-health"></a>Obter o estado de funcionamento da aplicação implementada
Devolve o estado de funcionamento de uma aplicação implementada na entidade de um nó. Contém os Estados de funcionamento do pacote de serviço implementado. Entrada:

* [Necessário] O nome da aplicação (URI) e o nome do nó (cadeia) que identificam a aplicação implementada.
* [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir as políticas de manifesto de aplicação.
* [Opcional] Filtros para eventos e pacotes de serviço implementado que especificam quais entradas são úteis e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos e pacotes de serviço implementado são utilizadas para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.
* [Opcional] Filtro excluir estatísticas de estado de funcionamento. Se não for especificado, as estatísticas de estado de funcionamento mostram o número de pacotes de serviços implementados em ok, aviso e erro Estados de funcionamento.

### <a name="api"></a>API
Para obter o estado de funcionamento de uma aplicação implementada num nó através da API, crie um `FabricClient` e chame o [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) método no respetivo HealthManager. Para especificar os parâmetros opcionais, utilize [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento da aplicação implementada é [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Para saber em que uma aplicação é implementada, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e observe os subordinados de aplicação implementada.

O seguinte cmdlet obtém o estado de funcionamento a **fabric: / WordCount** aplicação implementada no **node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM
                                     
HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento da aplicação implementada com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="get-deployed-service-package-health"></a>Obter o estado de funcionamento de pacote de serviço implementado
Devolve o estado de funcionamento de uma entidade de pacote de serviço implementado. Entrada:

* [Necessário] O nome da aplicação (URI), o nome do nó (cadeia) e nome do manifesto do serviço (cadeia) que identificam o pacote de serviço implementado.
* [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir a política de manifesto de aplicação.
* [Opcional] Filtros para os eventos que especificam as entradas de interesse e devem ser devolvidas nos resultados (por exemplo, apenas, erros ou avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento de entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento de um pacote de serviço implementado através da API, crie um `FabricClient` e chame o [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) método no respetivo HealthManager. Para especificar os parâmetros opcionais, utilize [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do pacote de serviço implementado é [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet. Para ver onde uma aplicação é implementada, execute [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e observe as aplicações implementadas. Para ver qual os pacotes são numa aplicação de serviço, observe os subordinados do pacote de serviço implementado no [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) saída.

O seguinte cmdlet obtém o estado de funcionamento a **WordCountServicePkg** pacote do serviço do **recursos de infraestrutura: / WordCount** aplicação implementada no **node_2**. A entidade é o **System.Hosting** relatórios para ativação com êxito do pacote de serviço e o ponto de entrada e de registo do tipo de serviço com êxito.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Pode obter o estado de funcionamento do serviço implementado pacote com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) que inclui as políticas de estado de funcionamento descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de segmento de estado de funcionamento
As consultas de segmento de estado de funcionamento podem devolver subordinados de cluster de múltiplos níveis (recursivamente), por filtros de entrada. Suporta filtros avançados que permitem muita flexibilidade na escolher os subordinados a ser devolvido. Os filtros podem especificar subordinados pelo identificador exclusivo ou por outros os identificadores de grupos e/ou os Estados de funcionamento. Por predefinição, são incluídos, por oposição comandos de estado de funcionamento que incluem sempre o primeiro nível subordinados sem subordinados.

O [consultas de estado de funcionamento](service-fabric-view-entities-aggregated-health.md#health-queries) devolver apenas primeiro nível subordinados a entidade especificada por filtros necessárias. Para obter os subordinados do subordinado, tem de chamar o estado de funcionamento APIs adicional para cada entidade de interesse. Da mesma forma, para obter o estado de funcionamento de entidades específicos, tem de chamar um Estado de funcionamento API para cada entidade pretendida. A consulta de segmento avançada filtragem permite-lhe solicitar vários itens de interesse numa consulta, minimizando o tamanho da mensagem e o número de mensagens.

O valor da consulta de segmento é que pode obter o estado de funcionamento para obter mais entidades de cluster (potencialmente todos os clusters entidades começando raiz necessário) numa chamada. Pode express consulta do Estado de funcionamento complexas tais como:

* Retorno apenas as aplicações num erro de e para essas aplicações incluem todos os serviços de aviso ou erro. Para serviços devolvidos, inclui todas as partições.
* Devolva apenas o estado de funcionamento das quatro aplicações, especificado pelos respetivos nomes.
* Devolva apenas o estado de funcionamento das aplicações de um tipo de aplicação desejada.
* Devolve entidades de todos os implementado num nó. Devolve todas as aplicações, todos os implementar aplicações no nó especificado e todos os pacotes de serviços implementados nesse nó.
* Devolva todas as réplicas no registo de erros. Devolve todas as aplicações, serviços, partições e réplicas apenas no registo de erros.
* Devolva todas as aplicações. Para um serviço especificado, inclui todas as partições.

Atualmente, a consulta de segmento de estado de funcionamento está exposta apenas para a entidade de cluster. Devolve um segmento de estado de funcionamento do cluster, que contém:

* O estado de funcionamento de agregados de cluster.
* A Estado de funcionamento Estado segmento a lista de nós que Respeitamos a entrada de filtros.
* Lista segmentos de estado de funcionamento estado das aplicações que Respeitamos a entrada de filtros. Cada segmento de estado de funcionamento de aplicação contém uma lista de segmentos com todos os serviços que respeitem filtros de entrada e uma lista de segmentos com todas as aplicações implementadas respeitamos os filtros. Mesmo para os elementos subordinados de serviços e aplicações implementadas. Desta forma, todas as entidades do cluster podem potencialmente devolvidas se solicitado, de uma forma hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de segmento de estado de funcionamento de cluster
Devolve o estado de funcionamento da entidade de cluster e contém os segmentos de estado de funcionamento hierárquica de subordinados necessários. Entrada:

* [Opcional] A política de estado de funcionamento do cluster utilizada para avaliar os nós e os eventos de cluster.
* [Opcional] O estado de funcionamento política o mapeamento de aplicações, com as políticas de estado de funcionamento utilizada para substituir as políticas de manifesto de aplicação.
* [Opcional] Filtros para nós e aplicações que especificam quais entradas são úteis e devem ser devolvidas nos resultados. Os filtros são específicos para um entidade/grupo de entidades ou são aplicáveis a todas as entidades nesse nível. A lista de filtros pode conter um gerais de filtro de e/ou filtros para identificadores específicos para entidades Cativação devolvidos pela consulta. Se estiver vazio, o subordinado não é devolvido por predefinição.
  Saiba mais sobre os filtros na [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) e [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). O recursivamente de pode de filtros de aplicação Especifique filtros avançados para elementos subordinados.

O resultado de segmento inclui os subordinados que respeitem os filtros.

Atualmente, a consulta de segmento não devolve avaliações mau estado de funcionamento ou eventos de entidade. Que é possível obter informações adicionais utilizando a consulta de estado de funcionamento do cluster existente.

### <a name="api"></a>API
Para obter o segmento de estado de funcionamento do cluster, crie um `FabricClient` e chame o [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) método no respetivo **HealthManager**. Pode passar no [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) para descrever as políticas de estado de funcionamento e filtros avançados.

O código seguinte obtém o segmento de estado de funcionamento do cluster com filtros avançados.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do cluster é [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Em primeiro lugar, ligar ao cluster utilizando o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet.

O código seguinte obtém nós apenas se estiverem no registo de erros, exceto para um nó específico, o que deve sempre ser devolvido.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1
                               
                               NodeName              : _Node_1
                               HealthState           : Ok
                               
ApplicationHealthStateChunks : None
```

O seguinte cmdlet obtém os segmentos de cluster com filtros de aplicação.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1
                               
                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1
                               
                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5
                               
                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

O seguinte cmdlet devolve todas as entidades implementadas num nó.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2
                               
                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
                               
                               
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Pode obter o segmento de estado de funcionamento de cluster com um [pedido GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) ou um [pedido POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) que inclui políticas de estado de funcionamento e filtros avançados descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
Consultas gerais devolvem uma lista de entidades do Service Fabric de um tipo especificado. Estes são expostos através da API (através de métodos em **FabricClient.QueryManager**), cmdlets do PowerShell e REST. Estas consultas agregar subconsultas de vários componentes. Uma delas é o [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store), que preenche o estado de funcionamento agregados para cada resultado da consulta.  

> [!NOTE]
> As consultas gerais devolvem o estado agregado de estado de funcionamento da entidade e não contêm dados de estado de funcionamento avançado. Se uma entidade não está em bom estada, pode seguir cópias de segurança com consultas de estado de funcionamento para obter todas as respetivas informações de estado de funcionamento, incluindo eventos, Estados de funcionamento de subordinados e avaliações mau estado de funcionamento.
>
>

Se as consultas gerais devolverem um Estado de funcionamento desconhecido para uma entidade, é possível que o arquivo de estado de funcionamento não tem dados completos sobre a entidade. Também é possível que uma subconsulta para o arquivo de estado de funcionamento não foi concluída com êxito (por exemplo, Ocorreu um erro de comunicação ou o arquivo de estado de funcionamento foi limitado). Siga a cópia de segurança com uma consulta do Estado de funcionamento da entidade. Se a subconsulta encontrou erros transitórios, tais como problemas de rede, pode autenticar esta consulta de seguimento. -Pode também lhe irá fornecer mais detalhes do arquivo de estado de funcionamento sobre por que motivo não está exposta a entidade.

As consultas que contêm **HealthState** para entidades são:

* Lista de nó: devolve os nós de lista no cluster (bloco paginado).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Lista de aplicações: devolve a lista de aplicações no cluster (bloco paginado).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Lista de serviço: devolve a lista de serviços de uma aplicação (bloco paginada).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Lista de partição: devolve a lista de partições num serviço (bloco paginado).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Lista de réplica: devolve a lista de réplicas numa partição (bloco paginada).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Implementar a lista de aplicações: devolve a lista de aplicações implementadas num nó.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Implementar a lista de pacote de serviço: devolve a lista de pacotes do serviço numa aplicação implementada.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Algumas das consultas devolvem resultados paginados. O retorno destas consultas é uma lista derivada [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Se os resultados não se enquadram uma mensagem, é devolvida apenas uma página e um ContinuationToken que controla onde parou a enumeração. Continue a chamar a mesma consulta e passar o token de continuação da consulta anterior para obter resultados seguintes.
>
>

### <a name="examples"></a>Exemplos
O código seguinte obtém as aplicações em mau estado de funcionamento do cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O seguinte cmdlet obtém os detalhes da aplicação para os recursos de infraestrutura: / aplicação WordCount. Tenha em atenção que o estado de funcionamento é em aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

O seguinte cmdlet obtém os serviços com um Estado de funcionamento de erro:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Atualizações de cluster e da aplicação
Durante uma atualização monitorizada do cluster e da aplicação, o Service Fabric verifica o estado de funcionamento para se certificar de que tudo permanece bom estado de funcionamento. Se uma entidade está danificada, avaliada através de políticas de estado de funcionamento configurado, a atualização se aplica a políticas de atualização específicas para determinar a ação seguinte. A atualização pode ser colocado em pausa para permitir a interação do utilizador (tais como corrigir condições de erro ou alteração de políticas), ou pode automaticamente reverter para a versão de boa anterior.

Durante uma *cluster* atualização, pode obter o estado de atualização do cluster. O estado de atualização inclui avaliações do mau estado de funcionamento, apontem para o que é mau estado de funcionamento do cluster. Se a atualização for revertida devido a problemas de estado de funcionamento, o estado de atualização memorizou os motivos pelos quais último mau estado de funcionamento. Esta informação pode ajudar os administradores a investigar o que aconteceu após a atualização revertida ou parado.

Da mesma forma, durante um *aplicação* atualização, as avaliações de mau estado de funcionamento estão contidas no estado de atualização da aplicação.

O seguinte mostra o estado de atualização da aplicação para um recurso de infraestrutura modificado: / aplicação WordCount. Um watchdog do comunicou um erro das suas réplicas. A atualização está a implementar porque as verificações de estado de funcionamento não são respeitadas.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Leia mais sobre o [atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Utilizar avaliações do Estado de funcionamento para resolver problemas
Sempre que há um problema com o cluster ou de uma aplicação, veja o estado de funcionamento de cluster ou aplicação para identificar o que é incorreto. As avaliações de mau estado de funcionamento fornecem detalhes sobre o que acionou o estado de mau estado de funcionamento atual. Se for necessário, pode explorar para baixo entidades subordinado mau estado de funcionamento para identificar a causa raiz.

Por exemplo, considere uma aplicação mau estado de funcionamento porque não existe um relatório de erros das suas réplicas. O cmdlet Powershell seguinte mostra as avaliações de mau estado de funcionamento:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.
                                  
                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.
                                  
                                            Error event: SourceId='MyWatchdog', Property='Memory'.
                                  
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Pode ver a réplica para obter mais informações:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> As avaliações de mau estado de funcionamento mostram que o motivo primeiro a entidade é avaliado como estado de funcionamento atual. Podem existir vários outros eventos que activam este estado, mas não são refletidas nas avaliações. Para obter mais informações, desagregar as entidades de estado de funcionamento para descobrir a todos os relatórios mau estado de funcionamento do cluster.
>
>

## <a name="next-steps"></a>Passos seguintes
[Use system health reports to troubleshoot (Utilizar relatórios de estado de funcionamento do sistema para resolver problemas)](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de estado de funcionamento personalizados do Service Fabric](service-fabric-report-health.md)

[Como comunicar e verifique o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)
