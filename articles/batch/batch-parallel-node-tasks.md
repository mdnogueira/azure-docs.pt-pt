---
title: "Executar tarefas em paralelo para utilizar recursos de computação de forma eficiente - Azure Batch | Microsoft Docs"
description: "Aumentar a eficiência e reduzir os custos, utilizando menos nós de computação e tarefas em execução em simultâneo em cada nó num conjunto do Azure Batch"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eae6359b5fb36bd0317391ce2330afb7dd7bfe3b
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Nós de computação de execução de tarefas em simultâneo para maximizar a utilização do Batch 

Ao executar a mais do que uma tarefa em simultâneo em cada nó de computação no seu conjunto do Azure Batch, pode maximizar a utilização de recursos num número menor de nós no conjunto. Para algumas cargas de trabalho, isto pode resultar em tempos de tarefa mais curtos e custo mais baixo.

Embora alguns cenários beneficiam dedicar todos os recursos de um nó para uma única tarefa, várias situações beneficiam permitindo várias tarefas partilhar esses recursos:

* **Transferência de dados de minimizar** quando as tarefas são capazes de partilhar os dados. Neste cenário, pode reduzir significativamente taxas aplicáveis às transferências de dados ao copiar dados partilhados para um número menor de nós e executar tarefas em paralelo em cada nó. Especialmente aplica-se os dados a ser copiado para cada nó têm de ser transferidos entre regiões geográficas.
* **Maximizar a forma de utilização de memória** quando tarefas requerem uma grande quantidade de memória, mas apenas durante curtos períodos de tempo e, em alturas variável durante a execução. Pode utilizar nós de computação menos mas maior, com mais memória de forma eficiente lidar com esses picos. Estes nós teria várias tarefas em execução em paralelo em cada nó, mas cada tarefa seria tirar partido de memória plentiful de nós em alturas diferentes.
* **Mitigar os limites de número de nó** quando a comunicação entre nós é necessária dentro de um conjunto. Atualmente, os agrupamentos configurados para comunicação entre nós estão limitados a 50 nós de computação. Se cada nó nesse conjunto é capaz de executar tarefas em paralelo, um maior número de tarefas pode ser executado em simultâneo.
* **Um cluster de computação no local a replicar**, por exemplo, ao mover primeiro um ambiente de computação para o Azure. Se a sua solução no local atual executa várias tarefas por nó de computação, pode aumentar o número máximo de tarefas de nó mais detalhadamente espelhar que a configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Por exemplo para ilustrar os benefícios da execução de tarefas paralelas, vamos supor que o se a aplicação de tarefa tem requisitos de CPU e memória que [padrão\_D1](../cloud-services/cloud-services-sizes-specs.md) nós são suficientes. No entanto, para concluir a tarefa no tempo necessário, 1000 estes nós são necessários.

Em vez de utilizar o padrão\_D1 nós que tenham 1 núcleo de CPU, pode utilizar [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md) nós que tem 16 núcleos e ativar a execução da tarefa paralela. Por conseguinte, *16 vezes menos nós* poderia ser utilizado – em vez de 1.000 nós, só 63 seriam necessários. Além disso, se os ficheiros de aplicações grandes ou dados de referência são necessários para cada nó, duração de tarefa e a eficiência são novamente melhorados, uma vez que os dados são copiados para apenas 63 nós.

## <a name="enable-parallel-task-execution"></a>Ativar a execução de tarefas paralelas
Configurar nós de computação para a execução da tarefa paralela ao nível do conjunto. Com a biblioteca .NET do Batch, defina o [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade quando cria um conjunto. Se estiver a utilizar a API de REST do Batch, defina o [maxTasksPerNode] [ rest_addpool] elemento no corpo do pedido durante a criação de conjunto.

O Azure Batch permite-lhe definir máximas tarefas por nó até quatro vezes (4 x) o número de núcleos de nó. Por exemplo, se o agrupamento está configurado connosco de tamanho "Grande" (quatro núcleos), em seguida, `maxTasksPerNode` pode ser definido para 16. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, consulte [tamanhos para serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md). Para obter mais informações sobre limites de serviço, consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md).

> [!TIP]
> Certifique-se de ter em conta o `maxTasksPerNode` valor quando constrói um [fórmula de dimensionamento automático] [ enable_autoscaling] para o seu conjunto. Por exemplo, uma fórmula que passa `$RunningTasks` poderão ser significativamente afetadas por um aumento de tarefas por nó. Consulte [automaticamente Dimensionar nós de computação num conjunto do Azure Batch](batch-automatic-scaling.md) para obter mais informações.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de computação num conjunto podem executar tarefas em simultâneo, é importante especificar como pretende que as tarefas para ser distribuída em todos os nós no conjunto.

Utilizando o [CloudPool.TaskSchedulingPolicy] [ task_schedule] propriedade, pode especificar que devem ser atribuídas tarefas uniformemente em todos os nós no conjunto ("propagando-se"). Em alternativa, pode especificar que as tarefas tantas quanto possível devem ser atribuídas a cada nó antes das tarefas estão atribuídas a outro nó no conjunto ("mais").

Como um exemplo de como esta funcionalidade é útil, considere o conjunto de [padrão\_D14](../cloud-services/cloud-services-sizes-specs.md) nós (no exemplo acima) que está configurado com um [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valor de 16. Se o [CloudPool.TaskSchedulingPolicy] [ task_schedule] está configurado com um [ComputeNodeFillType] [ fill_type] de *pacote*, seria maximizar a utilização de todas as 16 núcleos de cada nó e permitir que um [conjunto de dimensionamento automático](batch-automatic-scaling.md) para eliminar os nós do conjunto (nós sem quaisquer tarefas atribuídas). Isto minimiza a utilização de recursos e poupa dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET do batch
Isto [.NET do Batch] [ api_net] fragmento de código da API mostra um pedido para criar um conjunto que contém quatro nós de grandes dimensões com um máximo de quatro tarefas por nó. Especifica uma tarefa de agendamento de política que preencha cada nó com tarefas antes de atribuir tarefas a outro nó no conjunto. Para obter mais informações sobre como adicionar conjuntos utilizando a API .NET do Batch, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemplo REST do batch
Isto [REST do Batch] [ api_rest] fragmento de API mostra um pedido para criar um conjunto que contém dois nós de grandes dimensões com um máximo de quatro tarefas por nó. Para obter mais informações sobre como adicionar conjuntos utilizando a API REST, consulte [adicionar um conjunto para uma conta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Pode definir o `maxTasksPerNode` elemento e [MaxTasksPerComputeNode] [ maxtasks_net] propriedade apenas no momento de criação de conjunto. Não pode ser modificados depois de já tiver sido criado um conjunto.
>
>

## <a name="code-sample"></a>Exemplo de código
O [ParallelNodeTasks] [ parallel_tasks_sample] projeto no GitHub ilustra a utilização do [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriedade.

Esta aplicação de consola c# utiliza o [.NET do Batch] [ api_net] biblioteca para criar um conjunto com um ou mais nós de computação. Configurável diversas tarefas ser executada em nós para simular carga variável. Resultados da aplicação especifica quais os nós executar cada tarefa. A aplicação também fornece um resumo dos parâmetros da tarefa e duração. A parte de resumo de saída de dois executa diferente da aplicação exemplo é apresentado abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução da aplicação de exemplo mostra que com um único nó no conjunto e a predefinição de uma tarefa por nó, a duração de tarefa é mais de 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda executada do exemplo mostra uma redução significativa no duração de tarefa. Isto acontece porque o conjunto foi configurado com quatro tarefas por nó, o que permite a execução da tarefa paralela concluir a tarefa no quase um trimestre do tempo.

> [!NOTE]
> A duração de tarefa no resumos acima não incluem a hora de criação do conjunto. Cada uma das tarefas acima foi submetida para conjuntos criados anteriormente foram cujos nós de computação no *inativo* Estado em tempo de submissão.
>
>

## <a name="next-steps"></a>Passos seguintes
### <a name="batchlabs-heat-map"></a>Mapa térmico BatchLabs
O [BatchLabs][batch_labs] é uma ferramenta de cliente autónoma, gratuita e rica em funcionalidades para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. BatchLabs contém um *mapa térmico* funcionalidade que fornece a visualização de execução da tarefa. Quando estiver a executar o [ParallelTasks] [ parallel_tasks_sample] aplicação de exemplo, pode utilizar a funcionalidade de mapa térmico para visualizar facilmente a execução de tarefas paralelas em cada nó.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

