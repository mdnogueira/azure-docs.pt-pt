---
title: "Introdução ao blob storage e o Visual Studio ligado services (Serviços de cloud) | Microsoft Docs"
description: "Como começar a utilizar o Blob storage do Azure num projeto de serviço em nuvem no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligada a serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: cf14880c70f90b01c5dffbfe434150581c2ec33b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao Blob Storage do Azure e o Visual Studio ligado serviços (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar com o Blob Storage do Azure após a criação ou referenciada uma conta de armazenamento do Azure utilizando o Visual Studio **adicionar serviços ligados** projeto dos serviços de caixa de diálogo numa nuvem Visual Studio. Vamos mostrar-lhe como aceder e criar contentores de BLOBs e como realizar tarefas comuns, como carregar, listar e transferir os blobs. Os exemplos são escritos no C\# e utilizar o [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Um blob único pode ser qualquer dimensão. Os BLOBs podem ser coisas como imagens, ficheiros de áudio e vídeos, dados não processados e ficheiros do documento.

Tal como em direto de ficheiros em pastas, blobs de armazenamento em direto nos contentores. Depois de ter criado um armazenamento, crie um ou mais contentores no armazenamento. Por exemplo, um armazenamento chamado "Scrapbook", pode criar contentores no armazenamento chamado "imagens" para armazenar imagens e outra denominada "áudio" para armazenar ficheiros de áudio. Depois de criar os contentores, pode carregar ficheiros BLOBs individuais aos mesmos.

* Para obter mais informações sobre manipular programaticamente os blobs, consulte [introdução ao Blob storage do Azure através do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Para obter informações gerais sobre o Storage do Azure, consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/).
* Para obter informações gerais sobre os Cloud Services do Azure, consulte [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/).
* Para obter mais informações sobre a programação de aplicações ASP.NET, consulte [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Contentores de BLOBs de acesso no código
Através de programação para aceder a blobs em projetos de serviço em nuvem, tem de adicionar os seguintes itens, se não estiverem já presentes.

1. Adicione as seguintes declarações de espaço de nomes de código na parte superior de qualquer ficheiro c# no qual pretende aceder programaticamente ao Storage do Azure.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Obter um **CloudBlobClient** objeto para fazer referência a um contentor existente na sua conta de armazenamento.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Obter um **CloudBlobContainer** objeto para fazer referência a um contentor de blob específico.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Utilize todo o código mostrado no procedimento anterior à frente do código mostrado nas seguintes secções de.
> 
> 

## <a name="create-a-container-in-code"></a>Criar um contentor no código
> [!NOTE]
> Algumas APIs que efetuar chamadas enviados para o Storage do Azure no ASP.NET são assíncronas. Consulte [programação assíncrona com Async-Await](http://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código no exemplo seguinte parte do princípio de que está a utilizar métodos assíncronos de programação.
> 
> 

Para criar um contentor na sua conta de armazenamento, tudo o que precisa de fazer é adicionar uma chamada para **CreateIfNotExistsAsync** como no seguinte código:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Para disponibilizar os ficheiros dentro do contentor para todos os utilizadores, pode definir o contentor como público utilizando o seguinte código.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Qualquer pessoa na Internet pode ver os blobs num contentor público, mas pode modificar ou eliminá-los apenas se tiver a chave de acesso adequados.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Storage do Azure suporta blobs de blocos e blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Quando tiver uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o método **UploadFromStream**. Esta operação cria o blob caso não exista, ou substitui se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método **ListBlobs** do contentor para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um **IListBlobItem**, tem de o transmitir para um **CloudBlockBlob**, **CloudPageBlob**, ou  **CloudBlobDirectory** objeto. Se o tipo for desconhecido, pode utilizar uma verificação de tipo para determinar para qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar o URI de cada item no contentor **photos**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

Conforme mostrado no exemplo de código anterior, o serviço blob tem o conceito de diretórios contentores, bem. Isto é, de modo a que pode organizar os blobs numa estrutura mais semelhantes a pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos num contentor com o nome **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando chamar **ListBlobs** no contentor (tal como no exemplo anterior), contém a coleção devolvida **CloudBlobDirectory** e **CloudBlockBlob** objetos que representam os diretórios e os blobs contidos no nível superior. Segue-se a saída resultante:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método**ListBlobs** como **true**. Isto resulta em todos os BLOBs que está a ser devolvido como uma **CloudBlockBlob**, independentemente do diretório. Segue-se a chamada para **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e Eis os resultados:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Transferir blobs
Para transferir blobs, obtenha primeiro uma referência de blob e, em seguida, chame o método **DownloadToStream**. O exemplo seguinte utiliza o método **DownloadToStream** para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode manter num ficheiro local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Também pode utilizar o método **DownloadToStream** para transferir os conteúdos de um blob como uma cadeia de texto.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar blobs
Para eliminar um blob, obtenha primeiro uma referência de blob e, em seguida, chame o **eliminar** método.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs em páginas de forma assíncrona
Se está a listar um grande número de blobs ou pretende controlar o número de resultados devolvido numa operação de listagem, pode listar os blobs em páginas de resultados. Este exemplo mostra como devolver resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera por devolver um grande conjunto de resultados.

Este exemplo mostra uma lista de blobs não hierárquica, mas também pode criar uma lista hierárquica ao definir o parâmetro **useFlatBlobListing** do método **ListBlobsSegmentedAsync** como **falso**.

Uma vez que o método de exemplo chama um método assíncrono, tem de ser precedido pela palavra-chave **async** e tem de devolver um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de exemplo até que a tarefa de listagem seja concluída.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

