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
ms.date: 08/09/2017
ms.author: motanv
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induce Chaos controlada em clusters de Service Fabric
Sistemas distribuídos em grande escala, como infraestruturas de nuvem são inerentemente pouco fiáveis. Recursos de infraestrutura de serviço do Azure permite aos programadores escrever fiáveis serviços distribuídos por cima de uma infraestrutura pouco fiável. Para escrever robustos serviços distribuídos por cima de uma infraestrutura pouco fiável, os programadores têm de ser capaz de testar a estabilidade do respetivos serviços enquanto infraestrutura subjacente pouco fiável passar pelas transições de estado complicado devido a falhas.

O [Injeção de falhas e Cluster Analysis Service](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (também conhecido como o serviço de análise de falhas) permite aos programadores do induce falhas para testar os respetivos serviços. Estes direcionados simulated falhas, como [reiniciar uma partição](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), pode ajudá-lo a exercer transições de estado mais comuns. No entanto visadas falhas simuladas são totalmente direcionadas por definição e, por isso, poderão perder erros que mostram cópias de segurança apenas na sequência de disco rígido prever, longa e complicada de transições de estado. Para uma povoação não teste, pode utilizar Chaos.

Chaos simula periódicas, intercaladas falhas (correto e ungraceful) em todo o cluster ao longo de períodos de tempo prolongados. Assim que tiver configurado Chaos com a frequência e o tipo de falhas de hardware, pode começar a Chaos através de c# ou API do Powershell para iniciar a geração de falhas no cluster e nos serviços. Pode configurar Chaos sejam executadas durante um período de tempo especificado (por exemplo, para uma hora), após o qual interrompe Chaos automaticamente ou pode chamar a API de StopChaos (c# ou o Powershell) para parar a qualquer momento.

> [!NOTE]
> Na sua forma atual, Chaos induces falhas apenas seguras, que indica que a ausência de falhas externas de uma perda de quórum, ou perda de dados nunca ocorre.
>

Enquanto Chaos está em execução, produz diferentes eventos de capturar o estado de execução neste momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas Chaos decidiu para ser executado nesse iteração. Um ValidationFailedEvent contém os detalhes de uma falha de validação (problemas de estado de funcionamento ou estabilidade) que foi encontrado durante a validação do cluster. Pode invocar a API de GetChaosReport (c# ou o Powershell) para obter o relatório de execuções de Chaos. Estes eventos obterem continuados num [dicionário fiável](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncagem ditada pelo duas configurações: **MaxStoredChaosEventCount** (o valor predefinido é 25000) e  **StoredActionCleanupIntervalInSeconds** (valor predefinido é de 3600). Cada *StoredActionCleanupIntervalInSeconds* Chaos verificações e a todos os, mas o mais recente *MaxStoredChaosEventCount* eventos, são removidos do dicionário de fiável.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no Chaos
Chaos gera falhas em todo o cluster do Service Fabric e comprimir falhas que o se verificam no meses ou anos para algumas horas. A combinação de falhas intercaladas com a taxa de falhas elevada encontra nos casos extremos que caso contrário, poderão ser perdidos. Neste exercício de Chaos leva a uma melhoria significativa na qualidade de código do serviço.

Chaos induces falhas de seguintes categorias:

* Reinicia um nó
* Reiniciar um pacote do código implementado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (configurável)
* Mover uma réplica secundária (configurável)

Chaos é executado em múltiplas interações. Cada iteração é composta por falhas e validação de cluster para o período de tempo especificado. Pode configurar o tempo gasto para o cluster para stabilize e para a validação com êxito. Se for encontrada uma falha na validação de cluster, Chaos gera e persistir um ValidationFailedEvent com o UTC timestamp e os detalhes da falha. Por exemplo, considere uma instância de Chaos que está configurada para ser executada para uma hora com um máximo de três falhas em simultâneo. Chaos induces três falhas e, em seguida, valida o estado de funcionamento do cluster. -Itera através de anterior transmite passo até que esteja parado através da API de StopChaosAsync ou explicitamente uma hora. Se o cluster fica em mau estado de funcionamento em qualquer iteração (ou seja, não stabilize dentro de MaxClusterStabilizationTimeout transmitido no), Chaos gera um ValidationFailedEvent. Este evento indica que algo tornou errado e pode necessitar de investigação adicional.

Para obter as falhas Chaos induzida, pode utilizar a API GetChaosReport (powershell ou c#). A API obtém o segmento seguinte do relatório Chaos baseado no token de continuação transmitido na ou o transmitido no-intervalo de tempo. Pode especificar o ContinuationToken para obter o segmento seguinte do relatório Chaos ou pode especificar o intervalo de tempo através de StartTimeUtc e EndTimeUtc, mas não é possível especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando existe mais do que 100 eventos de Chaos, é devolvido o relatório de Chaos em segmentos em que um segmento contém mais do que 100 eventos de Chaos.

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
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

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

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

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
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

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
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
