---
title: "Criar tarefas para preparar as tarefas e tarefas de conclua em nós de computação - Azure Batch | Microsoft Docs"
description: "Utilize tarefas de preparação de ao nível da tarefa para minimizar a transferência de dados em nós de computação do Azure Batch e as tarefas de limpeza de nó após conclusão da tarefa de versão."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a2525c02ce7bd3969469d2e28a5fccc948f89b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Executar tarefa de preparação e tarefas de lançamento do batch nós de computação

 Uma tarefa de lote do Azure requer, muitas vezes, algum tipo de configuração antes das respetivas tarefas são executadas e manutenção de tarefa pós-cópia quando as tarefas estão concluídas. Poderá ter de transferir os dados de entrada de tarefas comuns para os nós de computação ou carregar dados de saída de tarefas para o Storage do Azure após a conclusão da tarefa. Pode utilizar **preparação da tarefa** e **lançamento da tarefa** tarefas a efetuar estas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são a tarefa de preparação e versão tarefas?
Antes de executam tarefas de um trabalho, a tarefa de preparação da tarefa é executada em todos os nós de computação agendados para ser executada pelo menos uma tarefa. Assim que a tarefa é concluída, a tarefa de lançamento da tarefa é executada em cada nó no conjunto que executou, pelo menos, uma tarefa. Tal como acontece com as tarefas de lote normal, pode especificar uma linha de comandos para ser invocado quando uma tarefa de preparação ou de lançamento da tarefa é executada.

Tarefas de preparação e de lançamento da tarefa oferecem funcionalidades de tarefa do Batch familiares como a transferência de ficheiro ([ficheiros de recursos][net_job_prep_resourcefiles]), elevados execução, variáveis de ambiente personalizadas, duração de execução máximo, contagem de repetições e tempo de retenção de ficheiros.

As secções seguintes, irá aprender como utilizar o [JobPreparationTask] [ net_job_prep] e [JobReleaseTask] [ net_job_release] localizadas classes no [.NET do Batch] [ api_net] biblioteca.

> [!TIP]
> Tarefas de preparação e de lançamento da tarefa são especialmente útil em ambientes do "agrupamento partilhado", em que um conjunto de nós de computação persistir entre execuções de tarefa e é utilizado por muitas tarefas.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando utilizar a tarefa de preparação e tarefas da versão
Preparação de trabalho e tarefas de lançamento são uma boa opção nas seguintes situações:

**Transferência de dados de tarefas comuns**

Tarefas do batch, muitas vezes, necessitam de um conjunto de dados comum como entrada para as tarefas do trabalho. Por exemplo, diária cálculos de análise de risco, dados de mercado são tarefa específico, ainda comuns a todas as tarefas na tarefa. Apenas uma vez estes dados de mercado, muitas vezes, vários gigabytes de tamanho, devem ser transferidos para cada nó de computação para que qualquer tarefa que é executado no nó pode utilizá-lo. Utilize um **tarefa de preparação** para transferir estes dados para cada nó antes da execução da tarefa 's outras tarefas.

**Eliminar tarefas e saída**

Num ambiente "agrupamento partilhado", onde não são desativados entre trabalhos de nós de computação de um conjunto, poderá ter de eliminar dados de tarefa entre é executado. Poderá ter de poupar espaço em disco em nós ou satisfazer as políticas de segurança da sua organização. Utilize um **tarefa de libertação** para eliminar dados que foi transferidos por uma tarefa de preparação ou gerados durante a execução da tarefa.

**Retenção do registo**

Pode querer manter uma cópia de ficheiros de registo que as suas tarefas geram ou, talvez falhas ficheiros de informação que podem ser gerados pelas aplicações com falhas. Utilize um **tarefa de libertação** nestes casos para comprimir e carregar estes dados para um [Storage do Azure] [ azure_storage] conta.

> [!TIP]
> Outra forma para manter os registos e outras tarefas e saída de dados estão a utilizar o [convenções de ficheiro do Azure Batch](batch-task-output.md) biblioteca.
> 
> 

## <a name="job-preparation-task"></a>Tarefa de preparação
Antes da execução das tarefas de uma tarefa, o Batch executa a tarefa de preparação em cada nó de computação que esteja agendada para ser executada uma tarefa. Por predefinição, o serviço Batch aguarda que a tarefa de preparação da tarefa seja concluída antes de executar as tarefas agendadas para serem executadas no nó. No entanto, pode configurar o serviço não deve aguardar. Se o nó for reiniciado, a tarefa de preparação da tarefa é executada novamente, mas também pode desativar este comportamento.

A tarefa de preparação é executada apenas em nós que estejam agendados para executar uma tarefa. Isto impede a execução de uma tarefa de preparação desnecessária no caso de um nó não está atribuído uma tarefa. Isto pode ocorrer quando o número de tarefas para uma tarefa é inferior ao número de nós num conjunto. Também se aplica quando [a execução da tarefa em simultâneo](batch-parallel-node-tasks.md) é ativado, que mantém algumas if Inativas de nós a contagem de tarefas é inferior ao totais tarefas simultâneas possíveis. Por não executar a tarefa de preparação da tarefa em nós inativos, pode demora menos dinheiro em custos de transferência de dados.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] difere [CloudPool.StartTask] [ pool_starttask] nessa JobPreparationTask executa no início de cada tarefa, enquanto que o StartTask executa apenas quando um nó de computação primeiro associa um conjunto ou de reiniciar.
> 
> 

## <a name="job-release-task"></a>Tarefa de lançamento da tarefa
Depois de uma tarefa é marcada como concluído, a tarefa de lançamento da tarefa é executada em cada nó no conjunto que executou, pelo menos, uma tarefa. Marcar uma tarefa como concluído ao emitir um pedido de terminar. O serviço Batch, em seguida, define o estado da tarefa para *terminar*, termina quaisquer tarefas de Active Directory ou em execução associadas a tarefa e execute a tarefa de libertação. A tarefa, em seguida, avança para o *concluída* estado.

> [!NOTE]
> Eliminação da tarefa também executa a tarefa de libertação. No entanto, se uma tarefa já foi terminada, o lançamento da tarefa não é executado uma segunda vez se a tarefa mais tarde é eliminada.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tarefa de preparação e versão tarefas com .NET do Batch
Para utilizar uma tarefa de preparação, atribua um [JobPreparationTask] [ net_job_prep] objeto da tarefa [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] propriedade. Da mesma forma, inicializar um [JobReleaseTask] [ net_job_release] e atribua-a sua tarefa [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] propriedade definida de lançamento da tarefa a tarefa.

Este fragmento de código, `myBatchClient` é uma instância de [BatchClient][net_batch_client], e `myPool` é um conjunto existente na conta do Batch.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Conforme mencionado anteriormente, o lançamento da tarefa é executado quando uma tarefa é terminada ou eliminada. Terminar uma tarefa com [joboperations. Terminatejobasync][net_job_terminate]. Eliminar uma tarefa com [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente, terminar ou eliminar uma tarefa quando as tarefas estão concluídas, ou quando foi atingido o limite de tempo que definiu.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub
Para ver a tarefa de preparação e libertar a tarefas em ação, consulte o [JobPrepRelease] [ job_prep_release_sample] projeto de exemplo no GitHub. Esta aplicação de consola faz o seguinte:

1. Cria um conjunto com dois nós "pequenos".
2. Cria uma tarefa com a tarefa de preparação, versão e tarefas padrão.
3. Executa a tarefa de preparação, que escreve primeiro o ID de nó para um ficheiro de texto no diretório de "partilhado" de um nó.
4. Executa uma tarefa em cada nó que escreve o respetivo ID de tarefa no mesmo ficheiro de texto.
5. Depois de todas as tarefas são concluídas (ou o tempo limite é alcançado), imprime os conteúdos do ficheiro de texto de cada nó na consola.
6. Quando a tarefa estiver concluída, executa o tarefa de lançamento da tarefa para eliminar o ficheiro a partir do nó.
7. Imprime os códigos de saída das tarefas de preparação e de lançamento de trabalho para cada nó no qual são executados.
8. Execução de pausa para permitir a confirmação de eliminação de tarefa e/ou de agrupamento.

Resultado da aplicação de exemplo é semelhante ao seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Devido ao tempo de criação e começar a variável de nós de um novo conjunto (alguns nós, estará pronto para tarefas antes de outras), poderá ver um resultado diferente. Especificamente, porque as tarefas concluírem rapidamente, um de nós do conjunto pode executar a todas as tarefas do trabalho. Se isto ocorrer, irá reparar que preparação da tarefa e não existem tarefas de lançamento para o nó que não existem tarefas executadas.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecione a preparação de trabalho e tarefas de lançamento no portal do Azure
Quando executar a aplicação de exemplo, pode utilizar o [portal do Azure] [ portal] para ver as propriedades do trabalho e as respetivas tarefas ou mesmo transferir o ficheiro de texto partilhado que é modificado pelas tarefas do trabalho.

A captura de ecrã abaixo mostra o **painel de tarefas de preparação** no portal do Azure após a execução da aplicação de exemplo. Navegue para o *JobPrepReleaseSampleJob* propriedades depois de concluíram as suas tarefas (mas antes de eliminar o seu trabalho e o conjunto) e clique em **tarefas de preparação** ou **versão tarefas** para ver as respetivas propriedades.

![Propriedades de preparação da tarefa no portal do Azure][1]

## <a name="next-steps"></a>Passos seguintes
### <a name="application-packages"></a>Pacotes de aplicações
Para além da tarefa de preparação, também pode utilizar o [pacotes de aplicações](batch-application-packages.md) funcionalidade do Batch para preparar nós de computação para a execução da tarefa. Esta funcionalidade é especialmente útil para a implementação de aplicações que não necessitam de executar um programa de instalação, as aplicações que contêm muitos ficheiros de (100 +) ou aplicações que requerem controlo de versão rigorosa.

### <a name="installing-applications-and-staging-data"></a>Instalar aplicações e dados de teste
Esta mensagem num fórum MSDN fornece uma descrição geral dos vários métodos de preparar os nós para as tarefas em execução:

[Instalar aplicações e dados em lote de teste de nós de computação][forum_post]

Escrito por um dos membros da equipa do Azure Batch, descreve várias técnicas que pode utilizar para implementar aplicações e dados em nós de computação.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
