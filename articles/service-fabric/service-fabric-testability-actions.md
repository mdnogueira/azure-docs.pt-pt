---
title: "Simular falhas no Azure micro-serviços | Microsoft Docs"
description: "Este artigo aborda as ações de teste foi encontradas no Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: toddabel
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv;heeldin
ms.openlocfilehash: c8ddc7732999ae555323bebaef60aa34c8f2ec17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="testability-actions"></a>Ações de teste
Para simular uma pouco fiável infraestrutura, o Azure Service Fabric fornece-lhe, o programador, com formas para simular várias falhas do mundo real e transições de estado. Estes são expostos como ações de teste. As ações são as APIs de baixo nível que fazer com que uma inserção de falhas específicas, transição de estado ou validação. Ao combinar estas ações, pode escrever cenários de teste abrangente para os serviços.

O Service Fabric fornece que alguns cenários comuns de teste é composto por estas ações. Recomendamos vivamente que utilizam estes cenários incorporados, que são escolhidos cuidadosamente para testar transições de Estados de comum e cenários de falha. No entanto, as ações podem ser utilizadas para criar cenários de teste personalizado quando pretender adicionar cobertura para cenários que não são abrangidos pelos cenários incorporados ainda ou que são personalizados adaptados para a sua aplicação.

C# implementações das ações encontram-se na assemblagem System.Fabric.dll. O módulo do PowerShell de recursos de infraestrutura de sistema foi encontrado na assemblagem Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação de tempo de execução, o módulo do ServiceFabric PowerShell está instalado para permitir a facilidade de utilização.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Correto vs ações ungraceful falhas
Ações de teste são classificadas em dois registos principais:

* Falhas ungraceful: estas falhas simular falhas como reinícios da máquina e de falhas de processos. Nestes casos de falhas, contexto de execução do processo interrompe abruptamente. Isto significa que nenhum limpeza do Estado pode ser executado antes da aplicação começa novamente.
* Falhas correto: estas falhas simulam correto ações como move de réplica e remoções acionadas por balanceamento de carga. Nestes casos, o serviço obtém uma notificação do fechar, pode limpar o estado antes de sair.

Para melhor validação da qualidade, execute a carga de trabalho de serviço e empresariais ao inducing várias falhas correto e ungraceful. Falhas ungraceful exercer cenários em que o processo de serviço sai abruptamente meio algumas fluxo de trabalho. Isto testa o caminho de recuperação depois da réplica de serviço é restaurada pelo Service Fabric. Isto irá ajudar a testar a consistência dos dados e se o estado de serviço é mantido corretamente após falhas. O outro conjunto de teste de falhas (as falhas correto) que o serviço reage corretamente para as réplicas que está a ser movidas em torno do Service Fabric. Isto testa o processamento de cancelamento no método runasync com. O serviço tem de verificar o token de cancelamento ser definido, corretamente guardar o estado e sair do método runasync com.

## <a name="testability-actions-list"></a>Lista de ações de teste
| Ação | Descrição | API gerido | Cmdlet do PowerShell | Falhas correto/ungraceful |
| --- | --- | --- | --- | --- |
| CleanTestState |Remove todos os Estados de teste do cluster em caso de um encerramento incorreto do controlador de teste. |CleanTestStateAsync |Remove-ServiceFabricTestState |Não aplicável |
| InvokeDataLoss |Induces perda de dados para uma partição de serviço. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Correto |
| InvokeQuorumLoss |Coloca uma partição de serviço com estado fornecido no perda de quórum. |InvokeQuorumLossAsync |ServiceFabricQuorumLoss invocar |Correto |
| Mover primário |Move a réplica primária especificada de um serviço com monitorização de estado para o nó de cluster especificado. |MovePrimaryAsync |Mover ServiceFabricPrimaryReplica |Correto |
| Mover secundário |Move a réplica secundária atual de um serviço com monitorização de estado para um nó de cluster diferente. |MoveSecondaryAsync |Mover ServiceFabricSecondaryReplica |Correto |
| RemoveReplica |Simula uma falha de réplica ao remover uma réplica de um cluster. Isto irá fechar a réplica e irão transitar para a função 'None', remover todos os Estado do cluster. |RemoveReplicaAsync |Remover ServiceFabricReplica |Correto |
| RestartDeployedCodePackage |Simula uma falha de processo de pacote do código reiniciando um pacote do código implementado num nó num cluster. Isto interrompe o processo de pacote do código, que será reiniciado todas as réplicas de serviço de utilizador alojadas no processo. |RestartDeployedCodePackageAsync |ServiceFabricDeployedCodePackage de reinício |Ungraceful |
| RestartNode |Simula uma falha de nó de cluster do Service Fabric reiniciando um nó. |RestartNodeAsync |ServiceFabricNode de reinício |Ungraceful |
| RestartPartition |Simula um cenário de blackout datacenter blackout ou cluster reiniciando algumas ou todas as réplicas de uma partição. |RestartPartitionAsync |Restart-ServiceFabricPartition |Correto |
| RestartReplica |Simula uma falha de réplica ao reiniciar uma réplica persistente num cluster, a fechar a réplica e, em seguida, abri-lo. |RestartReplicaAsync |ServiceFabricReplica de reinício |Correto |
| StartNode |Inicia um nó num cluster que já é parado. |StartNodeAsync |Start-ServiceFabricNode |Não aplicável |
| StopNode |Simula uma falha de nó por parar um nó num cluster. O nó permanecerão para baixo até StartNode é chamado. |StopNodeAsync |Stop-ServiceFabricNode |Ungraceful |
| ValidateApplication |Valida a disponibilidade e o estado de funcionamento de todos os serviços do Service Fabric dentro de uma aplicação, normalmente, após inducing algumas falhas no sistema. |ValidateApplicationAsync |Teste ServiceFabricApplication |Não aplicável |
| ValidateService |Valida a disponibilidade e o estado de funcionamento de um serviço do Service Fabric, geralmente após inducing algumas falhas no sistema. |ValidateServiceAsync |Teste ServiceFabricService |Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Execução de uma ação de teste com o PowerShell
Este tutorial mostra como executar uma ação de teste utilizando o PowerShell. Ficará a saber como executar uma ação de teste contra um cluster (uma caixa) local ou um cluster do Azure. Microsoft.Fabric.Powershell.dll – o módulo do PowerShell de Service Fabric – é instalado automaticamente quando instala o MSI de recursos de infraestrutura de serviço da Microsoft. O módulo é carregado automaticamente quando abrir uma linha de comandos do PowerShell.

Tutorial segmentos:

* [Executar uma ação contra um cluster de um caixa](#run-an-action-against-a-one-box-cluster)
* [Executar uma ação contra um cluster do Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação contra um cluster de um caixa
Para executar uma ação de teste contra um cluster local, primeiro ligar ao cluster e abra a linha de comandos do PowerShell no modo de administrador. Observe informe-na **reinício ServiceFabricNode** ação.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui, a ação **reinício ServiceFabricNode** está a ser executado num nó com o nome "Nó1". O modo de conclusão Especifica que este deve não verificar se a ação de reinício do nó, na verdade, foi concluída com êxito. Especificar o modo de conclusão como "Verificar" fará com que verificar se a ação de reinício, na verdade, foi concluída com êxito. Em vez de especificar diretamente no nó pelo respetivo nome, pode especificar, através de uma chave de partição e o tipo de réplica, da seguinte forma:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Reinício ServiceFabricNode** devem ser utilizadas para reiniciar um nó de Service Fabric no cluster. Isto irá parar o processo de Fabric.exe, que irá reiniciar todos os os sistema serviço e o utilizador serviço réplicas alojadas nesse nó. Utilizar esta API para testar o seu serviço de ajuda a descobrir erros ao longo de caminhos de recuperação de ativação pós-falha. Ajuda a simular falhas de nó do cluster.

A seguinte captura de ecrã mostra o **reinício ServiceFabricNode** comando de teste em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

O resultado do primeiro **Get-ServiceFabricNode** (um cmdlet do módulo do PowerShell de Service Fabric) mostra que o cluster local com cinco nós: Node.1 para Node.5. Depois da ação de teste (cmdlet) **reinício ServiceFabricNode** é executado no nó, com o nome Node.4, Vemos que a disponibilidade do nó foi reposta.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação contra um cluster do Azure
Execução de uma ação de teste (utilizando o PowerShell) contra um cluster do Azure é semelhante ao executar a ação contra um cluster local. A única diferença é que antes de poder executar a ação, em vez de ligar ao local cluster, tem de primeiro estabeleça a ligação ao cluster do Azure.

## <a name="running-a-testability-action-using-c35"></a>Execução de uma ação de teste com C &#35;
Para executar uma ação de teste ao utilizar c#, primeiro tem de ligar ao cluster utilizando FabricClient. Em seguida, obter os parâmetros necessários para executar a ação. Parâmetros diferentes podem ser utilizados para executar a mesma ação.
Observar a ação de RestartServiceFabricNode, é uma forma de executá-la utilizando as informações do nó (nome de nó e o ID de instância do nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação de parâmetro:

* **CompleteMode** Especifica que o modo de não deve verificar se a ação de reinício, na verdade, foi concluída com êxito. Especificar o modo de conclusão como "Verificar" fará com que verificar se a ação de reinício, na verdade, foi concluída com êxito.  
* **OperationTimeout** define a quantidade de tempo para a operação seja concluída antes de é emitida uma exceção de TimeoutException.
* **CancellationToken** permite uma chamada pendente terá de ser cancelada.

Em vez de especificar diretamente no nó pelo respetivo nome, pode especificá-la através de uma chave de partição e o tipo de réplica.

Para obter mais informações, consulte [PartitionSelector e ReplicaSelector](#partition_replica_selector).

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector e ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector é um programa auxiliar exposta no teste e é utilizado para selecionar uma partição específica para efetuar qualquer uma das ações de teste. Pode ser utilizado para selecionar uma partição específica, se o ID de partição é conhecido previamente. Em alternativa, pode fornecer a chave de partição e a operação irá resolver o ID de partição internamente. Também tem a opção de seleção de uma partição aleatória.

Para utilizar esta ajuda, criar o objeto de PartitionSelector e selecione a partição utilizando um dos métodos selecione *. Em seguida, passa o objeto de PartitionSelector para a API que obriga. Se nenhuma opção for selecionada, assume a uma partição aleatória.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector é um programa auxiliar exposta no teste e é utilizado para ajudar a selecionar uma réplica para efetuar qualquer uma das ações de teste. Pode ser utilizado para selecionar uma réplica específica, se o ID de réplica é conhecido previamente. Além disso, tem a opção de selecionar uma réplica primária ou secundária aleatória. ReplicaSelector deriva de PartitionSelector, por isso terá de selecionar a réplica e a partição no qual pretende executar a operação de teste.

Para utilizar esta ajuda, criar um objeto de ReplicaSelector e definir a forma como pretende selecionar a réplica e a partição. Em seguida, pode passar para a API que obriga. Se nenhuma opção for selecionada, assume como uma réplica aleatória e partição aleatória.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Passos seguintes
* [Cenários de teste](service-fabric-testability-scenarios.md)
* Como testar o seu serviço
  * [Simular falhas durante a cargas de trabalho do serviço](service-fabric-testability-workload-tests.md)
  * [Falhas de comunicação de serviço de serviço](service-fabric-testability-scenarios-service-communication.md)

