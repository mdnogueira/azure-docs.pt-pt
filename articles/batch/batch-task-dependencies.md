---
title: "Utilize as dependências de tarefas para executar tarefas com base na conclusão de outras tarefas - Azure Batch | Microsoft Docs"
description: "Criar tarefas que dependem da conclusão de outras tarefas de processamento de estilo de MapReduce e macrodados semelhantes cargas de trabalho no Azure Batch."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 465306d2de8d1dbe6ba1f0cd74be720b78a50de3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Criar as dependências de tarefas para executar as tarefas que dependem de outras tarefas

Pode definir as dependências de tarefas para executar uma tarefa ou o conjunto de tarefas apenas depois de uma tarefa principal foi concluída. Alguns cenários em que as dependências de tarefas são úteis incluem:

* Estilo de MapReduce as cargas de trabalho na nuvem.
* Tarefas cujas tarefas de processamento de dados podem ser expresso como um direcionados acíclicos (DAG).
* Processos de composição pré e pós-implementação composição, onde cada tarefa tem de concluir antes de pode começar a próxima tarefa.
* Qualquer outra tarefa no qual a jusante tarefas dependem da saída das tarefas a montante.

Com as dependências de tarefas do Batch, pode criar tarefas agendadas para execução em nós de computação após a conclusão de uma ou mais tarefas do principal. Por exemplo, pode criar uma tarefa que compõe a cada intervalo de um filme 3D com tarefas paralelas, separadas. O final de tarefas - a "task intercalação" – intercala as frames Jumbo compostas para o filmes concluída apenas depois de todos os fotogramas foi compostas com êxito.

Por predefinição, as tarefas dependentes estão agendadas para execução apenas depois da tarefa principal foi concluída com êxito. Pode especificar uma ação de dependência para substituir o comportamento predefinido e executar tarefas se a tarefa principal falhar. Consulte o [ações de dependência](#dependency-actions) secção para obter detalhes.  

Pode criar as tarefas que dependem de outras tarefas numa relação um para um ou um-para-muitos. Também pode criar uma dependência do intervalo em que uma tarefa depende da conclusão de um grupo de tarefas dentro de um intervalo de IDs de tarefas especificada. Pode combinar estas três cenários básicos para criar relações muitos-para-muitos.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com .NET do Batch
Neste artigo, vamos discutir como configurar as dependências de tarefas utilizando o [.NET do Batch] [ net_msdn] biblioteca. Vamos primeiro mostrar-lhe como para [ativar a dependência de tarefa](#enable-task-dependencies) na suas tarefas e, em seguida, demonstram como [configurar uma tarefa com dependências](#create-dependent-tasks). Podemos também descrevem como especificar uma ação de dependência para executar tarefas dependentes se principal falhar. Por fim, vamos discutir o [cenários de dependência](#dependency-scenarios) que suporta o Batch.

## <a name="enable-task-dependencies"></a>Ativar as dependências de tarefas
Para utilizar as dependências de tarefas na sua aplicação do Batch, primeiro tem de configurar a tarefa para utilizar as dependências de tarefas. No .NET do Batch, ativá-la no seu [CloudJob] [ net_cloudjob] definindo o [UsesTaskDependencies] [ net_usestaskdependencies] propriedade `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No fragmento de código anterior, "batchClient" é uma instância do [BatchClient] [ net_batchclient] classe.

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes
Para criar uma tarefa que depende da conclusão de uma ou mais tarefas de principal, pode especificar que a tarefa "depende" outras tarefas. No .NET do Batch, configure o [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] propriedade com uma instância do [TaskDependencies] [ net_taskdependencies] classe:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este fragmento de código cria uma tarefa dependente com o ID de tarefa "Flowers". A tarefa "Flowers" depende de tarefas "Rain" e "Sun". Tarefa "Flowers" irão ser agendadas para ser executada num nó de computação apenas depois de tarefas "Rain" e "Sun" concluiu com êxito.

> [!NOTE]
> É considerada uma tarefa seja concluída com êxito quando esta estiver dentro do **concluída** estado e a respetiva **código de saída** é `0`. No .NET do Batch, isto significa uma [CloudTask][net_cloudtask].[ Estado] [ net_taskstate] valor da propriedade de `Completed` e o CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] valor da propriedade é `0`.
> 
> 

## <a name="dependency-scenarios"></a>Cenários de dependência
Existem três cenários de dependência na tarefa básico que pode utilizar no Azure Batch: um para um, um-para-muitos e o ID de tarefa intervalo dependência. Estes podem ser combinados para fornecer um cenário quarto, muitos-para-muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo |  |
|:---:| --- | --- |
|  [Um para um](#one-to-one) |*Tarefab* depende *Tarefaa* <p/> *Tarefab* não será possível agendar para execução até *Tarefaa* foi concluída com êxito |![Diagrama: dependência de uma tarefa][1] |
|  [Um-para-muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> *Tarefac* não será possível agendar para execução até que ambas *Tarefaa* e *Tarefab* foram concluídos com êxito |![Diagrama: dependências de tarefas de um-para-muitos][2] |
|  [Intervalo de ID de tarefa](#task-id-range) |*taskD* depende de um intervalo de tarefas <p/> *taskD* não será possível agendar para execução até as tarefas com IDs *1* através de *10* foram concluídos com êxito |![Diagrama: Dependência de intervalo de id de tarefa][3] |

> [!TIP]
> Pode criar **muitos-para-muitos** relações, tais como onde tarefas C, D, I e F cada dependem tarefas A e B. Isto é útil, por exemplo, no paralelizada pré-processamentos cenários em que as suas tarefas a jusante dependem da saída de várias tarefas a montante.
> 
> Nos exemplos nesta secção, é executada uma tarefa dependente apenas depois das tarefas de principal concluírem com êxito. Este comportamento é o comportamento predefinido para uma tarefa dependente. Pode executar uma tarefa dependente após a falha de uma tarefa principal, especificando uma ação de dependência para substituir o comportamento predefinido. Consulte o [ações de dependência](#dependency-actions) secção para obter detalhes.

### <a name="one-to-one"></a>Um para um
Uma relação unívoca, uma tarefa depende a conclusão com êxito da tarefa de um elemento principal. Para criar a dependência, forneça um ID de tarefa única para o [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] método estático quando a preencher a [DependsOn] [ net_dependson] propriedade [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um-para-muitos
Numa relação um-para-muitos, uma tarefa depende da conclusão de várias tarefas de principal. Para criar a dependência, fornecem uma coleção de IDs de tarefas para o [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] método estático quando a preencher a [DependsOn] [ net_dependson] propriedade [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Intervalo de ID de tarefa
Uma dependência de uma variedade de tarefas de principal, uma tarefa depende a após a conclusão das tarefas cujos IDs se situam dentro de um intervalo.
Para criar a dependência, forneça o primeiro e IDs de tarefas pela última vez no intervalo para o [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] método estático quando a preencher a [DependsOn] [ net_dependson] propriedade [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando utiliza os intervalos de ID de tarefa para as suas dependências, os IDs de tarefas no intervalo *tem* ser representações de cadeia dos valores de número inteiro.
> 
> Cada tarefa no intervalo deve satisfazer a dependência, concluindo com êxito ou concluindo com uma falha que está mapeada para uma ação de dependência definida como **Satisfy**. Consulte o [ações de dependência](#dependency-actions) secção para obter detalhes.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Ações de dependência

Por predefinição, uma tarefa dependente ou conjunto de tarefas é executada apenas depois de uma tarefa principal foi concluída com êxito. Em alguns cenários, poderá executar tarefas dependentes, mesmo se a tarefa principal falhar. Pode substituir o comportamento predefinido, especificando uma ação de dependência. Uma ação de dependência Especifica se uma tarefa dependente é elegível para executar, com base no êxito ou falha da tarefa principal. 

Por exemplo, suponha que uma tarefa dependente está a aguardar dados após a conclusão da tarefa a montante. Se a tarefa a montante falhar, a tarefa dependente ainda poderá executar utilizando dados mais antigos. Neste caso, pode especificar uma ação de dependência que a tarefa dependente é elegível para ser executada, apesar da falha da tarefa principal.

Uma ação de dependência baseiam-se uma condição de saída para a tarefa principal. Pode especificar uma ação de dependência para qualquer uma das seguintes condições de saída; para .NET, consulte o [ExitConditions] [ net_exitconditions] classe para obter mais detalhes:

- Quando ocorre um erro de processamento previamente.
- Quando ocorre um erro de carregamento de ficheiros. Se a tarefa sai com um código de saída que foi especificado através de **exitCodes** ou **exitCodeRanges**, e, em seguida, prevalece encontros erro, a ação especificada, o código de saída de carregar um ficheiro.
- Quando a tarefa sai com um código de saída definido pelo **ExitCodes** propriedade.
- Quando a tarefa sai com um código de saída que se encontrem dentro de um intervalo especificado pelo **ExitCodeRanges** propriedade.
- Caso o predefinido, se a tarefa sai com um código de saída não definido pelo **ExitCodes** ou **ExitCodeRanges**, ou se a tarefa sai com um erro de pré-processamento e o **PreProcessingError** propriedade não for definida, ou se a tarefa falhar com um ficheiro carregar erro e o **FileUploadError** propriedade não está definida. 

Para especificar uma ação de dependência no .NET, defina o [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] propriedade para a condição de saída. O **DependencyAction** propriedade assume um de dois valores:

- Definir o **DependencyAction** propriedade **Satisfy** indica que as tarefas dependentes são elegíveis para ser executada se a tarefa principal sai com um erro especificado.
- Definir o **DependencyAction** propriedade **bloco** indica que as tarefas dependentes não são elegíveis para ser executada.

A definição predefinida para o **DependencyAction** propriedade é **Satisfy** para o código de saída 0, e **bloco** para todas as outras condições de saída.

O seguinte código conjuntos de fragmento a **DependencyAction** propriedade para uma tarefa principal. Se a tarefa principal sai com um erro de pré-processamento ou com os códigos de erro especificado, a tarefa dependente é bloqueada. Se a tarefa principal sai com qualquer outro erro diferente de zero, a tarefa dependente é elegível para ser executada.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Exemplo de código
O [TaskDependencies] [ github_taskdependencies] projeto de exemplo é uma do [exemplos de código do Azure Batch] [ github_samples] no GitHub. Esta solução do Visual Studio demonstra:

- Como ativar a dependência de tarefas numa tarefa
- Como criar tarefas que dependem de outras tarefas
- Como realizar essas tarefas num conjunto de nós de computação.

## <a name="next-steps"></a>Passos seguintes
### <a name="application-deployment"></a>Implementação de aplicação
O [pacotes de aplicações](batch-application-packages.md) funcionalidade do Batch fornece uma forma fácil para ambos implementar e a versão, as aplicações que as tarefas são executadas em nós de computação.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste
Consulte [instalar aplicações e dados em lote de teste de nós de computação] [ forum_post] no fórum do Azure Batch para uma descrição geral dos métodos para preparar os nós para executar tarefas. Escrito por um dos membros da equipa do Azure Batch, esta mensagem é uma boa manual sobre diferentes formas para copiar as aplicações, dados de entrada de tarefas e outros ficheiros para os nós de computação.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de um para um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um-para-muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de id de tarefa"
