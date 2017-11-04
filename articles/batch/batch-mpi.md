---
title: "Utilize tarefas de várias instâncias para executar aplicações MPI - Azure Batch | Microsoft Docs"
description: "Saiba como executar aplicações de Interface de passagem de mensagens (MPI) utilizando o tipo de tarefa de várias instâncias no Azure Batch."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: 5/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01da017587aed7c0f2415786fdcbf6f64024cbe3
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Utilize tarefas de várias instâncias para executar aplicações de Interface de passagem de mensagens (MPI) no Batch

Tarefas de várias instâncias permitem-lhe executar uma tarefa do Batch em vários nós de computação em simultâneo. Estas tarefas ativar cenários, como aplicações de Interface de passagem de mensagens (MPI) no Batch de computação de elevado desempenho. Neste artigo, irá aprender a executar tarefas de várias instâncias utilizando o [.NET do Batch] [ api_net] biblioteca.

> [!NOTE]
> Apesar dos exemplos neste artigo focar-se no .NET do Batch, MS-MPI e nós de computação do Windows, os conceitos de tarefas de várias instâncias abordados aqui são aplicáveis outras plataformas e tecnologias (Python e MPI Intel em nós do Linux, por exemplo).
>
>

## <a name="multi-instance-task-overview"></a>Descrição geral de tarefas de várias instâncias
No Batch, cada tarefa está normalmente executado num único nó de computação – submeter várias tarefas a um trabalho, bem como o serviço Batch agendas cada tarefa para execução num nó. No entanto, ao configurar uma tarefa **definições de várias instâncias**, informe o lote em vez disso, criar uma tarefa principal e vários subtarefas que, em seguida, são executadas em vários nós.

![Descrição geral de tarefas de várias instâncias][1]

Quando submete uma tarefa com definições de várias instâncias para uma tarefa, o Batch executa vários passos exclusivos para tarefas de várias instâncias:

1. O serviço Batch cria um **primário** e vários **subtarefas** com base nas definições de várias instâncias. O número total de tarefas (primários juntamente com todos os subtarefas) corresponde ao número de **instâncias** (nós de computação) que especificar nas definições de várias instâncias.
2. Batch designa um de nós de computação, como o **principal**e a agenda da tarefa para execução no mestre de primária. Esta agenda subtarefas para executar o resto de nós de computação alocados para a tarefa de várias instâncias, uma subtarefa por nó.
3. O principal e todas as subtarefas transferir nenhum **ficheiros de recursos comuns** que especificar nas definições de várias instâncias.
4. Depois do recurso comum os ficheiros foram transferidos, o site primário e executar subtarefas o **comando coordenação** que especificar nas definições de várias instâncias. O comando de coordenação é normalmente utilizado para preparar nós para executar a tarefa. Isto pode incluir a partir de serviços em segundo plano (tais como [Microsoft MPI][msmpi_msdn]do `smpd.exe`) e a verificar se os nós estão prontos para processar mensagens do nó entre.
5. A tarefa primária executa o **comando aplicação** no nó principal *depois* o comando de coordenação foi concluído com êxito pelo principal e todas as subtarefas. O comando de aplicação da linha de comandos da tarefa de várias instâncias próprio e é executado apenas pela tarefa principal. Num [MS-MPI][msmpi_msdn]-solução, baseada na trata onde executar a sua aplicação com capacidade de MPI utilizando `mpiexec.exe`.

> [!NOTE]
> Embora seja funcionalmente distinto, "tarefa de várias instâncias" não é um tipo de tarefa exclusivos, como o [StartTask] [ net_starttask] ou [JobPreparationTask] [ net_jobprep]. A tarefa de várias instâncias é simplesmente uma tarefa de lote standard ([CloudTask] [ net_task] no .NET do Batch) foram configuradas cujas definições de várias instâncias. Neste artigo, vamos referir-se a isto como o **tarefa de várias instâncias**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para tarefas de várias instâncias
Tarefas de várias instâncias necessitam de um agrupamento com **comunicação entre nós ativada**e com **desativada a execução da tarefa em simultâneo**. Para desativar a execução da tarefa em simultâneo, defina o [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) propriedade para 1.

Este fragmento de código mostra como criar um conjunto para tarefas de várias instâncias utilizando a biblioteca .NET do Batch.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Se tentar executar uma tarefa de várias instâncias num conjunto, a comunicação internós desativado ou com um *maxTasksPerNode* valor superior a 1, a tarefa é agendada nunca – indefinidamente permanece no estado "Active Directory". 
>
> Tarefas de várias instâncias podem executar apenas em nós nos conjuntos criados após 14 de Dezembro de 2015.
>
>

### <a name="use-a-starttask-to-install-mpi"></a>Utilizar um StartTask para instalar MPI
Para executar aplicações MPI com uma tarefa de várias instâncias, terá primeiro de instalar uma implementação de MPI (MS-MPI ou MPI Intel, por exemplo) em nós de computação no conjunto. Esta é uma boa altura para utilizar um [StartTask][net_starttask], que executa sempre que um nó se associa um conjunto ou for reiniciado. Este fragmento de código cria um StartTask que especifica o pacote de configuração do MS-MPI como um [ficheiro de recursos][net_resourcefile]. Linha de comandos da tarefa de início é executada após o ficheiro de recursos é transferido para o nó. Neste caso, a linha de comandos executa uma instalação autónoma de MS MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Acesso remoto direto à memória (RDMA)
Quando escolhe uma [tamanho com capacidade RDMA](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) como A9 de nós de computação no seu conjunto do Batch, a aplicação de MPI pode tirar partido da rede de acesso (RDMA) de elevado desempenho, baixa latência memória direta remoto do Azure.

Procure os tamanhos especificados como "Com capacidade RDMA" nos seguintes artigos:

* **CloudServiceConfiguration** agrupamentos

  * [Os tamanhos de serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md) (apenas Windows)
* **VirtualMachineConfiguration** agrupamentos

  * [Os tamanhos de máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Os tamanhos de máquinas virtuais no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Para tirar partido de RDMA [nós de computação do Linux](batch-linux-nodes.md), tem de utilizar **Intel MPI** em nós. Para obter mais informações sobre agrupamentos CloudServiceConfiguration e VirtualMachineConfiguration, consulte a secção de conjunto do [descrição geral da funcionalidade do Batch](batch-api-basics.md).
>
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Criar uma tarefa de várias instâncias com .NET do Batch
Agora que iremos tiver abrangidos os requisitos de agrupamento e a instalação do pacote MPI, vamos criar a tarefa de várias instâncias. Este fragmento, criamos um padrão [CloudTask][net_task], em seguida, configure o [MultiInstanceSettings] [ net_multiinstance_prop] propriedade. Conforme mencionado anteriormente, a tarefa de várias instâncias não é um tipo de tarefa distinto, mas uma tarefa de lote padrão configurada com definições de várias instâncias.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Tarefa principal e subtarefas
Quando criar as definições de várias instâncias de uma tarefa, especifique o número de nós de computação que está a executar a tarefa. Ao submeter a tarefa a um trabalho, o serviço Batch cria um **primário** tarefas e suficiente **subtarefas** que em conjunto corresponde ao número de nós que especificou.

Estas tarefas são atribuídas um id de número inteiro no intervalo de 0 para *numberOfInstances* - 1. A tarefa com o id de 0 a tarefa principal e todos os outros ids subtarefas. Por exemplo, se criar as seguintes definições de várias instâncias de uma tarefa, a tarefa principal teria um id de 0 e as subtarefas teria ids de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nó principal
Quando submete uma tarefa de várias instâncias, o serviço Batch designa um de nós de computação que o nó "original" bem como agendas a tarefa principal a executar no nó principal. As subtarefas são agendadas para serem executadas no resto de nós alocado para a tarefa de várias instâncias.

## <a name="coordination-command"></a>Comando de coordenação de problemas
O **comando coordenação** executado por ambos os principais e subtasks.

A invocação do comando coordenação está a bloquear – lote não é executado o comando de aplicação, até que o comando de coordenação devolveu com êxito para todas as subtarefas. O comando de coordenação, por conseguinte, deve começar todos os serviços necessários em segundo plano, certifique-se de que estas estão prontas para utilização e, em seguida, a sair. Por exemplo, este comando de coordenação de uma solução utilizando a versão de MS MPI 7 inicia o serviço SMPD no nó, em seguida, sai:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Tenha em atenção a utilização de `start` neste comando de coordenação de problemas. Isto é necessário porque o `smpd.exe` aplicação devolve imediatamente após a execução. Sem a utilização de [iniciar] [ cmd_start] comando, este comando de coordenação não devolvam e bloqueariam, por conseguinte, o comando de aplicação de execução.

## <a name="application-command"></a>Comando de aplicação
Depois da tarefa principal e todas as subtarefas terminar de executar o comando de coordenação de problemas, a linha de comandos da tarefa de várias instâncias é executada pela tarefa principal *apenas*. Chamamos a isto o **comando aplicação** distinguir do comando de coordenação de problemas.

Para aplicações de MS MPI, utilize o comando de aplicação para executar a sua aplicação com capacidade para MPI com `mpiexec.exe`. Por exemplo, eis um comando de aplicação para uma solução de utilizar a versão 7 de MPI MS:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Porque MS-MPI `mpiexec.exe` utiliza o `CCP_NODES` variável por predefinição (consulte [variáveis de ambiente](#environment-variables)) da linha de comandos da aplicação de exemplo acima exclui-lo.
>
>

## <a name="environment-variables"></a>Variáveis de ambiente
Batch cria vários [variáveis de ambiente] [ msdn_env_var] específicas para tarefas de várias instâncias em nós de computação atribuídos a uma tarefa de várias instâncias. As linhas de comandos coordenação e a aplicação pode referenciar estas variáveis de ambiente, como podem os scripts e programas que são executados.

As seguintes variáveis de ambiente são criadas pelo serviço Batch para utilização por tarefas de várias instâncias:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obter detalhes completos sobre estas e outro Batch de computação variáveis de ambiente de nó, incluindo os conteúdos e visibilidade, consulte [variáveis de ambiente de nó de computação][msdn_env_var].

> [!TIP]
> O exemplo de código do Batch Linux MPI contém um exemplo de como várias destas variáveis de ambiente podem ser utilizadas. O [coordenação cmd] [ coord_cmd_example] scripts de Bash transfere comum e entrada ficheiros do armazenamento do Azure, permite a uma partilha de ficheiros NFS (Network System) no nó principal e configura os outros nós atribuído à tarefa de várias instâncias como os clientes NFS.
>
>

## <a name="resource-files"></a>Ficheiros de recursos
Existem dois conjuntos de ficheiros de recursos a considerar para tarefas de várias instâncias: **ficheiros de recursos comuns** que *todos os* tarefas transferem (ambos os principais e subtarefas) e o **deficheirosderecursos** especificado para várias instâncias de tarefas, que *apenas primário* transferências de tarefas.

Pode especificar um ou mais **ficheiros de recursos comuns** nas definições de várias instâncias de uma tarefa. Estes ficheiros de recursos comuns são transferidos do [Storage do Azure](../storage/common/storage-introduction.md) em cada nó **diretório partilhado tarefas** pelo principal e todas as subtarefas. Pode aceder diretório partilhado da tarefa a partir de aplicações e coordenação linhas de comandos utilizando a `AZ_BATCH_TASK_SHARED_DIR` variável de ambiente. O `AZ_BATCH_TASK_SHARED_DIR` caminho é idêntico em cada nó atribuído a tarefa de várias instâncias, assim, pode partilhar um comando único coordenação entre a primária e todas as subtarefas. Lote não "partilhar" o diretório numa noção do acesso remoto, mas pode utilizá-la como uma montagem ou partilhar ponto, tal como mencionado anteriormente na sugestão em variáveis de ambiente.

Ficheiros de recursos que especificar para a tarefa de várias instâncias propriamente dita são transferidos para o diretório de trabalho da tarefa, `AZ_BATCH_TASK_WORKING_DIR`, por predefinição. Conforme mencionado, contrariamente comuns dos ficheiros de recursos, apenas a tarefa principal transfere ficheiros de recurso especificados para a tarefa de várias instâncias propriamente dita.

> [!IMPORTANT]
> Utilize sempre as variáveis de ambiente `AZ_BATCH_TASK_SHARED_DIR` e `AZ_BATCH_TASK_WORKING_DIR` para fazer referência aos diretórios na sua linhas de comandos. Não tente construir os caminhos manualmente.
>
>

## <a name="task-lifetime"></a>Duração de tarefa
A duração da tarefa principal controla a duração da tarefa de várias instâncias completo. Quando sai primário, todas as subtarefas estão terminadas. O código de saída dos principais é o código de saída da tarefa e, por conseguinte, é utilizado para determinar o êxito ou falha da tarefa para fins de repetição.

Se algum das subtarefas falhar, a sair com um código de retorno de diferente de zero, por exemplo, a tarefa de várias instâncias todo falha. A tarefa de várias instâncias, em seguida, é terminada e repetida até o respetivo limite de repetição.

Quando elimina uma tarefa de várias instâncias, o principal e todas as subtarefas também são eliminadas pelo serviço Batch. Subtarefa todos os diretórios e os respetivos ficheiros são eliminados de nós de computação, tal como uma tarefa padrão.

[TaskConstraints] [ net_taskconstraints] para uma tarefa de várias instâncias, tais como o [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], e [RetentionTime] [ net_taskconstraint_retention] propriedades, são cumpridas conforme forem para uma tarefa padrão e aplicam-se a principal e todas as subtarefas. No entanto, se alterar o [RetentionTime] [ net_taskconstraint_retention] propriedade depois de adicionar a tarefa de várias instâncias para a tarefa, esta alteração é aplicada apenas para a tarefa principal. Todas as subtarefas continuam a utilizar original [RetentionTime][net_taskconstraint_retention].

Lista de tarefas recentes de um nó de computação reflete o id da subtarefa se a tarefa recente fazia parte de uma tarefa de várias instâncias.

## <a name="obtain-information-about-subtasks"></a>Obter informações sobre subtarefas
Para obter informações sobre subtarefas, utilizando a biblioteca .NET do Batch, chamar o [CloudTask.ListSubtasks] [ net_task_listsubtasks] método. Este método devolve informações sobre todas as subtarefas e informações sobre o nó de computação que executar as tarefas. Partir destas informações pode determinar o diretório de raiz de cada subtarefa, o id do conjunto, o estado atual, código de saída e muito mais. Pode utilizar estas informações em combinação com o [PoolOperations.GetNodeFile] [ poolops_getnodefile] método para obter ficheiros de subtarefa. Tenha em atenção que este método não devolver informações para a tarefa principal (id de 0).

> [!NOTE]
> Salvo indicação em contrário, métodos de .NET do Batch operar em várias instâncias [CloudTask] [ net_task] próprio aplicar *apenas* para a tarefa principal. Por exemplo, quando chamar o [CloudTask.ListNodeFiles] [ net_task_listnodefiles] apenas os ficheiros da tarefa principal do método de uma tarefa de várias instâncias, são devolvidos.
>
>

O fragmento de código seguinte mostra como obter informações de subtarefa, bem como conteúdos do ficheiro de pedido de nós em que executar.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Exemplo de código
O [MultiInstanceTasks] [ github_mpi] exemplo de código no GitHub demonstra como utilizar uma tarefa de várias instâncias para executar um [MS-MPI] [ msmpi_msdn] aplicação em Nós de computação do batch. Siga os passos no [preparação](#preparation) e [execução](#execution) para executar o exemplo.

### <a name="preparation"></a>Preparação
1. Siga os primeiros dois passos no [como compilar e executar um programa de MS MPI simple][msmpi_howto]. Isto satisfaça os pré-requisitos para o passo seguinte.
2. Criar um *versão* versão do [MPIHelloWorld] [ helloworld_proj] programa MPI de exemplo. Este é o programa será executado em nós de computação pela tarefa de várias instâncias.
3. Criar um ficheiro zip que contém `MPIHelloWorld.exe` (que é criado o passo 2) e `MSMpiSetup.exe` (que transferiu o passo 1). Deverá carregar este ficheiro zip como um pacote de aplicação no próximo passo.
4. Utilize o [portal do Azure] [ portal] para criar um lote [aplicação](batch-application-packages.md) chamado "MPIHelloWorld" e especifique o ficheiro zip que criou no passo anterior como versão "1.0" do pacote de aplicação. Consulte [carregar e gerir aplicações](batch-application-packages.md#upload-and-manage-applications) para obter mais informações.

> [!TIP]
> Criar um *versão* versão do `MPIHelloWorld.exe` para que não tem de incluir quaisquer dependências adicionais (por exemplo, `msvcp140d.dll` ou `vcruntime140d.dll`) no seu pacote de aplicação.
>
>

### <a name="execution"></a>Execução
1. Transferir o [azure-batch-samples] [ github_samples_zip] a partir do GitHub.
2. Abra o MultiInstanceTasks **solução** no Visual Studio 2015 ou mais recente. O `MultiInstanceTasks.sln` solução ficheiro está localizado em:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Introduza as suas credenciais de conta do Batch e armazenamento no `AccountSettings.settings` no **Microsoft** projeto.
4. **Compilar e executar** a solução de MultiInstanceTasks para executar o MPI exemplo aplicações em nós de computação de um conjunto do Batch.
5. *Opcional*: Utilize o [portal do Azure] [ portal] ou [BatchLabs] [ batch_labs] para examinar o conjunto de exemplo, o trabalho e tarefas (" MultiInstanceSamplePool","MultiInstanceSampleJob","MultiInstanceSampleTask") antes de eliminar os recursos.

> [!TIP]
> Pode transferir [Visual Studio Community] [ visual_studio] gratuitamente se não tiver o Visual Studio.
>
>

O resultado da `MultiInstanceTasks.exe` é semelhante ao seguinte:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Passos seguintes
* Blogue do Microsoft HPC & equipa do Azure Batch aborda [MPI suporte para Linux no Azure Batch][blog_mpi_linux]e inclui informações sobre como utilizar [OpenFOAM] [ openfoam] com o Batch. Pode encontrar exemplos de código do Python para o [OpenFOAM exemplo no GitHub][github_mpi].
* Saiba como [criar conjuntos de nós de computação do Linux](batch-linux-nodes.md) para utilização nas suas soluções de MPI de lote do Azure.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Descrição geral de várias instâncias"
