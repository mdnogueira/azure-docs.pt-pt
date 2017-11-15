---
title: Induce Chaos em clusters de Service Fabric | Microsoft Docs
description: "Utilizar a inserção de falhas e APIs de serviço de análise do Cluster para gerir Chaos no cluster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: motanv
ms.openlocfilehash: c78d9e77d807f3ccf8c1f56d856abad8135989c2
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induce Chaos controlada em clusters de Service Fabric
Sistemas distribuídos em grande escala, como infraestruturas de nuvem são inerentemente pouco fiáveis. Recursos de infraestrutura de serviço do Azure permite aos programadores escrever fiáveis serviços distribuídos por cima de uma infraestrutura pouco fiável. Para escrever robustos serviços distribuídos por cima de uma infraestrutura pouco fiável, os programadores têm de ser capaz de testar a estabilidade do respetivos serviços enquanto infraestrutura subjacente pouco fiável passar pelas transições de estado complicado devido a falhas.

O [Injeção de falhas e Cluster Analysis Service](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (também conhecido como o serviço de análise de falhas) permite aos programadores do induce falhas para testar os respetivos serviços. Estes direcionados simulated falhas, como [reiniciar uma partição](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), pode ajudá-lo a exercer transições de estado mais comuns. No entanto visadas falhas simuladas são totalmente direcionadas por definição e, por isso, poderão perder erros que mostram cópias de segurança apenas na sequência de disco rígido prever, longa e complicada de transições de estado. Para uma povoação não teste, pode utilizar Chaos.

Chaos simula periódicas, intercaladas falhas (correto e ungraceful) em todo o cluster ao longo de períodos de tempo prolongados. Uma falha correto consiste num conjunto de chamadas à API de recursos de infraestrutura de serviço, por exemplo, o reinício de falhas de réplica é uma falha correto porque se trata de um fecho seguido por um aberto numa réplica. Remover a réplica, mova a réplica primária e réplica secundária de movimentação são outras falhas correto executadas por Chaos. Falhas ungraceful são sai do processo, tal como reiniciar o nó e restrat pacote do código. 

Assim que tiver configurado Chaos com a frequência e o tipo de falhas de hardware, pode começar a Chaos através de c#, Powershell ou REST API para iniciar a geração de falhas no cluster e nos serviços. Pode configurar Chaos sejam executadas durante um período de tempo especificado (por exemplo, para uma hora), após o qual interrompe Chaos automaticamente ou pode chamar a API de StopChaos (c#, Powershell ou REST) para parar a qualquer momento.

> [!NOTE]
> Na sua forma atual, Chaos induces falhas apenas seguras, que indica que a ausência de falhas externas de uma perda de quórum, ou perda de dados nunca ocorre.
>

Enquanto Chaos está em execução, produz diferentes eventos de capturar o estado de execução neste momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas Chaos decidiu para ser executado nesse iteração. Um ValidationFailedEvent contém os detalhes de uma falha de validação (problemas de estado de funcionamento ou estabilidade) que foi encontrado durante a validação do cluster. Pode invocar a API de GetChaosReport (c#, Powershell ou REST) para obter o relatório de execuções de Chaos. Estes eventos obterem continuados num [dicionário fiável](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncagem ditada pelo duas configurações: **MaxStoredChaosEventCount** (o valor predefinido é 25000) e  **StoredActionCleanupIntervalInSeconds** (valor predefinido é de 3600). Cada *StoredActionCleanupIntervalInSeconds* Chaos verificações e a todos os, mas o mais recente *MaxStoredChaosEventCount* eventos, são removidos do dicionário de fiável.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no Chaos
Chaos gera falhas em todo o cluster do Service Fabric e comprimir falhas que o se verificam no meses ou anos para algumas horas. A combinação de falhas intercaladas com a taxa de falhas elevada encontra nos casos extremos que caso contrário, poderão ser perdidos. Neste exercício de Chaos leva a uma melhoria significativa na qualidade de código do serviço.

Chaos induces falhas de seguintes categorias:

* Reinicia um nó
* Reiniciar um pacote do código implementado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (configurável)
* Mover uma réplica secundária (configurável)

Chaos é executado em múltiplas interações. Cada iteração é composta por falhas e validação de cluster para o período de tempo especificado. Pode configurar o tempo gasto para o cluster para stabilize e para a validação com êxito. Se for encontrada uma falha na validação de cluster, Chaos gera e persistir um ValidationFailedEvent com o UTC timestamp e os detalhes da falha. Por exemplo, considere uma instância de Chaos que está configurada para ser executada para uma hora com um máximo de três falhas em simultâneo. Chaos induces três falhas e, em seguida, valida o estado de funcionamento do cluster. -Itera através de anterior transmite passo até que esteja parado através da API de StopChaosAsync ou explicitamente uma hora. Se o cluster fica em mau estado de funcionamento em qualquer iteração (ou seja, não a ser stabilize ou não fiquem bom dentro de MaxClusterStabilizationTimeout transmitido no), Chaos gera um ValidationFailedEvent. Este evento indica que algo tornou errado e pode necessitar de investigação adicional.

Para obter as falhas Chaos induzida, pode utilizar a API GetChaosReport (powershell, c# ou REST). A API obtém o segmento seguinte do relatório Chaos baseado no token de continuação transmitido na ou o transmitido no-intervalo de tempo. Pode especificar o ContinuationToken para obter o segmento seguinte do relatório Chaos ou pode especificar o intervalo de tempo através de StartTimeUtc e EndTimeUtc, mas não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando existe mais do que 100 eventos de Chaos, é devolvido o relatório de Chaos em segmentos em que um segmento contém mais do que 100 eventos de Chaos.

## <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: Total de tempo que é executado Chaos antes de concluir com êxito. Pode parar Chaos antes de ter executado durante o período de TimeToRun através da API de StopChaos.

* **MaxClusterStabilizationTimeout**: A quantidade máxima de tempo de espera para o cluster para se tornarem bom estado de funcionamento antes que produzem um ValidationFailedEvent. Este espera é para reduzir a carga no cluster enquanto está a recuperar. As verificações executadas são:
  * Se o estado de funcionamento do cluster é OK
  * Se o estado de funcionamento do serviço é OK
  * Se o tamanho do conjunto de réplicas de destino é obtida para a partição de serviço
  * Se nenhum réplicas InBuild existe
* **MaxConcurrentFaults**: O número máximo de falhas em simultâneo que são induzida em cada iteração. É o Chaos o número, mais agressiva superior e as ativações pós-falha e as combinações de transição de estado que atravessa o cluster também são mais complexas. 

> [!NOTE]
> Independentemente disso como um valor *MaxConcurrentFaults* tem Chaos garante - na ausência de falhas externas - não é qualquer perda de quórum ou perda de dados.
>

* **EnableMoveReplicaFaults**: ativa ou desativa as falhas que fazer com que as réplicas primários ou secundários mover. Estas falhas estão desativadas por predefinição.
* **WaitTimeBetweenIterations**: A quantidade de tempo de espera entre iterações. Ou seja, a quantidade de tempo Chaos será colocado em pausa depois de ter executado um round de falhas e ter concluído a validação do Estado de funcionamento do cluster correspondente. Quanto maior for o valor, menor é a taxa de inserção de média de falhas.
* **WaitTimeBetweenFaults**: A quantidade de tempo de espera entre duas falhas consecutivas numa único iteração. Maior o valor, o inferior a simultaneidade de (ou a sobreposição entre) falhas.
* **ClusterHealthPolicy**: política de estado de funcionamento do Cluster é utilizada para validar o estado de funcionamento do cluster entre iterações Chaos. Se o estado de funcionamento do cluster é um erro ou se ocorre uma exceção inesperada durante a execução de falhas, Chaos irá aguardar antes do Estado de funcionamento seguinte-Verifique - para fornecer o cluster com algum tempo a recuperate de 30 minutos.
* **Contexto**: uma coleção de (cadeia, cadeia) escreva os pares chave-valor. O mapa pode ser utilizado para registar informações sobre a execução de Chaos. Não é possível existir mais do que 100 essas pares e cada cadeia (chave ou valor) pode ter no máximo 4095 carateres de comprimento. Este mapa está definido pelo arranque de Chaos executar armazenar opcionalmente o contexto sobre a execução específico.
* **ChaosTargetFilter**: este filtro pode ser utilizado para falhas de Chaos de destino apenas a determinados tipos de nó ou apenas a determinados instâncias da aplicação. Se não for utilizado ChaosTargetFilter, Chaos faults todas as entidades de cluster. Se for utilizado ChaosTargetFilter, Chaos faults apenas as entidades que cumprem a especificação de ChaosTargetFilter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas a semântica de União. Por outras palavras, não é possível especificar uma intersecção de NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falhas desta aplicação apenas quando é nesse tipo de nó." Depois de uma entidade está incluída no NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não é possível excluir ChaosTargetFilter. Mesmo se applicationX não for apresentada ApplicationInclusionList, em algumas iteração Chaos applicationX pode falhar porque e estar num nó de nodeTypeY que está incluído na NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList são nulos ou estar vazio, é emitida uma ArgumentException.
    * **NodeTypeInclusionList**: uma lista de tipos de nó para incluir no falhas Chaos. Todos os tipos de falhas de hardware (reiniciar nó, reinicie codepackage, remover a réplica, reinicie a réplica, mover primário e mover secundário) estão ativadas para os nós dos seguintes tipos de nó. Se um nodetype (diga NodeTypeX) não for apresentada NodeTypeInclusionList, em seguida, falhas de nível de nó (como NodeRestart) nunca irão estar ativadas para os nós do NodeTypeX, mas a falhas de pacote e a réplica de código ainda podem ser ativadas para NodeTypeX se uma aplicação no ApplicationInclusionList acontece residam num nó do NodeTypeX. No máximo 100 nó os nomes de tipo podem ser incluídos nesta lista, para aumentar este número, uma atualização da configuração é necessária para a configuração de MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: uma lista de aplicações URIs para incluir no falhas Chaos. Todas as réplicas que pertencem aos serviços destas aplicações são propensos a falhas de réplica (reinício réplica, remova réplica, move, principais e mover secundários) por Chaos. Chaos pode reiniciar um pacote do código apenas se o pacote do código aloja réplicas apenas destas aplicações. Se uma aplicação não for apresentada nesta lista,-lo pode ainda ser falhou em alguns iteração Chaos se a aplicação termine a cópia de segurança num nó de um tipo de nó que seja incuded no NodeTypeInclusionList. No entanto se applicationX está associada ao nodeTypeY através de restrições de posicionamento e applicationX está ausente da ApplicationInclusionList e nodeTypeY está ausente da NodeTypeInclusionList, em seguida, applicationX irá nunca falhar. No máximo, os nomes das aplicações de 1000 podem ser incluídas nesta lista, para aumentar este número, uma atualização da configuração é necessária para a configuração de MaxNumberOfApplicationsInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Como executar Chaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
Git 