---
title: "Criar testes de chaos e ativação pós-falha do Azure micro-serviços | Microsoft Docs"
description: "Utilizando o Service Fabric teste chaos e ativação pós-falha de teste cenários induce falhas e verificar a fiabilidade dos seus serviços."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d06026c750e01ad5825338a78d9af331265f434a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="testability-scenarios"></a>Cenários de teste
Sistemas distribuídos grande como infraestruturas de nuvem são inerentemente pouco fiáveis. Azure Service Fabric fornece aos programadores a capacidade de escrever os serviços são executados sobre infraestruturas pouco fiáveis. Para escrever os serviços de alta qualidade, os programadores têm de conseguir induce essa infraestrutura pouco fiável para testar a estabilidade nos serviços.

O serviço de análise de falhas permite que os programadores a induce ações de falhas para testar os serviços na presença de falhas. No entanto, falhas simuladas visadas irão ajudá-lo apenas até ao momento. Para tirar o teste adicional, pode utilizar os cenários de teste no Service Fabric: um teste de chaos e uma ativação pós-falha de teste. Estes cenários simulam contínuas falhas intercaladas correto e ungraceful, todo o cluster ao longo de períodos de tempo prolongados. Depois de um teste está configurado com a taxa e o tipo de falhas de hardware, que possa ser iniciado através de APIs de c# ou PowerShell, para gerar falhas de cluster e o seu serviço.

> [!WARNING]
> ChaosTestScenario está a ser substituído por um Chaos mais resiliente, com base no serviço. Consulte o artigo novo [controlada Chaos](service-fabric-controlled-chaos.md) para obter mais detalhes.
> 
> 

## <a name="chaos-test"></a>Teste Chaos
O cenário de chaos gera falhas em todo o cluster do Service Fabric. O cenário de comprime falhas geralmente é vistas na meses ou anos para algumas horas. A combinação de falhas intercaladas com a taxa de falhas elevada encontra nos casos extremos que caso contrário, estão em falta. Isto leva a uma melhoria significativa na qualidade de código do serviço.

### <a name="faults-simulated-in-the-chaos-test"></a>Falhas simuladas no teste chaos
* Reinicia um nó
* Reiniciar um pacote do código implementado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (opcional)
* Mover uma réplica secundária (opcional)

O teste de chaos é executado múltiplas interações de falhas e validações de cluster para o período de tempo especificado. O tempo gasto para o cluster para stabilize e para a validação teve êxito também é configurável. O cenário de falha quando acessos de falha na validação de cluster.

Por exemplo, considere um teste definido para ser executado por uma hora com um máximo de três falhas em simultâneo. O teste será induce três falhas e, em seguida, validar o estado de funcionamento do cluster. O teste será iterar através do passo anterior até o cluster fica danificado ou passar de uma hora. Se o cluster ficar danificado em qualquer iteração, ou seja, não stabilize dentro de um período de tempo configurado, o teste falhará com uma exceção. Esta exceção indica que algo tornou errado e precisa de investigação adicional.

Na sua forma atual, o motor de geração de falha no teste chaos induces apenas seguras falhas. Isto significa que na ausência de falhas externas, uma quórum ou perda de dados nunca ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: Total de tempo que o teste será executado antes de concluir com êxito. O teste pode concluir anteriormente in lieu of uma falha de validação.
* **MaxClusterStabilizationTimeout**: quantidade máxima de tempo de espera para o cluster para se tornarem bom estado de funcionamento antes do teste a falhar. As verificações executadas são indica se o estado de funcionamento do cluster está OK, estado de funcionamento do serviço está OK, o tamanho de conjunto de réplicas de destino é obtido para a partição de serviço e não réplicas InBuild existem.
* **MaxConcurrentFaults**: induzida pelo número máximo de falhas em simultâneo em cada iteração. Quanto maior for o número, mais agressiva o teste, por conseguinte, resultando em combinações de transição e as ativações pós-falha mais complexas. O teste garante que na ausência de falhas externas não haverá uma quórum ou perda de dados, independentemente desta configuração como elevada é.
* **EnableMoveReplicaFaults**: ativa ou desativa as falhas que estão a causar a movimentação das réplicas primários ou secundários. Estas falhas estão desativadas por predefinição.
* **WaitTimeBetweenIterations**: quantidade de tempo de espera entre iterações, ou seja, após um round de falhas e de validação correspondente.

### <a name="how-to-run-the-chaos-test"></a>Como executar o teste de chaos
Exemplo C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Ativação pós-falha de teste
O cenário de teste de ativação pós-falha é uma versão do cenário de teste chaos que tenha como destino uma partição de serviço específicos. Testa ao efeito de ativação pós-falha numa partição de serviço específicos, mantendo os outros serviços afetados. Depois de ter configurado com as informações de partição de destino e outros parâmetros, que seja executada como uma ferramenta do lado do cliente que utiliza o c# APIs ou o PowerShell para gerar falhas para uma partição de serviço. O cenário itera através de uma sequência de falhas simuladas e validação do serviço enquanto a lógica de negócio é executada no lado para fornecer uma carga de trabalho. Uma falha na validação de serviço indica um problema que tem mais de investigação.

### <a name="faults-simulated-in-the-failover-test"></a>Falhas simuladas no teste de ativação pós-falha
* Reiniciar um pacote do código implementado em que a partição está alojada
* Remover uma réplica primária/secundário ou a instância sem monitorização de estado
* Reiniciar uma réplica secundária primária (se um serviço persistente)
* Mover uma réplica primária
* Mover uma réplica secundária
* Reinicie a partição

A ativação pós-falha de teste induces uma falha que escolheu e, em seguida, executa a validação do serviço para garantir a estabilidade. A ativação pós-falha de teste induces falhas apenas um cada vez, por oposição a possível várias falhas no teste chaos. Se a partição de serviço não stabilize dentro do limite de tempo configurado após cada de falhas, o teste falha. O teste induces apenas seguras falhas. Isto significa que na ausência de falhas externas, uma quórum ou perda de dados não ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **PartitionSelector**: objeto de Seletor que especifica a partição que tem de ser a visada.
* **TimeToRun**: Total de tempo que o teste será executado antes de concluir.
* **MaxServiceStabilizationTimeout**: quantidade máxima de tempo de espera para o cluster para se tornarem bom estado de funcionamento antes do teste a falhar. As verificações executadas são indica se o estado de funcionamento do serviço está OK, o tamanho de conjunto de réplicas de destino é obtido para todas as partições e nenhum réplicas InBuild existem.
* **WaitTimeBetweenFaults**: quantidade de tempo de espera entre cada ciclo de validação e falhas.

### <a name="how-to-run-the-failover-test"></a>Como executar o teste de ativação pós-falha
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
