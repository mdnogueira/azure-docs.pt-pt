---
title: "Introdução ao Armazenamento de Blobs do Azure (armazenamento de objetos) através do .NET | Microsoft Docs"
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: tamram
ms.openlocfilehash: 87594d2688e3cd01f5e7db8f5be8ca513969e774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-using-net"></a>Introdução ao Blob Storage do Azure através do .NET

[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

O Blob Storage do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos.

### <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial demonstra como escrever código .NET para alguns cenários comuns utilizando o Blob Storage do Azure. Os cenários abrangidos incluem carregar, listar, transferir e eliminar blobs.

**Pré-requisitos:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [Biblioteca de Clientes do Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Gestor de Configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Uma [Conta do Storage do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais exemplos
Para obter exemplos adicionais que utilizam o Armazenamento de Blobs, veja [Introdução ao Armazenamento de Blobs do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Pode transferir a aplicação de exemplo e executá-la ou procurar o código no GitHub.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Adicionar com diretivas
Adicione as seguintes diretivas de **utilização** à parte superior do ficheiro `Program.cs`:

```csharp
using Microsoft.WindowsAzure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Criar o cliente do serviço Blob
A classe **CloudBlobClient** permite obter contentores e blobs armazenados no Blob Storage. Eis uma forma de criar o cliente do serviço:

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
Agora, está pronto para escrever código que lê dados de e escreve dados para o Blob Storage.

## <a name="create-a-container"></a>Criar um contentor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contentor se ainda não existir:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

Por predefinição, o novo contentor é privado, o que significa que tem de especificar a chave de acesso ao armazenamento para transferir blobs deste contentor. Se pretender disponibilizar os ficheiros dentro do contentor para todos os utilizadores, pode definir o contentor como público utilizando o seguinte código:

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

Qualquer pessoa na Internet pode ver os blobs num contentor público. No entanto, só quem tem a chave de acesso da conta adequada ou uma assinatura de acesso partilhado os pode modificar ou eliminar.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O Blob Storage do Azure suporta blobs de blocos e blobs de páginas.  Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Quando tiver uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o método **UploadFromStream**. Esta operação cria o blob caso não exista, ou substitui se existir.

O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método **ListBlobs** do contentor para obter os blobs e/ou os diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um **IListBlobItem** devolvido, tem de o transmitir para um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se o tipo for desconhecido, pode utilizar uma verificação de tipo para determinar para qual este deve ser transmitido. O código seguinte demonstra como obter e apresentar o URI de cada item no contentor _photos_:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

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
```

Ao incluir informações do caminho nos nomes de blobs, pode criar uma estrutura de diretório virtual que pode organizar e percorrer tal como faria com um sistema de ficheiros tradicional. A estrutura de diretório é apenas virtual – os únicos recursos disponíveis no Armazenamento de blobs são contentores e blobs. No entanto, a biblioteca de clientes do Storage oferece um objeto **CloudBlobDirectory** para fazer referência a um diretório virtual e simplificar o processo de trabalhar com blobs que se encontram organizados desta forma.

Por exemplo, considere o seguinte conjunto de blobs de blocos num contentor com o nome *photos*:

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Quando chama **ListBlobs** no contentor *photos* (tal como no fragmento de código anterior), é devolvida uma lista hierárquica. Esta contém os objetos **CloudBlobDirectory** e **CloudBlockBlob**, que representam os diretórios e os blobs no contentor, respetivamente. A saída resultante tem um aspeto semelhante a este:

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Opcionalmente, pode definir o parâmetro **UseFlatBlobListing** do método**ListBlobs** como **true**. Neste caso, todos os blobs no contentor são devolvidos como um objeto **CloudBlockBlob** A chamada para **ListBlobs** para devolver uma lista não hierárquica tem o seguinte aspeto:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

e os resultados têm o seguinte aspeto:

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>Transferir blobs
Para transferir blobs, obtenha primeiro uma referência de blob e, em seguida, chame o método **DownloadToStream**. O exemplo seguinte utiliza o método **DownloadToStream** para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode manter num ficheiro local.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Também pode utilizar o método **DownloadToStream** para transferir os conteúdos de um blob como uma cadeia de texto.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Eliminar blobs
Para eliminar um blob, obtenha primeiro uma referência de blob e, em seguida, chame o método **Delete**.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>Listar blobs em páginas de forma assíncrona
Se está a listar um grande número de blobs ou pretende controlar o número de resultados devolvido numa operação de listagem, pode listar os blobs em páginas de resultados. Este exemplo mostra como devolver resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera por devolver um grande conjunto de resultados.

Este exemplo mostra uma lista de blobs não hierárquica, mas também pode criar uma lista hierárquica ao definir o parâmetro _useFlatBlobListing_ do método **ListBlobsSegmentedAsync** como _falso_.

Uma vez que o método de exemplo chama um método assíncrono, tem de ser precedido pela palavra-chave _async_ e tem de devolver um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de exemplo até que a tarefa de listagem seja concluída.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
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
```

## <a name="writing-to-an-append-blob"></a>Escrever num blob de acréscimo
Um blob de acréscimo está otimizado para operações de acréscimo, como o registo. Tal como um blob de blocos, um blob de acréscimo é composto por blocos. Porém, quando adiciona um novo bloco a um blob de acréscimo, este é sempre acrescentado ao fim do blob. Não é possível atualizar ou eliminar um bloco existente num blob de acréscimo. O IDs de bloco de um blob de acréscimo não são expostos como acontece num blob de blocos.

Cada bloco num blob de acréscimo pode ter um tamanho diferente, até um máximo de 4 MB, e um blob de acréscimo pode incluir um máximo de 50 000 blocos. Por conseguinte, o tamanho máximo de um blob de acréscimo é ligeiramente superior a 195 GB (4 MB X 50 000 blocos).

O exemplo abaixo cria um novo blob de acréscimo e acrescenta alguns dados ao mesmo, simulando uma operação de registo simples.

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

Consulte [Compreender os Blobs de Blocos, os Blobs de Páginas e os Blobs de Acréscimo](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) para obter mais informações sobre as diferenças entre os três tipos de blobs.

## <a name="managing-security-for-blobs"></a>Gerir a segurança dos blobs
Por predefinição, o Storage do Azure mantém os seus dados seguros ao limitar o acesso ao proprietário da conta, que está na posse das chaves de acesso da conta. Quando precisar de partilhar dados de blobs na sua conta do Storage, é importante que o faça sem comprometer a segurança das suas chaves de acesso da conta. Além disso, pode encriptar os dados dos blobs para garantir que estes estão seguros durante a transmissão e no Storage do Azure.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controlar o acesso aos dados de blobs
Por predefinição, os dados de blobs na sua conta do Storage estão acessíveis apenas para o proprietário da conta do Storage. A autenticação de pedidos para o Blob Storage requer a chave de acesso da conta por predefinição. No entanto, é possível que pretenda disponibilizar certos dados de blobs a outros utilizadores. Tem duas opções:

* **Acesso anónimo:** pode tornar um contentor ou os respetivos blobs publicamente disponíveis para acesso anónimo. Consulte [Gerir o acesso de leitura anónimo a contentores e blobs](storage-manage-access-to-resources.md) para obter mais informações.
* **Assinaturas de acesso partilhado:** pode fornecer aos clientes uma assinatura de acesso partilhado (SAS), que concede acesso delegado a um recurso na sua conta do Storage, com as permissões que especificar e durante um intervalo que definir. Veja [Utilizar Assinaturas de Acesso Partilhado (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações.

### <a name="encrypting-blob-data"></a>Encriptar dados de blobs
O Storage do Azure suporta a encriptação de dados de blobs do cliente e no servidor:

* **Encriptação do lado do cliente:** a Biblioteca de Clientes do Storage para o .NET suporta a encriptação de dados dentro de aplicações de cliente antes do carregamento para o Storage do Azure e a desencriptação de dados durante a transferência para o cliente. A biblioteca também suporta a integração com o Cofre de Chaves do Azure para a gestão de chaves da conta do Storage. Consulte [Encriptação do Lado do Cliente com .NET para o Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações. Consulte também [Tutorial: encriptar e desencriptar blobs no Armazenamento do Microsoft Azure com o Cofre de Chaves do Azure](storage-encrypt-decrypt-blobs-key-vault.md).
* **Encriptação do lado do servidor**: agora, o Storage do Azure suporta a encriptação do lado do servidor. Consulte [Encriptação do Serviço do Storage do Azure para Dados Inativos (Pré-visualização)](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do Blob Storage, siga estas ligações para saber mais.

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure
* O [Explorador de Armazenamento do Microsoft Azure (MASE)](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

### <a name="blob-storage-samples"></a>Exemplos de Armazenamento de Blobs
* [Introdução ao Armazenamento de Blobs do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referência do Blob Storage
* [Referência da Biblioteca de Clientes do Storage para o .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [Referência da API REST](/rest/api/storageservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>Guias conceptuais
* [Transferir dados com o utilitário de linha de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Introdução ao Armazenamento de ficheiros do .NET](../files/storage-dotnet-how-to-use-files.md)
* [Como utilizar o armazenamento de blobs do Azure com o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki)
