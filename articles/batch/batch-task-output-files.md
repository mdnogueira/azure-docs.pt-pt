---
title: "Manter a saída de tarefas e ao armazenamento do Azure com a API do serviço Azure Batch | Microsoft Docs"
description: "Saiba como utilizar a API do serviço de Batch para manter a saída de tarefa e tarefas de lote ao Storage do Azure."
services: batch
author: tamram
manager: timlt
editor: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.openlocfilehash: 2530b7c20347b9fb58aee4dfe693847cf3911741
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Manter os dados da tarefa ao Storage do Azure com a API do serviço Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A partir da versão de 2017-05-01, a API do serviço Batch suporta persistentes dados de saída ao Storage do Azure para tarefas e tarefas de Gestor de tarefas serem executadas em conjuntos com a configuração de máquina virtual. Quando adiciona uma tarefa, pode especificar um contentor no armazenamento do Azure como o destino para o resultado da tarefa. O serviço Batch, em seguida, escreve os dados de saída a esses contentores quando a tarefa estiver concluída.

Uma vantagem em utilizar a API do serviço de Batch para manter o resultado da tarefa é que não tem de modificar a aplicação que a tarefa é executada. Em vez disso, com algumas alterações simples para a aplicação cliente, pode manter resultado da tarefa de dentro do código que cria a tarefa.   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando utiliza a API do serviço de Batch para manter o resultado da tarefa?

O Azure Batch fornece mais de uma forma para manter o resultado da tarefa. Utilizando a API do serviço de Batch é uma abordagem conveniente que é mais adequada para estes cenários:

- Pretende escrever código para manter o resultado da tarefa de dentro da sua aplicação de cliente, sem modificar a aplicação que está a executar a tarefa.
- Pretende manter o resultado das tarefas de lote e o Gestor de tarefas em conjuntos criadas com a configuração de máquina virtual.
- Pretende manter a saída para um contentor de armazenamento do Azure com um nome arbitrário.
- Pretende manter a saída para um contentor de armazenamento do Azure com o nome em conformidade com a [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

Se o seu cenário difere de acordo com os listado acima, poderá ter de considerar uma abordagem diferente. Por exemplo, a API do serviço Batch não suporta atualmente transmissão em fluxo de saída ao Storage do Azure enquanto a tarefa é executada. Fluxo de saída, considere utilizar a biblioteca de convenções de ficheiro Batch, disponível para o .NET. Para outros idiomas, terá de implementar a sua própria solução. Para obter mais informações sobre outras opções para persistentes resultado da tarefa, consulte [manter tarefas e saída ao Storage do Azure](batch-task-output.md). 

## <a name="create-a-container-in-azure-storage"></a>Criar um contentor de armazenamento do Azure

Para manter o resultado da tarefa ao Storage do Azure, terá de criar um contentor que funciona como o destino para os ficheiros de saída. Crie o contentor antes de executar a tarefa, preferencialmente, antes de submeter a tarefa. Para criar o contentor, utilize a biblioteca de clientes do Storage do Azure adequada ou o SDK. Para obter mais informações sobre as APIs de armazenamento do Azure, consulte o [documentação do Storage do Azure](https://docs.microsoft.com/azure/storage/).

Por exemplo, se estiver a escrever a aplicação em c#, utilize o [biblioteca de clientes do Storage do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). O exemplo seguinte mostra como criar um contentor:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Obter uma assinatura de acesso partilhado do contentor

Depois de criar o contentor, obtenha uma assinatura de acesso partilhado (SAS) com acesso de escrita ao contentor. Uma SAS concede acesso delegado ao contentor. O SAS concede acesso com um conjunto especificado de permissões e ao longo de um intervalo de tempo especificado. O serviço Batch tem um SAS com permissões de escrita para o resultado da tarefa de escrita ao contentor. Para obter mais informações sobre SAS, consulte [utilizar assinaturas de acesso partilhado \(SAS\) no armazenamento do Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Quando obtiver um SAS com as APIs de armazenamento do Azure, a API devolve uma cadeia de token SAS. Esta cadeia token inclui todos os parâmetros de SAS, incluindo as permissões e o intervalo durante o qual a SAS é válida. Para utilizar a SAS para aceder a um contentor de armazenamento do Azure, terá de anexar a cadeia do token SAS para o URI do recurso. O recurso URI, juntamente com o token SAS anexado, proporciona acesso autenticado ao Storage do Azure.

O exemplo seguinte mostra como obter uma cadeia de token de SAS só de escrita para o contentor, em seguida, acrescenta a SAS para o URI do contentor:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>Especificar os ficheiros de saída de resultado da tarefa

Para especificar os ficheiros de saída para uma tarefa, crie uma coleção de [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objetos e atribuí-la para o [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) propriedade quando criar a tarefa. 

Exemplo de código seguinte do .NET cria uma tarefa que escreve números aleatórios num ficheiro denominado `output.txt`. O exemplo cria um ficheiro de saída para `output.txt` escrita ao contentor. O exemplo também cria ficheiros de saída para todos os ficheiros de registo que correspondam ao padrão de ficheiro `std*.txt` (_por exemplo,_, `stdout.txt` e `stderr.txt`). O URL do contentor requer a SAS que foi criada anteriormente para o contentor. O serviço Batch utiliza a SAS para autenticar o acesso ao contentor: 

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Especificar um padrão de ficheiro para a correspondência

Quando especificar um ficheiro de saída, pode utilizar o [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) propriedade para especificar um padrão de ficheiro para a correspondência. O padrão de ficheiro pode corresponder zero ficheiros, um único ficheiro ou um conjunto de ficheiros que são criados pela tarefa.

O **FilePattern** propriedade suporta carateres universais de sistema de ficheiros padrão, tais como `*` (para recursiva não corresponde a) e `**` (para recursiva corresponde). Por exemplo, o exemplo de código acima Especifica o padrão de ficheiro a corresponder `std*.txt` modo não recursivo: 

`filePattern: @"..\std*.txt"`

Para carregar um ficheiro único, especificar um padrão de ficheiro com carateres não universais. Por exemplo, o exemplo de código acima Especifica o padrão de ficheiro a corresponder `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Especifique uma condição de carregamento

O [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) propriedade permite condicional o carregamento de ficheiros de saída. Um cenário comum é para carregar um conjunto de ficheiros se a tarefa for bem sucedida e um conjunto diferente de ficheiros se falhar. Por exemplo, poderá carregar ficheiros de registo verboso apenas quando a tarefa falha e sai com um código de saída diferente de zero. Da mesma forma, poderá querer carregar ficheiros de resultado apenas se a tarefa for bem sucedida, como esses ficheiros poderão estar em falta ou incompletas se a tarefa falhar.

O exemplo de código acima define o **UploadCondition** propriedade **TaskCompletion**. Esta definição especifica que o ficheiro está a carregar depois de concluir as tarefas, independentemente do valor do código de saída. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Para outras definições, consulte o [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum.

### <a name="disambiguate-files-with-the-same-name"></a>Ambiguidade ficheiros com o mesmo nome

As tarefas numa tarefa podem produzir ficheiros que tenham o mesmo nome. Por exemplo, `stdout.txt` e `stderr.txt` são criadas para cada tarefa que é executada uma tarefa. Uma vez cada tarefa é executada no seu próprio contexto, estes ficheiros não estão em conflito no sistema de ficheiros do nó. No entanto, ao carregar os ficheiros de várias tarefas para um contentor partilhado, terá de ambiguidade ficheiros com o mesmo nome.

O [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) propriedade especifica o blob de destino ou o diretório virtual para ficheiros de saída. Pode utilizar o **caminho** propriedade nome do blob ou um diretório virtual de forma a que os ficheiros de saída com o mesmo nome exclusivamente são denominados no Storage do Azure. Com o ID de tarefa no caminho é uma boa forma Certifique-se os nomes exclusivos e identificar facilmente os ficheiros.

Se o **FilePattern** propriedade está definida como uma expressão com carateres universais, em seguida, todos os ficheiros que correspondam ao padrão são carregados para o diretório virtual especificado pelo **caminho** propriedade. Por exemplo, se o contentor é `mycontainer`, a tarefa ID é `mytask`, e o padrão de ficheiro é `..\std*.txt`, o URI absoluto para os ficheiros de saída no armazenamento do Azure será semelhante a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se o **FilePattern** propriedade está definida para corresponder a um nome de ficheiro único, que significa que não contém quaisquer carateres universais, em seguida, o valor da **caminho** propriedade especifica o nome do blob completamente qualificado. Se prevê a atribuição de nome entra em conflito com um único ficheiro de várias tarefas, em seguida, inclua o nome do diretório virtual como parte do nome do ficheiro para eliminar a ambiguidade esses ficheiros. Por exemplo, definir o **caminho** propriedade para incluir o ID de tarefa, o caráter delimitador (normalmente, uma barra) e o nome do ficheiro:

`path: taskId + @"/output.txt"`

O URI absoluto para os ficheiros de saída para um conjunto de tarefas será semelhante a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Para obter mais informações sobre os diretórios virtuais no armazenamento do Azure, consulte [listar os blobs num contentor](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).


## <a name="diagnose-file-upload-errors"></a>Diagnosticar erros de carregamento de ficheiros

Se o carregamento de ficheiros de saída ao Storage do Azure falhou, em seguida, a tarefa é movido para o **concluído** estado e o [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) propriedade está definida. Examine o **FailureInformation** propriedade para determinar o erro. Por exemplo, aqui é um erro que ocorre no carregamento do ficheiro se não é possível localizar o contentor: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Em cada carregamento de ficheiros, o Batch escreve dois ficheiros de registo para o nó de computação, `fileuploadout.txt` e `fileuploaderr.txt`. Pode examinar estes ficheiros de registo para obter mais informações sobre uma falha específica. Nos casos em que o carregamento de ficheiros foi nunca tentado, por exemplo porque não foi possível executar a tarefa propriamente dita, em seguida, estes ficheiros de registo não existirá.

## <a name="diagnose-file-upload-performance"></a>Diagnosticar o desempenho de carregamento de ficheiros

O `fileuploadout.txt` progresso do carregamento de registos de ficheiros. Pode examinar este ficheiro para obter mais informações sobre o período de tempo demoram os carregamentos de ficheiros. Tenha em atenção que existem muitos fatores coadjuvantes para carregar o desempenho, incluindo o tamanho de nó, outra atividade no nó ao tempo de carregamento, se o contentor de destino está na mesma região que o conjunto do Batch, o número de nós estiver a carregar para o stora conta de gE em simultâneo e assim sucessivamente.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Utilizar a API do serviço Batch com a norma de convenções de ficheiro Batch

Quando a manter o resultado da tarefa com a API do serviço Batch, pode atribuir o nome do contentor de destino e no entanto, como de blobs. Também pode optar por nome de acordo com o [padrão de convenções de ficheiro Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). O padrão de ficheiro convenções determina os nomes de contentor de destino e blob no Storage do Azure para um ficheiro de saída determinado com base nos nomes de tarefa e tarefas. Se utilizar o padrão de convenções de ficheiro para atribuir nomes a ficheiros de saída, em seguida, os ficheiros de saída estão disponíveis para visualização no [portal do Azure](https://portal.azure.com).

Se estiver a desenvolver em c#, pode utilizar os métodos construídos a [biblioteca convenções de ficheiro Batch para .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Esta biblioteca cria os contentores com o nome corretamente e caminhos de blob por si. Por exemplo, pode chamar a API para obter o nome correto para o contentor, com base no nome da tarefa:

```csharp
string containerName = job.OutputStorageContainerName();
```

Pode utilizar o [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) método para devolver um URL de assinatura (SAS) de acesso partilhado que é utilizado para escrever para o contentor. Em seguida, pode passar esta SAS para o [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) construtor.

Se estiver a desenvolver um idioma diferente do c#, terá de implementar a norma de convenções de ficheiro por si.

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs] [ github_persistoutputs] projeto de exemplo é uma do [exemplos de código do Azure Batch] [ github_samples] no GitHub. Esta solução do Visual Studio demonstra como utilizar a biblioteca de cliente de Batch para .NET para manter o resultado da tarefa para o armazenamento durável. Para executar o exemplo, siga estes passos:

1. Abra o projeto na **Visual Studio versão 2015 ou mais recente**.
2. Adicionar o Batch e armazenamento **credenciais de contas** para **AccountSettings.settings** no projeto Microsoft.
3. **Criar** (mas não executam) a solução. Restaure quaisquer pacotes NuGet, se lhe for pedido.
4. Utilizar o portal do Azure para carregar um [pacote de aplicação](batch-application-packages.md) para **PersistOutputsTask**. Incluir o `PersistOutputsTask.exe` e respetivas assemblagens dependentes no pacote. zip, defina o ID da aplicação para "PersistOutputsTask" e a versão do pacote de aplicação para "1.0".
5. **Iniciar** (executar) a **PersistOutputs** projeto.
6. Quando lhe for pedido para escolher a tecnologia de persistência a utilizar para executar o exemplo, introduza **2** para executar o exemplo utilizando a API do serviço de Batch para manter o resultado da tarefa.
7. Se assim o desejar, execute o exemplo, introduzir **3** para manter o resultado com a API do serviço Batch bem como para o caminho de blob e contentor de destino, de acordo com o padrão de ficheiro convenções de nomes.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o resultado da tarefa persistentes com a biblioteca de convenções de ficheiro para o .NET, consulte [manter os dados de tarefas e ao armazenamento do Azure com a biblioteca de convenções de ficheiro Batch para .NET manter ](batch-task-output-file-conventions.md).
- Para obter informações sobre outras abordagens para dados de saída persistentes no Azure Batch, consulte [manter tarefas e saída ao Storage do Azure](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
