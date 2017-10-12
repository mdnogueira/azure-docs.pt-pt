---
title: "Tutorial – Utilizar a biblioteca de cliente do Azure Batch para .NET | Microsoft Docs"
description: "Obter informações sobre os conceitos básicos do Azure Batch e criar uma solução simples utilizando o .NET."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf8fdca51a6a4ad1b7cd4fe6980543199f6b36e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Comece a criar soluções com a biblioteca de cliente do Batch para .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Conheça as informações básicas do [Azure Batch][azure_batch] e da biblioteca [Batch .NET][net_api] neste artigo, à medida que debatemos um exemplo de aplicação C# passo a passo. Observamos como o exemplo de aplicação tira partido do serviço Batch para processar uma carga de trabalho paralela na cloud e de que forma interage com o [Armazenamento do Azure](../storage/common/storage-introduction.md) para teste e obtenção de ficheiros. Irá conhecer um fluxo de trabalho de aplicações Batch comuns e obter uma compreensão base dos componentes principais do Batch, como trabalhos, conjuntos e nós de computação.

![Fluxo de trabalho da solução Batch (básico)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo parte do princípio de que tem um conhecimento prático do C# e do Visual Studio. Também parte do princípio de que consegue satisfazer os requisitos de criação de conta que são especificados abaixo para o Azure e os serviços Batch e Armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: se ainda não tiver uma subscrição do Azure, [crie uma conta do Azure gratuita][azure_free_account].
* **Conta do Batch**: assim que tiver uma subscrição do Azure, [crie uma conta do Azure Batch](batch-account-create-portal.md).
* **Conta de Armazenamento**: veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

> [!IMPORTANT]
> Atualmente, o Batch suporta *apenas* o tipo de conta de armazenamento para **fins gerais**, conforme descrito no passo 5, [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account), em [Acerca das contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
>
>

### <a name="visual-studio"></a>Visual Studio
Tem de ter o **Visual Studio 2015 ou mais recente** para compilar o projeto de exemplo. Pode encontrar versões de avaliação e gratuitas do Visual Studio na [descrição geral dos produtos Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Exemplo de código *DotNetTutorial*
A amostra [DotNetTutorial][github_dotnettutorial] é um dos muito exemplos de código Batch encontrados no repositório [azure-batch-samples][github_samples] do GitHub. Pode transferir todos os exemplos ao clicar em **Clonar ou transferir > Transferir ZIP** na home page do repositório ou ao clicar na ligação de transferência direta [azure-batch-samples-master.zip][github_samples_zip]. Assim que extrair o conteúdo do ficheiro ZIP, pode encontrar a solução na seguinte pasta:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Explorador do Azure Batch (opcional)
O [Explorador do Azure Batch][github_batchexplorer] é um utilitário gratuito que está incluído no repositório [azure-batch-samples][github_samples] do GitHub. Embora não seja necessário para concluir este tutorial, poderá ser útil no desenvolvimento e depuração das soluções Batch.

## <a name="dotnettutorial-sample-project-overview"></a>Descrição geral do projeto de exemplo DotNetTutorial
O exemplo de código *DotNetTutorial* é uma solução do Visual Studio que consiste em dois projetos: **DotNetTutorial** e **TaskApplication**.

* **DotNetTutorial** é a aplicação cliente que interage com os serviços Batch e Armazenamento para executar uma carga de trabalho paralela em nós de computação (máquinas virtuais). O DotNetTutorial é executado na sua estação de trabalho local.
* **TaskApplication** é o programa que é executado em nós de computação do Azure para realizar o trabalho real. No exemplo, `TaskApplication.exe` analisa o texto num ficheiro transferido do Armazenamento do Azure (o ficheiro de entrada). Em seguida, gera um ficheiro de texto (o ficheiro de saída) que contém uma lista das três palavras principais que aparecem no ficheiro de entrada. Depois de criar o ficheiro de saída, o TaskApplication carrega o ficheiro para o Armazenamento do Azure. Esta ação torna-o disponível para a aplicação cliente para transferência. O TaskApplication é executado em paralelo em diversos nós de computação no serviço Batch.

O diagrama seguinte ilustra as operações primárias que são executadas pela aplicação cliente, *DotNetTutorial*, e a aplicação que é executada pelas tarefas, *TaskApplication*. Este fluxo de trabalho básico é típico de várias soluções de computação que são criadas com o Batch. Embora não demonstrem todas as funcionalidades disponíveis no serviço Batch, quase todos os cenários do Batch incluem partes deste fluxo de trabalho.

![Exemplo de fluxo de trabalho do Batch][8]<br/>

[**Passo 1.**](#step-1-create-storage-containers) Criar **contentores** no Armazenamento de Blobs do Azure.<br/>
[**Passo 2.**](#step-2-upload-task-application-and-data-files) Carregar os ficheiros da aplicação de tarefa e os ficheiros de entrada para contentores.<br/>
[**Passo 3.**](#step-3-create-batch-pool) Criar um **conjunto** do Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O conjunto **StartTask** transfere os ficheiros binários de tarefas (TaskApplication) para nós à medida que se associam ao conjunto.<br/>
[**Passo 4.**](#step-4-create-batch-job) Criar um **trabalho** do Batch.<br/>
[**Passo 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** ao trabalho.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas estão agendadas para serem executadas em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa transfere os respetivos dados de entrada a partir do Armazenamento do Azure e, em seguida, começa a execução.<br/>
[**Passo 6.**](#step-6-monitor-tasks) Monitorizar tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** À medida que as tarefas são concluídas, carregam os respetivos dados de saída para o Armazenamento do Azure.<br/>
[**Passo 7.**](#step-7-download-task-output) Transferir o resultado da tarefa do Armazenamento.

Conforme mencionado, nem todas as soluções do Batch executam estes passos exatos e podem incluir muitos mais, mas o exemplo de aplicação *DotNetTutorial* demonstra processos comuns encontrados numa solução do Batch.

## <a name="build-the-dotnettutorial-sample-project"></a>Compilar o projeto de exemplo *DotNetTutorial*
Para poder executar com êxito o exemplo, tem de especificar primeiro as credenciais da conta do Batch e do Armazenamento no ficheiro `Program.cs` do projeto *DotNetTutorial*. Se ainda não o tiver o feito, abra a solução no Visual Studio, fazendo duplo clique no ficheiro de solução `DotNetTutorial.sln`. Em alternativa, abra-a no Visual Studio através do menu **Ficheiro > Abrir > Projeto/Solução**.

Abra `Program.cs` dentro do projeto *DotNetTutorial*. Em seguida, adicione as suas credenciais conforme especificado perto da parte superior do ficheiro:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> Tal como mencionado acima, atualmente, tem de especificar as credenciais de uma conta de armazenamento para **fins gerais** no Armazenamento do Azure. As aplicações do Batch utilizam o armazenamento de blobs na conta de armazenamento para **fins gerais**. Não especifique as credenciais de uma conta de Armazenamento criada através da seleção do tipo de conta *Armazenamento de blobs*.
>
>

Pode encontrar as credenciais da conta do Batch e do Armazenamento no painel de conta de cada serviço do [portal do Azure][azure_portal]:

![Credenciais do Batch no portal][9]
![Credenciais do Armazenamento no portal][10]<br/>

Agora que atualizou o projeto com as suas credenciais, clique com o botão direito do rato na solução no Explorador de Soluções e clique em **Compilar Solução**. Confirme o restauro de quaisquer pacotes NuGet, se lhe for pedido.

> [!TIP]
> Se os pacotes NuGet não forem restaurados automaticamente ou se observar erros sobre uma falha ao restaurar os pacotes, certifique-se de que tem o [Gestor de Pacotes NuGet][nuget_packagemgr] instalado. Em seguida, ative a transferência dos pacotes em falta. Veja [Enabling Package Restore During Build (Ativar o Restauro de Pacotes Durante a Compilação)][nuget_restore] para ativar a transferência dos pacotes.
>
>

Nas secções seguintes, dividimos o exemplo de aplicação nos passos que executa para processar uma carga de trabalho no serviço Batch e descrevemos esses passos em pormenor. Aconselhamo-lo a ver a solução aberta no Visual Studio à medida que vai acompanhando o resto do artigo, uma vez que o exemplo não aborda todas as linhas de código.

Navegue até ao início do método `MainAsync` no ficheiro `Program.cs` do projeto *DotNetTutorial*, para começar com o passo 1. Cada um dos passos abaixo segue aproximadamente a evolução das chamadas de método em `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Passo 1: criar contentores de armazenamento
![Criar contentores no Armazenamento do Azure][1]
<br/>

O Batch inclui suporte incorporado para interagir com o Armazenamento do Azure. Os contentores na sua conta de Armazenamento fornecerão os ficheiros necessários para as tarefas que são executadas na sua conta do Batch. Os contentores fornecem também um local para armazenar os dados de saída que as tarefas produzem. A primeira coisa que a aplicação cliente *DotNetTutorial* faz é criar três contentores no [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md):

* **aplicação**: este contentor armazenará a aplicação executada pelas tarefas, bem como qualquer uma das respetivas dependências, tal como DLL.
* **entrada**: as tarefas irão transferir os ficheiros de dados a processar a partir do contentor de *entrada*.
* **saída**: quando as tarefas concluírem o processamento do ficheiro de entrada, irão carregar os resultados para o contentor de *saída*.

Para interagir com uma conta de Armazenamento e criar contentores, utilizamos a [Biblioteca de Clientes de Armazenamento do Azure para .NET][net_api_storage]. Criamos uma referência para a conta com [CloudStorageAccount][net_cloudstorageaccount] e, a partir daí, criamos um [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Utilizamos a referência `blobClient` em toda a aplicação e transmitimo-la como um parâmetro a vários métodos. Um exemplo disto encontra-se no bloco de código que aparece imediatamente a seguir ao acima mencionado, onde chamamos `CreateContainerIfNotExistAsync` para criar efetivamente os contentores.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Depois de os contentores estarem criados, a aplicação pode agora carregar os ficheiros que serão utilizados pelas tarefas.

> [!TIP]
> O artigo [Como utilizar o Armazenamento de Blobs a partir do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) fornece uma boa descrição geral de como trabalhar com blobs e contentores de Armazenamento do Azure. Deve estar perto do topo da sua lista de leitura à medida que começa a trabalhar com o Batch.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Passo 2: carregar a aplicação de tarefa e os ficheiros de dados
![Carregar a aplicação de tarefa e os ficheiros de entrada (dados) para contentores][2]
<br/>

Na operação de carregamento de ficheiros, o *DotNetTutorial* define primeiro coleções de caminhos de ficheiros de **aplicação** e **entrada**, tal como existem no computador local. Em seguida, carrega estes ficheiros para os contentores que criou no passo anterior.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Existem dois métodos em `Program.cs` que estão relacionados com o processo de carregamento:

* `UploadFilesToContainerAsync`: este método devolve uma coleção de objetos [ResourceFile][net_resourcefile] (abordados abaixo) e chama internamente `UploadFileToContainerAsync` para carregar cada ficheiro que é transmitido no parâmetro *filePaths*.
* `UploadFileToContainerAsync`: este é o método que executa efetivamente o carregamento de ficheiros e cria os objetos [ResourceFile][net_resourcefile]. Depois de carregar o ficheiro, obtém uma assinatura de acesso partilhado (SAS) para o ficheiro e devolve um objeto ResourceFile que a representa. As assinaturas de acesso partilhado também são abordadas abaixo.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
Um [ResourceFile][net_resourcefile] fornece tarefas no Batch com o URL para um ficheiro no Armazenamento do Azure que é transferido para um nó de computação antes de essa tarefa ser executada. A propriedade [ResourceFile.BlobSource][net_resourcefile_blobsource] especifica o URL completo do ficheiro, tal como existe no Armazenamento do Azure. O URL também pode incluir uma assinatura de acesso partilhado (SAS) que proporciona acesso seguro ao ficheiro. A maioria dos tipos de tarefas no Batch .NET inclui uma propriedade *ResourceFiles*, que inclui:

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

O exemplo de aplicação DotNetTutorial não utiliza os tipos de tarefas JobPreparationTask ou JobReleaseTask, mas pode ler mais sobre os mesmos em [Executar tarefas de preparação e de conclusão da tarefa em nós de computação do Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso partilhado (SAS)
As assinaturas de acesso partilhado são cadeias que, quando incluídas como parte de um URL, proporcionam acesso seguro aos contentores e blobs do Armazenamento do Azure. A aplicação DotNetTutorial utiliza os URLs de assinaturas de acesso partilhado de blobs e de contentores, e demonstra como obter estas cadeias de assinatura de acesso partilhado a partir do serviço Armazenamento.

* **Assinaturas de acesso partilhado de blob**: o StartTask do conjunto no DotNetTutorial utiliza assinaturas de acesso partilhado de blob quando transfere os binários da aplicação e os ficheiros de dados de entrada a partir do Armazenamento (veja o passo 3 abaixo). O método `UploadFileToContainerAsync` no ficheiro `Program.cs` do DotNetTutorial contém o código que obtém a assinatura de acesso partilhado de cada blob. Isto é feito chamando [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Assinaturas de acesso partilhado de contentor**: à medida que cada tarefa conclui o respetivo trabalho no nó de computação, carrega o respetivo ficheiro de saída para o contentor de *saída* no Armazenamento do Azure. Para fazê-lo, o TaskApplication utiliza uma assinatura de acesso partilhado de contentor, que fornece acesso de escrita ao contentor como parte do caminho quando carrega o ficheiro. A obtenção da assinatura de acesso partilhado do contentor é feita de forma semelhante à obtenção da assinatura de acesso partilhado do blob. No DotNetTutorial, irá descobrir que o método de ajuda `GetContainerSasUrl` chama [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] para fazê-lo. Irá ler mais sobre como o TaskApplication utiliza a assinatura de acesso partilhado do contentor no "Passo 6: monitorizar tarefas".

> [!TIP]
> Consulte a série de duas partes sobre assinaturas de acesso partilhado, [Parte 1: compreender o modelo de assinatura de acesso partilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: criar e utilizar uma assinatura de acesso partilhado (SAS) com o armazenamento de Blobs](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro aos dados da sua conta de Armazenamento.
>
>

## <a name="step-3-create-batch-pool"></a>Passo 3: criar conjunto do Batch
![Criar um conjunto do Batch][3]
<br/>

Um **conjunto** do Batch é uma coleção de nós de computação (máquinas virtuais) nos quais o Batch executa tarefas de um trabalho.

Depois de carregar a aplicação e os ficheiros de dados para a conta de Armazenamento com APIs de Armazenamento do Azure, o *DotNetTutorial* começa a fazer chamadas para o serviço Batch com APIs disponibilizadas pela biblioteca .NET do Batch. O código cria primeiro um [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Em seguida, o exemplo cria um conjunto de nós de computação na conta do Batch, com uma chamada para `CreatePoolIfNotExistsAsync`. `CreatePoolIfNotExistsAsync` utiliza o método [BatchClient.PoolOperations.CreatePool][net_pool_create] para criar um novo conjunto no serviço Batch:

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-core, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Quando cria um conjunto com [CreatePool][net_pool_create], especifica vários parâmetros, como o número de nós de computação, o [tamanho dos nós](../cloud-services/cloud-services-sizes-specs.md) e o sistema operativo dos nós. No *DotNetTutorial*, utilizamos [CloudServiceConfiguration][net_cloudserviceconfiguration] para especificar o Windows Server 2012 R2 a partir dos [Serviços Cloud](../cloud-services/cloud-services-guestos-update-matrix.md). 

Também pode criar conjuntos de nós de computação que sejam Máquinas Virtuais (VMs) do Azure ao especificar [VirtualMachineConfiguration][net_virtualmachineconfiguration] para o seu conjunto. Pode criar um conjunto de nós de computação de VM a partir de [imagens do Linux](batch-linux-nodes.md) ou do Windows. A origem das imagens de VMs podem ser:

- O [Marketplace das Máquinas Virtuais do Azure][vm_marketplace], que disponibiliza imagens do Windows e do Linux prontas a utilizar. 
- Uma imagem personalizada preparada e fornecida por si. Para obter mais informações sobre as imagens personalizadas, veja [Desenvolver soluções de computação paralela em grande escala com o Batch](batch-api-basics.md#pool).

> [!IMPORTANT]
> São-lhe cobrados os recursos de computação no Batch. Para minimizar os custos, pode reduzir `targetDedicatedComputeNodes` para 1 antes de executar e exemplo.
>
>

Juntamente com estas propriedades de nó físico, também pode especificar um [StartTask][net_pool_starttask] para o conjunto. O StartTask é executado em cada nó à medida que esse nó se associa ao conjunto, e sempre que um nó for reiniciado. O StartTask é especialmente útil para instalar aplicações em nós de computação antes da execução das tarefas. Por exemplo, se as suas tarefas processarem dados com scripts do Python, pode utilizar um StartTask para instalar o Python nos nós de computação.

Neste exemplo de aplicação, o StartTask copia os ficheiros que transfere do Armazenamento (que são especificados através da propriedade [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) do StartTask, a partir do diretório de trabalho do StartTask para o diretório partilhado ao qual *todas* as tarefas em execução no nó podem aceder. Essencialmente, esta ação copia `TaskApplication.exe` e as respetivas dependências para o diretório partilhado em cada nó, à medida que o nó se associa ao conjunto, para que quaisquer tarefas executadas no nó possam aceder ao mesmo.

> [!TIP]
> A funcionalidade **pacotes de aplicações** do Azure Batch proporciona outra forma de colocar a sua aplicação nos nós de computação num conjunto. Veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md) para obter mais detalhes.
>
>

A utilização de duas variáveis de ambiente na propriedade *CommandLine* do StartTask, `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`, é também visível no fragmento de código acima. Cada nó de computação num conjunto do Batch é automaticamente configurado com várias variáveis de ambiente que são específicas do Batch. Qualquer processo executado por uma tarefa tem acesso a estas variáveis de ambiente.

> [!TIP]
> Para obter mais informações sobre as variáveis de ambiente que estão disponíveis nos nós de computação de um conjunto do Batch e informações sobre os diretórios de trabalho de tarefas, veja as secções [Definições de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) e [Ficheiros e diretórios](batch-api-basics.md#files-and-directories) na [Descrição geral da funcionalidade Batch para programadores](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Passo 4: criar trabalho do Batch
![Criar trabalho do Batch][4]<br/>

Um **trabalho** do Batch é uma coleção de tarefas e está associado a um conjunto de nós de computação. As tarefas num trabalho são executadas nos nós de computação do conjunto associado.

Pode utilizar um trabalho apenas para organizar e controlar tarefas em cargas de trabalho relacionadas, mas também para impor determinadas restrições, como o tempo de execução máximo para a tarefa (e, por extensão, as tarefas), bem como a prioridade do trabalho em relação a outros trabalhos da conta do Batch. Neste exemplo, no entanto, o trabalho está associado apenas ao conjunto que foi criado no passo 3. Não estão configuradas propriedades adicionais.

Todos os trabalhos do Batch estão associados a um conjunto específico. Esta associação indica os nós nos quais as tarefas do trabalho irão ser executadas. Isto é especificado através da propriedade [CloudJob.PoolInformation][net_job_poolinfo], conforme ilustrado no fragmento de código abaixo.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Agora que criou um trabalho, as tarefas são adicionadas para realizar o trabalho.

## <a name="step-5-add-tasks-to-job"></a>Passo 5: adicionar tarefas ao trabalho
![Adicionar tarefas ao trabalho][5]<br/>
*(1) As tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para serem executadas em nós e (3) as tarefas transferem os ficheiros de dados a processar*

As **tarefas** do Batch são as unidades de trabalho individuais que são executadas nos nós de computação. Uma tarefa tem uma linha de comandos e executa os scripts ou executáveis que especificar nessa linha de comandos.

Para trabalhar de facto, as tarefas têm de ser adicionadas a um trabalho. Cada [CloudTask][net_task] é configurada através de uma propriedade de linha de comandos e de [ResourceFiles][net_task_resourcefiles] (tal como acontece com o StartTask do conjunto) que a tarefa transfere para o nó antes de a respetiva linha de comandos ser executada automaticamente. No projeto de exemplo *DotNetTutorial*, cada tarefa processa apenas um ficheiro. Assim, a respetiva coleção ResourceFiles contém um único elemento.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> Quando acedem a variáveis de ambiente, como `%AZ_BATCH_NODE_SHARED_DIR%`, ou executam uma aplicação que não está no `PATH` do nó, as linhas de comandos de tarefas têm de ter o prefixo `cmd /c`. Isto irá executar explicitamente o interpretador de comandos e dar-lhe instruções para terminar depois de o comando ser executado. Este requisito é desnecessário se as suas tarefas executarem uma aplicação no `PATH` do nó (como *robocopy.exe* ou *powershell.exe*) e não forem utilizadas variáveis de ambiente.
>
>

Dentro do ciclo `foreach` no fragmento de código acima, pode ver que a linha de comandos da tarefa está construída de forma a que três argumentos da linha de comandos sejam transmitidos para *TaskApplication.exe*:

1. O **primeiro argumento** corresponde ao caminho do ficheiro a processar. Trata-se do caminho local para o ficheiro, tal como existe no nó. Quando o objeto ResourceFile em `UploadFileToContainerAsync` foi criado acima, o nome do ficheiro foi utilizado para esta propriedade (como um parâmetro para o construtor ResourceFile). Isto indica que o ficheiro pode ser encontrado no mesmo diretório que *TaskApplication.exe*.
2. O **segundo argumento** especifica que as *N* palavras do início devem ser escritas no ficheiro de saída. No exemplo, isto está hard-coded para que as três palavras do início sejam escritas no ficheiro de saída.
3. O **terceiro argumento** corresponde à assinatura de acesso partilhado (SAS) que fornece acesso de escrita ao contentor de **saída** no Armazenamento do Azure. *TaskApplication.exe* utiliza este URL de assinatura de acesso partilhado quando carrega o ficheiro de saída para o Armazenamento do Azure. Pode encontrar o código correspondente no método `UploadFileToContainer` no ficheiro `Program.cs` do projeto TaskApplication:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Passo 6: monitorizar tarefas
![Monitorizar tarefas][6]<br/>
*A aplicação cliente (1) monitoriza as tarefas de estado de conclusão e êxito, e (2) as tarefas carregam os dados dos resultados para o Armazenamento do Azure*

Quando as tarefas são adicionadas a um trabalho, são colocadas automaticamente em fila de espera e agendadas para execução em nós de computação dentro do conjunto associado ao trabalho. Com base nas definições especificadas, o Batch processa todas as tarefas de colocação em fila, agendamento, repetições e outras funções de administração de tarefas por si.

Existem várias abordagens à monitorização da execução de tarefas. O DotNetTutorial mostra um exemplo simples que comunica apenas estados de conclusão e de falha ou êxito das tarefas. No método `MonitorTasks` no ficheiro `Program.cs` do DotNetTutorial, existem três conceitos de Batch .NET que garantem o debate. Estes conceitos são indicados abaixo, por ordem de apresentação:

1. **ODATADetailLevel**: especificar [ODATADetailLevel][net_odatadetaillevel] em operações de lista (tais como obter uma lista das tarefas de um trabalho) é essencial para garantir o desempenho da aplicação do Batch. Adicione [Consultar o serviço Azure Batch de forma eficiente](batch-efficient-list-queries.md) à sua lista de leitura se planear fazer algum tipo de monitorização de estado nas suas aplicações do Batch.
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] fornece às aplicações do Batch .NET utilitários de ajuda para monitorização dos estados de tarefas. Em `MonitorTasks`, o *DotNetTutorial* espera até que todas as tarefas fiquem com o estado [TaskState.Completed][net_taskstate] num limite de tempo. Em seguida, termina o trabalho.
3. **TerminateJobAsync**: terminar uma tarefa com [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (ou o JobOperations.TerminateJob de bloqueio) marca essa tarefa como concluída. É essencial fazê-lo se a sua solução do Batch utilizar uma [JobReleaseTask][net_jobreltask]. Este é um tipo especial de tarefa, que é descrito em [Tarefas de preparação e de conclusão de trabalho](batch-job-prep-release.md).

O método `MonitorTasks` do ficheiro `Program.cs` do *DotNetTutorial* aparece abaixo:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Passo 7: transferir o resultado da tarefa
![Transferir o resultado da tarefa do Armazenamento][7]<br/>

Agora que o trabalho está concluído, o resultado das tarefas pode ser transferido do Armazenamento do Azure. Isto é feito com uma chamada para `DownloadBlobsFromContainerAsync` no ficheiro `Program.cs` do *DotNetTutorial*:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> A chamada para `DownloadBlobsFromContainerAsync` na aplicação *DotNetTutorial* especifica que os ficheiros devem ser transferidos para a pasta `%TEMP%`. Pode modificar esta localização de saída.
>
>

## <a name="step-8-delete-containers"></a>Passo 8: eliminar contentores
Uma vez que os dados que residem no Armazenamento do Azure lhe são cobrados, é sempre boa ideia remover blobs que já não são necessários para as tarefas do Batch. No ficheiro `Program.cs` do DotNetTutorial, esta ação é feita com três chamadas para o método de ajuda `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

O método propriamente dito apenas obtém uma referência ao contentor e, em seguida, chama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passo 9: eliminar o trabalho e o conjunto
No último passo, é-lhe pedido que elimine o trabalho e o conjunto que foram criados pela aplicação DotNetTutorial. Apesar de os próprios trabalhos e tarefas não lhe serem cobrados, os nós de computação *são* cobrados. Assim, recomendamos que atribua nós apenas conforme necessário. A eliminação de conjuntos não utilizados pode fazer parte do processo de manutenção.

O [JobOperations][net_joboperations] e o [PoolOperations][net_pooloperations] do BatchClient têm métodos de eliminação correspondentes, que são chamados se confirmar a eliminação:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Tenha em atenção que os recursos de computação são-lhe cobrados — a eliminação dos conjuntos não utilizados irá minimizar os custos. Além disso, tenha em atenção que eliminar um conjunto elimina todos os nós de computação nesse conjunto, e que quaisquer dados nos nós serão irrecuperáveis depois de o conjunto ser eliminado.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Executar o exemplo *DotNetTutorial*
Quando executa o exemplo de aplicação, o resultado da consola será semelhante ao seguinte. Durante a execução, irá ocorrer uma pausa em `Awaiting task completion, timeout in 00:30:00...` enquanto os nós de computação do conjunto são iniciados. Utilize o [portal do Azure][azure_portal] para monitorizar o conjunto, nós de computação, o trabalho e as tarefas durante e após a execução. Utilize o [portal do Azure][azure_portal] ou o [Explorador de Armazenamento do Azure][storage_explorers] para ver os recursos de Armazenamento (contentores e blobs) que são criados pela aplicação.

O tempo de execução normal é **aproximadamente 5 minutos** quando executa a aplicação na configuração predefinida.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Passos seguintes
Pode fazer alterações a *DotNetTutorial* e a *TaskApplication* para testar cenários de computação diferentes. Por exemplo, experimente adicionar um atraso de execução a *TaskApplication*, tal como com [Thread.Sleep][net_thread_sleep], para simular tarefas demoradas e monitorizá-las no portal. Experimente adicionar mais tarefas ou ajustar o número de nós de computação. Adicione lógica para procurar e permita a utilização de um conjunto existente para acelerar o tempo de execução (*sugestão*: dê saída de `ArticleHelpers.cs` no projeto [Microsoft.Azure.Batch.Samples.Common][github_samples_common] em [azure-batch-samples][github_samples]).

Agora que está familiarizado com o fluxo de trabalho básico de uma solução do Batch, está na altura de aprofundar as funcionalidades adicionais do serviço Batch.

* Reveja o artigo [Descrição geral das funcionalidades do Azure Batch](batch-api-basics.md), que recomendamos se não estiver familiarizado com o serviço.
* Comece pelos outros artigos de desenvolvimento do Batch em **Desenvolvimento aprofundado** no [percurso de aprendizagem do Batch][batch_learning_path].
* Veja uma implementação diferente de processamento da carga de trabalho "N palavras principais" através do Batch no exemplo [TopNWords][github_topnwords].
* Reveja as [notas de versão](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) do .NET do Batch para obter as alterações mais recentes da biblioteca.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Criar contentores no Armazenamento do Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Carregar a aplicação de tarefa e os ficheiros de entrada (dados) para contentores"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Criar um conjunto do Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Criar um trabalho do Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorizar tarefas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Transferir o resultado da tarefa do Armazenamento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Fluxo de trabalho da solução Batch (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciais do Batch no Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciais do Armazenamento no Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Fluxo de trabalho da solução Batch (diagrama mínimo)"
