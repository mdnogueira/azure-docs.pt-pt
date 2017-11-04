---
title: "Guia de introdução do Azure - objetos de transferência da Blob storage do Azure através do .NET | Microsoft Docs"
description: "Saiba rapidamente a transferência de objetos do Blob storage do Azure através do .NET"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.openlocfilehash: fdba4588fbb2c46efb3fc4de1a9e53414264444a
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Transferência de objetos para/de Blob storage do Azure através do .NET

Este guia de introdução, irá aprender a utilizar o C# .NET para carregar, transfira e blobs de blocos de lista num contentor no Blob storage do Azure no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) com a carga de trabalho seguinte:
    - **Desenvolvimento do Azure**

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento utilizando o portal do Azure

Em primeiro lugar, crie uma nova conta de armazenamento para fins gerais para utilizar este guia de introdução. 

1. Vá para o [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure. 
2. No Hub menu, selecione **novo** > **armazenamento** > **conta de armazenamento - BLOBs, ficheiro, tabela, fila**. 
3. Introduza um nome para a conta do Storage. O nome tem de ter entre 3 e 24 carateres de comprimento e conter apenas letras minúsculas e números. Também tem de ser exclusivo.
4. Definir `Deployment model` para **do Resource manager**.
5. Definir `Account kind` para **fins gerais**.
6. Definir `Performance` para **padrão**. 
7. Definir `Replication` para **armazenamento localmente redundante (LRS)**.
8. Definir `Storage service encryption` para **desativado**.
9. Definir `Secure transfer required` para **desativado**.
10. Selecione a sua subscrição. 
11. Para `resource group`, crie um novo e atribua um nome exclusivo. 
12. Selecione o `Location` a utilizar para a sua conta de armazenamento.
13. Verifique **afixar ao dashboard** e clique em **criar** para criar a conta de armazenamento. 

Depois de criar a sua conta do storage, está afixada ao dashboard. Clique na mesma para abri-lo. Em definições, clique em **chaves de acesso**. Selecione uma chave e copie a cadeia de ligação para a área de transferência e, em seguida, cole-o num editor de texto para utilização posterior.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação de exemplo utilizada neste início rápido é uma aplicação de consola básico. 

Utilize [git](https://git-scm.com/) para transferir uma cópia da aplicação para o ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clones o repositório para a pasta de local git. Para abrir a solução do Visual Studio, procure a pasta de armazenamento-blobs-dotnet-guia de introdução, abra-o e faça duplo clique no armazenamento de blobs-dotnet quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Na aplicação, tem de fornecer a cadeia de ligação para a sua conta de armazenamento. Abra o `app.config` ficheiro a partir do Explorador de soluções no Visual Studio. Localizar o `StorageConnectionString` entrada. Para **valor**, substitua o valor da cadeia de ligação completo pelo guardou no portal do Azure. O `storageConnectionString` deve ter um aspeto semelhante ao seguinte:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=<NameHere>;
    AccountKey=<KeyHere>" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Executar o exemplo

Este exemplo cria um ficheiro de teste nos meus documentos, esta carrega-o para o Blob storage, apresenta uma lista de blobs no contentor, em seguida, transfere o ficheiro com um novo nome, pelo que pode comparar os antigos e novos ficheiros. 

Execute o exemplo ao premir F5. Mostra a saída numa janela de consola que é semelhante ao seguinte: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

Quando prima qualquer tecla para continuar, elimina o contentor de armazenamento e os ficheiros. Antes de continuar, verifique MyDocuments para os dois ficheiros. Pode abrir e ver que se são idênticos. Copie o URL para o blob fora da janela de consola e cole-o num browser para ver os conteúdos do ficheiro no armazenamento de Blobs.

Também pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver os ficheiros no Blob storage. Explorador de armazenamento do Azure é uma ferramenta de plataforma livre que lhe permite aceder às suas informações de conta de armazenamento. 

Depois de verificar que os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que faz o exemplo, abra o ficheiro Program.cs para ver o código. 

## <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

A primeira coisa a fazer é criar as referências a objectos utilizados para aceder e gerir o armazenamento de Blobs. Criar estes objetos entre si – cada um é utilizada pela seguinte na lista.

* Criar uma instância do **CloudStorageAccount** objeto apontar para a conta de armazenamento. 

* Criar uma instância do **CloudBlobClient** objeto, que aponta para o serviço Blob na sua conta de armazenamento. 

* Criar uma instância do **CloudBlobContainer** objeto que representa o contentor que está a aceder. Contentores são utilizados para organizar os blobs como utilizar as pastas no seu computador para organizar os seus ficheiros.

Assim que tiver o **CloudBlobContainer**, pode criar uma instância do **CloudBlockBlob** objeto que aponta para o blob específico no qual está interessado e efetuar um carregamento, transferência, copiar, etc. operação.

Nesta secção, criar uma instância dos objetos, criar um novo contentor e, em seguida, definir as permissões no contentor para que os blobs são públicos e podem ser acedidos com apenas um URL. O contentor é designado por **quickstartblobs**. 

Este exemplo utiliza **CreateIfNotExists** uma vez que queremos criar um novo contentor sempre que a amostra é executada. Num ambiente de produção onde utiliza o mesmo contentor ao longo de uma aplicação, é melhor prática só é possível chamar **CreateIfNotExists** depois. Em alternativa, para criar o contentor de antecedência, pelo que não tem de criá-la no código.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>Carregar os blobs no contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados mais frequentemente e que é o que é utilizado neste guia de introdução. 

Para carregar um ficheiro para um blob, obtenha uma referência para o blob no contentor de destino. Assim que tiver a referência de blob, pode carregar dados para a mesma utilizando [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Esta operação cria o blob caso ainda não existir ou substitui-lo se já existir.

O código de exemplo cria um ficheiro local para ser utilizado para o carregamento e transferência, armazenar o ficheiro a carregar como **fileAndPath** e o nome do blob no **localFileName**. O exemplo seguinte carrega o ficheiro para o contentor denominado **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Existem vários métodos de carregamento que pode utilizar com o Blob storage. Por exemplo, se tiver uma sequência de memória, pode utilizar o método UploadFromStreamAsync em vez do UploadFromFileAsync. 

Os blobs de blocos podem ser qualquer tipo de ficheiro binário ou de texto. Os blobs de páginas são utilizados principalmente para os ficheiros VHD utilizados para fazer uma cópia de VMs de IaaS. Acrescentar blobs são utilizados para registo, por exemplo, se pretender escrever um ficheiro e, em seguida, mantenha a adição de mais informações. A maioria dos objetos armazenados no Blob storage são blobs de blocos.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de ficheiros no contentor com [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). O código seguinte obtém a lista de blobs, em seguida, repetido ao longo, que mostra os URIs dos blobs encontrados. Pode copiar o URI a partir da janela de comando e cole-o num browser para ver o ficheiro.

Se tiver de 5000 ou menos blobs no contentor, todos os nomes dos BLOBs são obtidos numa chamada para ListBlobsSegmentedAsync. Se tiver mais de 5.000 blobs no contentor, o serviço obtém a lista de conjuntos de 5000 até que todos os nomes dos BLOBs foram obtidos. A primeira é chamado esta API, devolve os nomes de 5000 blob primeiro e um token de continuação. Na segunda vez, que fornece o token, o serviço obtém o seguinte conjunto de nomes de blob e assim sucessivamente, até que o token de continuação tem o valor null, que indica que todos os nomes dos BLOBs foram obtidos. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>Transferir blobs

Transferir blobs para a sua utilização de disco local [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

O código seguinte transfere o blob carregado numa secção anterior, adicionar um sufixo de "_DOWNLOADED" para o nome do blob para que possa ver ambos os ficheiros no disco local. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de blobs carregados este início rápido, pode eliminar o contentor inteiro utilizando [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Também elimine os ficheiros criados se já não são necessárias.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como transferir ficheiros entre um disco local e o Blob storage do Azure através do .NET. Para obter mais informações sobre como trabalhar com armazenamento de BLOBs, continue para o Blob storage procedimentos.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de BLOBs](storage-dotnet-how-to-use-blobs.md)

Para obter mais informações sobre o Explorador de armazenamento e Blobs, consulte [recursos de armazenamento de Blobs do Azure de gerir com o Explorador de armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
