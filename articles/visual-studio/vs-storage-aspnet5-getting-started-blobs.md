---
title: "Introdução ao blob storage e o Visual Studio ligado serviços (ASP.NET Core) | Microsoft Docs"
description: "Como começar a utilizar o Blob storage do Azure num projeto Visual Studio ASP.NET Core depois de criar uma conta de armazenamento com o Visual Studio ligada a serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: afd73bd0fd041a53fbe31aa3a5c23b3e27d7a9ec
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao Blob do Azure Visual Studio e de armazenamento ligado serviços (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Este artigo descreve como começar a utilizar o Blob storage do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto ASP.NET Core utilizando o Visual Studio **serviços ligados** funcionalidade. O **serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto. (Consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Storage do Azure.)

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Um blob único pode ser qualquer dimensão. Os BLOBs podem ser coisas como imagens, ficheiros de áudio e vídeos, dados não processados e ficheiros do documento. Este artigo descreve como começar com o blob storage depois de criar uma conta de armazenamento do Azure utilizando o Visual Studio **serviços ligados** num projeto ASP.NET Core.

Tal como em direto de ficheiros em pastas, blobs de armazenamento em direto nos contentores. Depois de criar um blob, crie um ou mais contentores nesse blob. Por exemplo, num blob chamado "Scrapbook", pode criar contentores chamados "imagens" para armazenar imagens e outra denominada "áudio" para armazenar ficheiros de áudio. Depois de criar os contentores, pode carregar ficheiros individuais aos mesmos. Consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para obter mais informações sobre manipular programaticamente os blobs.

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo parte do princípio de métodos assíncronos estão a ser utilizados. Consulte [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-blob-containers-in-code"></a>Contentores de BLOBs de acesso no código

Através de programação para aceder a blobs em projetos de ASP.NET Core, tem de adicionar o código seguinte, se ainda não estiver presente:

1. Adicionar o necessário `using` instruções:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Obter um `CloudStorageAccount` objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Utilize um `CloudBlobClient` objeto para obter um `CloudBlobContainer` referência a um contentor existente na sua conta de armazenamento:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Criar um contentor no código

Também pode utilizar o `CloudBlobClient` para criar um contentor na sua conta de armazenamento ao chamar `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Para disponibilizar os ficheiros dentro do contentor para todos os utilizadores, defina o contentor como público:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Para carregar um ficheiro do blob para um contentor, obtenha uma referência de contentor e utilizá-la para obter uma referência de blob. Em seguida, carregar qualquer fluxo de dados para essa referência ao chamar o `UploadFromStreamAsync` método. Esta operação cria o blob caso ainda não esteja aí e substitui um blob existente. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Para listar os blobs num contentor, get primeiro uma referência de contentor, em seguida, chame o `ListBlobsSegmentedAsync` método para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um `IListBlobItem`, converta-o para um `CloudBlockBlob`, `CloudPageBlob`, ou `CloudBlobDirectory` objeto. Se não souber o tipo de blob, utilize uma verificação de tipo para determinar que este deve ser transmitido.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) para outras formas listar o conteúdo de um contentor de blob.

## <a name="download-a-blob"></a>Transferir um blob

Para transferir um blob; get primeiro uma referência para o blob, em seguida, chame o `DownloadToStreamAsync` método. O exemplo seguinte utiliza o `DownloadToStreamAsync` método para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode guardar como um ficheiro local.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) para outras formas de guardar os blobs como ficheiros.

## <a name="delete-a-blob"></a>Eliminar um blob

Para eliminar um blob; get primeiro uma referência para o blob, em seguida, chame o `DeleteAsync` método:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
