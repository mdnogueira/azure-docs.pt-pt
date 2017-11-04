---
title: "Transferência de dados com a biblioteca de movimento de dados de armazenamento do Microsoft Azure | Microsoft Docs"
description: "Utilize a biblioteca de movimento de dados para mover ou copiar dados de ou para conteúdo de blob e o ficheiro. Copiar dados para o armazenamento do Azure de ficheiros locais ou copie os dados dentro ou entre contas de armazenamento. Migre facilmente os dados para armazenamento do Azure."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.openlocfilehash: 7890159574de0db58dd2e7d1b6a19305381d29d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Transferência de dados com a biblioteca de movimento de dados de armazenamento do Microsoft Azure

## <a name="overview"></a>Descrição geral
A biblioteca de movimento de dados do Microsoft Azure Storage é uma biblioteca de open source para várias plataformas que foi concebida para carregamento, a transferência e a cópia de Blobs de armazenamento do Azure e os ficheiros de elevado desempenho. Esta biblioteca é a arquitetura de movimento de dados de núcleos que for ligado [AzCopy](../storage-use-azcopy.md). A biblioteca de movimento de dados fornece métodos convenientes que não estão disponíveis no nosso tradicional [biblioteca de clientes de armazenamento de Azure .NET](../blobs/storage-dotnet-how-to-use-blobs.md). Isto inclui a capacidade de definir o número de operações simultâneas, controlar o progresso da transferência, retome facilmente uma transferência cancelada e muito mais.  

Esta biblioteca também utiliza o .NET Core, o que significa que pode utilizá-lo ao criar aplicações de .NET para Windows, Linux e macOS. Para saber mais sobre .NET Core, consulte o [documentação do .NET Core](https://dotnet.github.io/). Esta biblioteca também funciona para tradicionais aplicações de .NET Framework para o Windows. 

Este documento demonstra como criar uma aplicação de consola .NET Core que é executado no Windows, Linux e macOS e efetua os seguintes cenários:

- Carregar ficheiros e diretórios para o Blob Storage.
- Defina o número de operações simultâneas ao transferir dados.
- Progresso de transferência de dados de controlar.
- Transferência de dados retomar cancelada. 
- Copie o ficheiro do URL para o Blob Storage. 
- Copie a partir do Blob Storage para o Blob Storage.

**O que precisa de:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Uma [Conta do Storage do Azure](storage-create-storage-account.md#create-a-storage-account)

> [!NOTE]
> Este guia pressupõe que já estiver familiarizado com [Storage do Azure](https://azure.microsoft.com/services/storage/). Se não, ler o [introdução ao Storage do Azure](storage-introduction.md) documentação é útil. Mais importante ainda, terá de [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para começar a utilizar a biblioteca de movimento de dados.
> 
> 

## <a name="setup"></a>Configurar  

1. Visite o [guia de instalação do .NET Core](https://www.microsoft.com/net/core) para instalar o .NET Core. Quando selecionar o seu ambiente, escolha a opção da linha de comandos. 
2. Na linha de comandos, crie um diretório para o seu projeto. Navegue para este diretório, em seguida, escreva `dotnet new console -o <sample-project-name>` para criar um projeto de consola c#.
3. Abra este diretório no Visual Studio Code. Este passo pode ser efetuado rapidamente através da linha de comandos, escrevendo `code .` no Windows.  
4. Instalar o [c# extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) do Visual Studio Code Marketplace. Reinicie o Visual Studio Code. 
5. Neste momento, deverá ver dois pedidos. Um é adicionar "recursos necessários para compilar e depuração". Clique em "Sim". Outro pedido refere-se para restaurar dependências não resolvidas. Clique em "restaurar".
6. Modificar `launch.json` em `.vscode` para utilizar o terminal externa como uma consola. Esta definição deve ler como` "console": "externalTerminal"`
7. Visual Studio Code permite-lhe depurar aplicações de .NET Core. Atingiu o `F5` para executar a sua aplicação e certifique-se de que a configuração está a funcionar. Deverá ver "Olá mundo!" indicadas para a consola. 

## <a name="add-data-movement-library-to-your-project"></a>Adicionar a biblioteca de movimento de dados ao seu projeto

1. Adicionar a versão mais recente da biblioteca de movimento de dados para o `dependencies` secção do seu `<project-name>.csproj` ficheiro. No momento da escrita, seria desta versão`"Microsoft.Azure.Storage.DataMovement": "0.6.2"` 
2. Deve apresentar uma linha de comandos para restaurar o seu projeto. Clique no botão "restauro". Também pode restaurar o projeto na linha de comandos, escrevendo o comando `dotnet restore` na raiz do diretório do seu projeto.

Modificar `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Configurar a estrutura da aplicação
A primeira coisa a que fazer é configurar o código de "estrutura" da nossa aplicação. Este código solicita-numa chave de conta e o nome da conta de armazenamento e utiliza essas credenciais para criar um `CloudStorageAccount` objeto. Este objeto é utilizado para interagir com a nossa conta de armazenamento em todos os cenários de transferência. O código solicita-nos para escolher o tipo de operação de transferência que gostaríamos de executar. 

Modificar `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");           
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");           
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        { 
            
        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        { 
            
        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="transfer-local-file-to-azure-blob"></a>Transferência de ficheiros local para Blob do Azure
Adicione os métodos `GetSourcePath` e `GetBlob` para `Program.cs`:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Modificar o `TransferLocalFileToAzureBlob` método:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{ 
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Este código pede-lhe-nos para o caminho para um ficheiro local, o nome de um contentor de novo ou existente e o nome de um blob de novo. O `TransferManager.UploadAsync` método efetua o carregamento utilizando estas informações. 

Atingiu o `F5` para executar a sua aplicação. Pode verificar que o carregamento ocorreu visualizando a sua conta do Storage com o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Número de conjunto de operações simultâneas
Uma funcionalidade excelente oferecida pela biblioteca de movimento de dados é a capacidade de definir o número de operações simultâneas para aumentar o débito de transferência de dados. Por predefinição, a biblioteca de movimento de dados define o número de operações simultâneas para 8 * o número de núcleos no seu computador. 

Tenha em atenção que muitas operações simultâneas num ambiente de largura de banda baixa podem sobrecarregar a ligação de rede e, na verdade, impedir operações totalmente conclusão. Terá de testar esta definição para determinar o que funciona melhor com base na sua largura de banda de rede disponível. 

Vamos adicionar algum código que lhe permite-nos definir o número de operações simultâneas. Vamos também adicionar código vezes quanto tempo demora para a transferência concluir.

Adicionar um `SetNumberOfParallelOperations` método `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modificar o `ExecuteChoice` método a utilizar `SetNumberOfParallelOperations`:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Modificar o `TransferLocalFileToAzureBlob` método a utilizar um temporizador:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{ 
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Progresso da transferência de controlar
É ótimo saber quanto tempo demorou relativamente aos nossos dados transferir. No entanto, a capacidade de ver o progresso da nossa transferência *durante* a operação de transferência seria ainda mais. Para alcançar este cenário, temos de criar um `TransferContext` objeto. O `TransferContext` objeto é fornecido em dois formatos: `SingleTransferContext` e `DirectoryTransferContext`. É a primeira para transferir um único ficheiro (que é que podemos estiver a fazer agora) e a última opção é para transferir um diretório de ficheiros (que iremos adicionar mais tarde).

Adicione os métodos `GetSingleTransferContext` e `GetDirectoryTransferContext` para `Program.cs`: 

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });
    
    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });
    
    return context;
}
```

Modificar o `TransferLocalFileToAzureBlob` método a utilizar `GetSingleTransferContext`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{ 
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Retomar uma transferência cancelada
Outra funcionalidade conveniente oferecida pela biblioteca de movimento de dados é a capacidade para retomar uma transferência cancelada. Vamos adicionar algum código que lhe permite-nos Cancelar temporariamente a transferência, escrevendo `c`e, em seguida, retome a transferência 3 segundos mais tarde.

Modificar `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{ 
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account); 
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint); 
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);  
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Até agora, nosso `checkpoint` valor sempre foi definido para `null`. Agora, se a transferência é cancelado, vamos obter o último ponto de verificação do nosso de transferência, então, utilizar este novo ponto de verificação no nosso contexto de transferência. 

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Transferência de diretório local para o diretório de Blob do Azure
Seria disappointing se a biblioteca de movimento de dados só foi possível transferir um ficheiro de cada vez. Luckily, não for este o caso. A biblioteca de movimento de dados fornece a capacidade para transferir um diretório de ficheiros e todos os seus subdiretórios. Vamos adicionar algum código que lhe permite-na fazem isso mesmo.

Em primeiro lugar, adicione o método `GetBlobDirectory` para `Program.cs`:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Em seguida, modificar `TransferLocalDirectoryToAzureBlobDirectory`:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{ 
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account); 
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint); 
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);  
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Existem algumas diferenças entre este método e o método de carregamento de um único ficheiro. Estamos a utilizar agora `TransferManager.UploadDirectoryAsync` e `getDirectoryTransferContext` método criámos anteriormente. Além disso, atualmente, fornecemos uma `options` valor a nossa operação de carregamento, que permite-nos indicar que pretendemos incluir subdiretórios no nosso carregamento. 

## <a name="copy-file-from-url-to-azure-blob"></a>Copie o ficheiro do URL para Blob do Azure
Agora, vamos adicionar código que lhe permite-na copiar um ficheiro a partir de um URL para um Blob do Azure. 

Modificar `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account); 
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint); 
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);  
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Um caso de utilização importantes para esta funcionalidade é quando precisar de mover dados de outro serviço em nuvem (por exemplo, AWS) para o Azure. Desde que tenham um URL que lhe dá acesso ao recurso, podem mover facilmente esse recurso em Blobs do Azure utilizando o `TransferManager.CopyAsync` método. Este método também introduz um novo parâmetro booleano. Definição este parâmetro `true` indica que queremos fazer uma cópia assíncrona do lado do servidor. Definição este parâmetro `false` indica uma cópia síncrona - o que significa que o recurso é primeiro transferido para os nosso computador local, em seguida, carregar para Blob do Azure. No entanto, copiar síncrona atualmente só está disponível para copiar a partir de um recurso de armazenamento do Azure para outro. 

## <a name="transfer-azure-blob-to-azure-blob"></a>Transferir BLOBs do Azure para BLOBs do Azure
Outra funcionalidade exclusivamente é fornecida pela biblioteca de movimento de dados é a capacidade de copiar a partir de um recurso de armazenamento do Azure para outro. 

Modificar `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account); 
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint); 
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);  
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Neste exemplo, iremos definir o parâmetro booleano `TransferManager.CopyAsync` para `false` para indicar que queremos fazer uma cópia síncrona. Isto significa que o recurso é primeiro transferido para os nosso computador local, em seguida, carregar para Blob do Azure. A opção de cópia síncrona é uma excelente forma de garantir que a operação de cópia tem uma velocidade consistente. Em contrapartida, a velocidade de uma cópia assíncrona do lado do servidor está dependente da largura de banda de rede disponível no servidor, que pode variam. No entanto, síncrona cópia poderá gerar o custo de saída adicionais em comparação comparado a cópia assíncrona. A abordagem recomendada é utilizar a cópia síncrona numa VM do Azure que se encontra na mesma região que a sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="conclusion"></a>Conclusão
A nossa aplicação de movimento de dados está agora concluída. [O exemplo de código completa está disponível no GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app). 

## <a name="next-steps"></a>Passos seguintes
Nesta Introdução, criámos uma aplicação que interage com o Storage do Azure e é executado no Windows, Linux e macOS. Esta introdução focado no armazenamento de Blobs. No entanto, este mesmo conhecimento pode ser aplicado ao armazenamento de ficheiros. Para obter mais informações, consulte [documentação de referência da biblioteca de movimento de dados de armazenamento do Azure](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]




