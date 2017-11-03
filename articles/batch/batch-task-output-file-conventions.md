---
title: "Manter a saída de tarefas e ao armazenamento do Azure com a biblioteca de convenções de ficheiro para .NET - Azure Batch | Microsoft Docs"
description: "Saiba como utilizar a biblioteca de convenções de ficheiro do Azure Batch para .NET para manter a saída de tarefa e tarefas de lote ao Storage do Azure e ver o resultado persistente no portal do Azure."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a9de327c20463469bc91d9720aa17333a36f919e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net-to-persist"></a>Manter os dados de tarefas e ao armazenamento do Azure com a biblioteca de convenções de ficheiro Batch para .NET manter 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Uma forma para manter os dados de tarefa consiste em utilizar o [biblioteca convenções de ficheiro do Azure Batch para .NET][nuget_package]. A biblioteca de convenções de ficheiros simplifica o processo do armazenamento de dados de saída da tarefa ao Storage do Azure e obtê-lo. Pode utilizar a biblioteca de convenções de ficheiro no código de tarefas e o cliente &mdash; no código de tarefas para ficheiros persistentes e no código de cliente para listar e obtê-los. O código de tarefa também pode utilizar a biblioteca para obter o resultado das tarefas a montante, tal como um [dependências de tarefas](batch-task-dependencies.md) cenário. 

Para obter ficheiros de saída com a biblioteca de convenções de ficheiro, pode localizar os ficheiros para uma determinada tarefa tarefas enumerando-los por ID e o objetivo. Não precisa de saber os nomes ou localizações dos ficheiros. Por exemplo, pode utilizar a biblioteca de convenções de ficheiro para listar todos os ficheiros intermédios de uma determinada tarefa ou obter um ficheiro de pré-visualização para uma determinada tarefa.

> [!TIP]
> A partir da versão de 2017-05-01, a API do serviço Batch suporta persistentes dados de saída ao Storage do Azure para tarefas e tarefas de Gestor de tarefas serem executadas em conjuntos criados com a configuração de máquina virtual. A API do serviço Batch fornece uma forma simples para manter um resultado de dentro do código que cria uma tarefa e funciona como uma alternativa para a biblioteca de convenções de ficheiro. Pode modificar as aplicações de cliente do Batch para manter a saída sem necessidade de atualizar a aplicação que está a executar a tarefa. Para obter mais informações, consulte [API do serviço de dados de tarefa de persistência ao Storage do Azure com o Batch](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando utiliza a biblioteca de convenções de ficheiro para manter o resultado da tarefa?

O Azure Batch fornece mais de uma forma para manter o resultado da tarefa. As convenções de ficheiro é mais adequada para estes cenários:

- Pode facilmente modificar o código para a aplicação que está a executar a tarefa para manter os ficheiros utilizando a biblioteca de convenções de ficheiro.
- Pretende-se aos dados de fluxo para o Storage do Azure enquanto a tarefa ainda está em execução.
- Pretende manter os dados a partir de conjuntos criados com a configuração do serviço em nuvem ou a configuração de máquina virtual.
- A aplicação cliente ou outras tarefas na tarefa tem de localizar e transferir ficheiros de saída de tarefas por ID ou por objetivo. 
- Pretende ver o resultado da tarefa no portal do Azure.

Se o seu cenário difere de acordo com os listado acima, poderá ter de considerar uma abordagem diferente. Para obter mais informações sobre outras opções para persistentes resultado da tarefa, consulte [manter tarefas e saída ao Storage do Azure](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>O que é as convenções de ficheiro Batch padrão?

O [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) fornece um esquema de nomenclatura para os contentores de destino e os caminhos de BLOBs para os quais os ficheiros de saída são escritos. Ficheiros transferido para o armazenamento do Azure que cumprem a norma de convenções de ficheiros estão automaticamente disponíveis para visualização no portal do Azure. O portal tem conhecimento de convenção de nomenclatura e, por isso, pode visualizar ficheiros que adira ao mesmo.

A biblioteca de convenções de ficheiro para .NET nomes automaticamente os ficheiros de saída de tarefas, de acordo com as convenções de ficheiro padrão e contentores de armazenamento. A biblioteca de convenções de ficheiros também fornece métodos para ficheiros de saída no armazenamento do Azure, de acordo com ID da tarefa, o ID de tarefa ou o objetivo de consulta.   

Se estiver a desenvolver com um idioma diferente do .NET, pode implementar a norma de convenções de ficheiro por si na sua aplicação. Para obter mais informações, consulte [padrão sobre o convenções de ficheiro Batch](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Associar uma conta de armazenamento do Azure à sua conta do Batch

Para manter os dados de saída para o armazenamento do Azure utilizando a biblioteca de convenções de ficheiro, primeiro tem de associar uma conta de armazenamento do Azure à sua conta de Batch. Se não tiver o feito, associar uma conta de armazenamento à sua conta de Batch, utilizando o [portal do Azure](https://portal.azure.com):

1. No portal do Azure, navegue para a sua conta do Batch. 
2. Em **definições**, selecione **conta de armazenamento**.
3. Se ainda não tiver uma conta de armazenamento associada à sua conta do Batch, clique em **conta de armazenamento (nenhum)**.
4. Selecione uma conta de armazenamento na lista para a sua subscrição. Para melhor desempenho, utilize uma conta de armazenamento do Azure que se encontra na mesma região que a conta do Batch em que as tarefas estão em execução.

## <a name="persist-output-data"></a>Manter os dados de saída

Para manter os dados de saída de tarefas e com a biblioteca de convenções de ficheiros, criar um contentor de armazenamento do Azure, em seguida, guarde o resultado ao contentor. Utilize o [biblioteca de clientes do Storage do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage) no código da tarefa para carregar o resultado da tarefa ao contentor. 

Para obter mais informações sobre como trabalhar com contentores e blobs no armazenamento do Azure, consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Todas as saídas de tarefas e persistente com as convenções de ficheiro de biblioteca estão armazenados no contentor do mesmo. Se um grande número de tarefas tentar manter os ficheiros ao mesmo tempo, [os limites de armazenamento](../storage/common/storage-performance-checklist.md#blobs) poderá ser forçado.
> 
> 

### <a name="create-storage-container"></a>Criar contentor de armazenamento

Para manter o resultado da tarefa ao Storage do Azure, criar um contentor chamando [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Este método de extensão aceita um [CloudStorageAccount] [ net_cloudstorageaccount] objeto como parâmetro. Cria um contentor com o nome, de acordo com a norma de convenções de ficheiro, para que o respetivo conteúdo é detetável pelo portal do Azure e os métodos de obtenção abordados posteriormente o artigo.

Normalmente, colocar o código para criar um contentor na sua aplicação de cliente &mdash; a aplicação que cria os agrupamentos, tarefas e tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Resultados de tarefas de arquivo

Agora que já preparou um contentor de armazenamento do Azure, as tarefas podem guardar saída para o contentor utilizando o [TaskOutputStorage] [ net_taskoutputstorage] encontrar a classe na biblioteca de convenções de ficheiro.

No seu código de tarefas, primeiro crie um [TaskOutputStorage] [ net_taskoutputstorage] objeto, em seguida, quando a tarefa de concluir o trabalho, invoque o [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] método para guardar o resultado ao Storage do Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

O `kind` parâmetro o [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) método categoriza os ficheiros persistentes. Existem quatro predefinidas [TaskOutputKind] [ net_taskoutputkind] tipos: `TaskOutput`, `TaskPreview`, `TaskLog`, e `TaskIntermediate.` também pode definir categorias personalizadas de saída.

Estes tipos de saída permitem-lhe especificar o tipo de saídas para listar quando consultar mais tarde o Batch para as saídas persistentes de uma determinada tarefa. Por outras palavras, quando listar as saídas para uma tarefa, pode filtrar a lista dos tipos de saída. Por exemplo, "fornecer-me a *pré-visualização* de saída para a tarefa *109*." Obter mais informações sobre a listagem e obter as saídas aparece no [obter o resultado](#retrieve-output) posteriormente neste artigo.

> [!TIP]
> O tipo de saída também determina onde no portal do Azure um determinado ficheiro aparece: *TaskOutput*-categorizados ficheiros são apresentados em **ficheiros de saída de tarefas**, e *TaskLog* os ficheiros são apresentados em **registos de tarefas**.
> 
> 

### <a name="store-job-outputs"></a>Saídas da tarefa de arquivo

Para além de armazenar saídas de tarefas, pode armazenar as saídas associadas uma tarefa de toda. Por exemplo, a tarefa de intercalação de uma tarefa de composição de filmes, manter a filmes totalmente composto como uma saída da tarefa. Quando a tarefa estiver concluída, a aplicação cliente pode listar e obter as saídas da tarefa e não necessita de consultar as tarefas individuais.

Armazenar o resultado da tarefa ao chamar o [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] método e especifique o [JobOutputKind] [ net_joboutputkind] e nome de ficheiro:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Tal como com a **TaskOutputKind** tipo para saídas de tarefas, utilize o [JobOutputKind] [ net_joboutputkind] tipo a categorizar uma tarefa do persistente a ficheiros. Este parâmetro permite-lhe consulta posterior para um tipo específico de saída (lista). O **JobOutputKind** tipo inclui categorias de saída e de pré-visualização e suporta a criação de categorias personalizadas.

### <a name="store-task-logs"></a>Registos da tarefa de arquivo

Para além de persistência de um ficheiro para o armazenamento durável quando uma tarefa ou a tarefa estiver concluída, poderá ser necessário manter os ficheiros que são atualizados durante a execução de uma tarefa &mdash; ficheiros de registo ou `stdout.txt` e `stderr.txt`, por exemplo. Para esta finalidade, a biblioteca de convenções de ficheiros de lote do Azure fornece o [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] método. Com [SaveTrackedAsync][net_savetrackedasync], pode controlar as atualizações para um ficheiro no nó (com o intervalo que especificou) e manter essas atualizações ao Storage do Azure.

No fragmento de código seguinte, utilizamos [SaveTrackedAsync] [ net_savetrackedasync] atualizar `stdout.txt` no armazenamento do Azure cada 15 segundos durante a execução da tarefa:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

A secção Comentada `Code to process data and produce output file(s)` é um marcador de posição para o código que a tarefa que normalmente iria efetuar. Por exemplo, pode ter o código que transfere dados do Storage do Azure e efetua a transformação ou de cálculo no mesmo. A parte importante deste fragmento é que demonstra como pode encapsular desse código, num `using` bloco atualizar periodicamente um ficheiro com [SaveTrackedAsync][net_savetrackedasync].

O agente de nó é um programa que é executada em cada nó no conjunto e fornece a interface de comando e controlo entre o nó e o serviço Batch. O `Task.Delay` chamada é necessária no fim deste `using` bloco para garantir que o agente de nó tem tempo para esvaziar o conteúdo de saída padrão para o ficheiro de stdout.txt no nó. Sem este atraso, é possível perder os último alguns segundos de saída. Este atraso pode não ser necessário para todos os ficheiros.

> [!NOTE]
> Quando ativa o ficheiro de controlo com **SaveTrackedAsync**, apenas *acrescenta* para o ficheiro controlado são mantidas ao Storage do Azure. Utilize este método apenas para ficheiros de registo não rodar ou outros ficheiros que são escritos com as operações de acréscimo ao fim do ficheiro de controlo.
> 
> 

## <a name="retrieve-output-data"></a>Obter dados de saída

Ao obter o resultado persistente utilizando a biblioteca de convenções de ficheiro do Azure Batch, fazê-lo de forma tarefas e tarefa-centrada. Pode pedir a saída para indicada tarefas sem necessidade de saber o caminho no Storage do Azure ou o mesmo nome do respetivo ficheiro. Em vez disso, pode pedir ficheiros de saída por tarefas ou ID de tarefa

O seguinte fragmento de código itera através de tarefas de uma tarefa, imprime algumas informações sobre os ficheiros de saída da tarefa e, em seguida, transfere os ficheiros de armazenamento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Ver ficheiros de saída no portal do Azure

O portal do Azure apresenta os ficheiros de saída de tarefas e conta de registos que são mantidos para um armazenamento do Azure ligado através de [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Pode implementar estas convenções de de um idioma de à sua escolha, ou pode utilizar a biblioteca de convenções de ficheiros nas suas aplicações de .NET.

Para ativar a apresentação dos seus ficheiros de saída no portal, tem de satisfazer os seguintes requisitos:

1. [Associar uma conta de armazenamento do Azure](#requirement-linked-storage-account) à sua conta do Batch.
2. Respeitar as convenções de nomenclatura predefinidas para contentores de armazenamento e de ficheiros quando a persistência de saídas. Pode encontrar a definição destas convenções na biblioteca do ficheiro convenções [Leia-me][github_file_conventions_readme]. Se utilizar o [convenções de ficheiro do Azure Batch] [ nuget_package] biblioteca para manter o resultado, os ficheiros são mantidos, de acordo com a norma de convenções de ficheiro.

Para ver registos e ficheiros de saída da tarefa no portal do Azure, navegue para a tarefa cuja saída que está interessado, em seguida, clique em **guardados ficheiros de saída** ou **guardar registos**. Esta imagem mostra o **guardados ficheiros de saída** para a tarefa com o ID "007":

![Tarefa saídas painel no portal do Azure][2]

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs] [ github_persistoutputs] projeto de exemplo é uma do [exemplos de código do Azure Batch] [ github_samples] no GitHub. Esta solução do Visual Studio demonstra como utilizar a biblioteca de convenções de ficheiro do Azure Batch para manter o resultado da tarefa para o armazenamento durável. Para executar o exemplo, siga estes passos:

1. Abra o projeto na **Visual Studio versão 2015 ou mais recente**.
2. Adicionar o Batch e armazenamento **credenciais de contas** para **AccountSettings.settings** no projeto Microsoft.
3. **Criar** (mas não executam) a solução. Restaure quaisquer pacotes NuGet, se lhe for pedido.
4. Utilizar o portal do Azure para carregar um [pacote de aplicação](batch-application-packages.md) para **PersistOutputsTask**. Incluir o `PersistOutputsTask.exe` e respetivas assemblagens dependentes no pacote. zip, defina o ID da aplicação para "PersistOutputsTask" e a versão do pacote de aplicação para "1.0".
5. **Iniciar** (executar) a **PersistOutputs** projeto.
6. Quando lhe for pedido para escolher a tecnologia de persistência a utilizar para executar o exemplo, introduza **1** para executar o exemplo utilizando a biblioteca de convenções de ficheiro para manter o resultado da tarefa. 

## <a name="next-steps"></a>Passos seguintes

### <a name="get-the-batch-file-conventions-library-for-net"></a>Obter a biblioteca de convenções de ficheiro Batch para .NET

A biblioteca de convenções de ficheiro Batch para .NET está disponível no [NuGet][nuget_package]. A biblioteca expande o [CloudJob] [ net_cloudjob] e [CloudTask] [ net_cloudtask] classes com métodos de novo. Consulte também o [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) para a biblioteca de convenções de ficheiro.

O [código fonte] [ github_file_conventions] para as convenções de ficheiro de biblioteca está disponível no GitHub no Microsoft Azure SDK para o repositório de .NET. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Explorar outras abordagens para dados de saída persistentes

- Consulte [manter tarefas e saída ao Storage do Azure](batch-task-output.md) para uma descrição geral dos dados persistentes de tarefa e tarefas.
- Consulte [API do serviço de dados de tarefa de persistência ao Storage do Azure com o Batch](batch-task-output-files.md) para aprender a utilizar a API do serviço de Batch para manter os dados de saída.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Ficheiros de saída guardado e guardado seletores de registos no portal"
[2]: ./media/batch-task-output/task-output-02.png "Tarefa saídas painel no portal do Azure"
