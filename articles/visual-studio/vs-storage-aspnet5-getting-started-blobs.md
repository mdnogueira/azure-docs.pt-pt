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
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 2e8060b44c395ad7c24e7778c0ef65148a3a45de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao Blob do Azure Visual Studio e de armazenamento ligado serviços (ASP.NET Core)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o Blob storage do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto ASP.NET Core, utilizando a caixa de diálogo do Visual Studio adicionar ligado serviços.

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Um blob único pode ser qualquer dimensão. Os BLOBs podem ser coisas como imagens, ficheiros de áudio e vídeos, dados não processados e ficheiros do documento. Este artigo descreve como começar com o blob storage depois de criar uma conta de armazenamento do Azure utilizando o Visual Studio **adicionar serviços ligados** diálogo num projeto ASP.NET Core.

Tal como em direto de ficheiros em pastas, blobs de armazenamento em direto nos contentores. Depois de ter criado um armazenamento, crie um ou mais contentores no armazenamento. Por exemplo, um armazenamento chamado "Scrapbook", pode criar contentores no armazenamento chamado "imagens" para armazenar imagens e outra denominada "áudio" para armazenar ficheiros de áudio. Depois de criar os contentores, pode carregar ficheiros BLOBs individuais aos mesmos. Consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para obter mais informações sobre manipular programaticamente os blobs.

## <a name="access-blob-containers-in-code"></a>Contentores de BLOBs de acesso no código
Através de programação para aceder a blobs em projetos de ASP.NET Core, tem de adicionar os seguintes itens, se não estiverem já presentes.

1. Adicione as seguintes declarações de espaço de nomes de código na parte superior de qualquer ficheiro c# no qual pretende aceder programaticamente ao storage do Azure.
   
        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;
2. Obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
            "<storage-account-name>",
            "<access-key>"), true);
   
    **Nota:** utilizar todo o código acima à frente o código de nas secções seguintes.
3. Utilize um **CloudBlobClient** objeto para obter um **CloudBlobContainer** referência a um contentor existente na sua conta de armazenamento.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

## <a name="create-a-container-in-code"></a>Criar um contentor no código
Também pode utilizar o **CloudBlobClient** para criar um contentor na sua conta de armazenamento. Tudo o que precisa fazer consiste em Adicionar uma chamada para **CreateIfNotExistsAsync** como no seguinte código:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "my-new-container."
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


**Nota:** as APIs que efetuar chamadas para o armazenamento do Azure no ASP.NET Core são assíncronas. Consulte [programação assíncrona com Async-Await](http://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código abaixo parte do princípio de métodos de programação assíncrona estão a ser utilizados.

Para disponibilizar os ficheiros dentro do contentor para todos os utilizadores, pode definir o contentor como público utilizando o seguinte código.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Para carregar um ficheiro do blob para um contentor, obtenha uma referência de contentor e utilizá-la para obter uma referência de blob. Depois de ter uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o **UploadFromStreamAsync** método. Esta operação cria o blob caso não ainda esteja aí ou substitui-lo se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode chamar o contentor **ListBlobsSegmentedAsync** método para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um **IListBlobItem** devolvido, tem de o transmitir para um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se não souber o tipo de blob, pode utilizar uma verificação de tipo para determinar qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar o URI de cada item no contentor.

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

Existem outras formas para listar o conteúdo de um contentor de blob. Consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) para obter mais informações.

## <a name="download-a-blob"></a>Transferir um blob
Para transferir um blob, obtenha primeiro uma referência para o blob e, em seguida, chame o **DownloadToStreamAsync** método. O exemplo seguinte utiliza o **DownloadToStreamAsync** método para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode guardar como um ficheiro local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named "myfile".
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Existem outras formas de guardar os blobs como ficheiros. Consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) para obter mais informações.

## <a name="delete-a-blob"></a>Eliminar um blob
Para eliminar um blob, obtenha primeiro uma referência para o blob e, em seguida, chame o **DeleteAsync** método.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

