---
title: "Introdução ao blob storage do Azure e o Visual Studio ligado serviços (ASP.NET) | Microsoft Docs"
description: "Como começar a utilizar o armazenamento de Blobs do Azure num projeto ASP.NET no Visual Studio depois de se ligar a uma conta de armazenamento utilizando o Visual Studio ligado Services"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraig
ms.openlocfilehash: e953c7978705379a28581213e8f1c665473ddd60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao blob storage do Azure e o Visual Studio ligado serviços (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos.

Este tutorial mostra como escrever código ASP.NET para alguns cenários comuns utilizando o armazenamento de Blobs do Azure. Os cenários incluem a criação de um contentor de blob, carregar, listar, transferir e eliminar blobs.

##<a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Explorador de soluções**, faça duplo clique **controladores**e, no menu de contexto, selecione **adicionar -> controlador**.

    ![Adicionar um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. No **adicionar andaime** caixa de diálogo, selecione **controlador MVC 5 – vazio**e selecione **adicionar**.

    ![Especifique o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. No **Adicionar controlador** caixa de diálogo, o nome do controlador *BlobsController*e selecione **adicionar**.

    ![Nome do controlador MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Adicione o seguinte *utilizando* diretivas para a `BlobsController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Criar um contentor de blob

Um contentor do blob é uma hierarquia aninhada de blobs e de pastas. Os passos seguintes mostram como criar um contentor do blob:

> [!NOTE]
> 
> O código nesta secção assume que concluiu os passos na secção, [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método denominado **CreateBlobContainer** que devolve um **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **CreateBlobContainer** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Alterar  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudBlobClient** objeto representa um cliente do serviço blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obter um **CloudBlobContainer** objeto que representa uma referência para o nome do contentor de blob pretendido. O **CloudBlobClient.GetContainerReference** método não faz um pedido para o blob storage. A referência é devolvida se ou não existe no contentor de blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Chamar o **CloudBlobContainer.CreateIfNotExists** método para criar o contentor se ainda não existir. O **CloudBlobContainer.CreateIfNotExists** método devolve **verdadeiro** se o contentor não existe e é criado com êxito. Caso contrário, **falso** é devolvido.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Atualização do **ViewBag** com o nome do contentor do blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **Blobs**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **CreateBlobContainer** para o nome da vista e selecione **adicionar**.

1. Abra `CreateBlobContainer.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Executar a aplicação e selecione **criar contentor de Blob** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Criar contentor de blob](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Tal como mencionado anteriormente, o **CloudBlobContainer.CreateIfNotExists** método devolve **verdadeiro** apenas quando o contentor não existe e é criado. Por conseguinte, se executar a aplicação quando o contentor existe, o método devolve **falso**. Para executar a aplicação várias vezes, tem de eliminar o contentor antes de executar novamente a aplicação. A eliminar o contentor pode ser efetuada através de **CloudBlobContainer.Delete** método. Também pode eliminar o contentor utilizando o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Carregar um blob para um contentor do blob

Assim que tiver [criou um contentor de blob](#create-a-blob-container), pode carregar ficheiros para esse contentor. Esta secção explica como carregar um ficheiro local para um contentor do blob. Os passos partem do princípio de que criou um contentor do blob denominado *contentor de BLOBs de teste*. 

> [!NOTE]
> 
> O código nesta secção assume que concluiu os passos na secção, [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método denominado **UploadBlob** que devolve um **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dentro do **UploadBlob** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudBlobClient** objeto representa um cliente do serviço blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obter um **CloudBlobContainer** objeto que representa uma referência para o nome do contentor de blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Tal como explicado anteriormente, o storage do Azure suporta tipos de blob diferente. Para obter uma referência para um blob de página, chame o **CloudBlobContainer.GetPageBlobReference** método. Para obter uma referência para um blob de bloco, chame o **CloudBlobContainer.GetBlockBlobReference** método. Normalmente, os BLOBs de blocos é o tipo recomendado a utilizar. (Alterar < nome do blob > * para o nome que pretende conceder o blob, uma vez carregado.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Depois de ter uma referência de blob, pode carregar qualquer fluxo de dados para a mesma ao chamar o objeto de referência de blob **UploadFromStream** método. O **UploadFromStream** método cria o blob, se não existir ou substitui-lo se existir. (Alterar  *&lt;carregamento do ficheiro >* para um caminho completamente qualificado para o ficheiro que pretende carregar.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **Blobs**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Executar a aplicação e selecione **carregar blob**.  
  
A secção - [listar os blobs num contentor de blob](#list-the-blobs-in-a-blob-container) -ilustra como listar os blobs num contentor de blob.    

## <a name="list-the-blobs-in-a-blob-container"></a>Listar os blobs num contentor de blob

Esta secção ilustra como listar os blobs num contentor de blob. As referências do código de exemplo a *contentor de BLOBs de teste* criou na secção, [criar um contentor de blob](#create-a-blob-container).

> [!NOTE]
> 
> O código nesta secção assume que concluiu os passos na secção, [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método denominado **ListBlobs** que devolve um **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **ListBlobs** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudBlobClient** objeto representa um cliente do serviço blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obter um **CloudBlobContainer** objeto que representa uma referência para o nome do contentor de blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Para listar os blobs num contentor de BLOBs, utilize o **CloudBlobContainer.ListBlobs** método. O **CloudBlobContainer.ListBlobs** método devolve um **IListBlobItem** objeto convertido para um **CloudBlockBlob**, **CloudPageBlob**, ou **CloudBlobDirectory** objeto. O fragmento de código seguinte enumera todos os blobs num contentor de blob. Cada blob é convertido para o objecto apropriado com base no respetivo tipo e o respetivo nome (ou URI em case de um **CloudBlobDirectory**) é adicionado a uma lista.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Para além de blobs, os contentores de blob podem conter diretórios. Vamos imaginar que tem um contentor do blob denominado *contentor de BLOBs de teste* com a hierarquia seguinte:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Utilizando o exemplo de código anterior, o **blobs** lista de cadeias contém valores semelhantes ao seguinte:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Como pode ver, a lista inclui apenas as entidades de nível superior; não aninhados aqueles (*bar.png* e *baz.png*). Para listar todas as entidades dentro de um contentor de blob, tem de chamar o **CloudBlobContainer.ListBlobs** método e passar **verdadeiro** para o **useFlatBlobListing** parâmetro.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    Definir o **useFlatBlobListing** parâmetro **verdadeiro** devolve uma lista não hierárquica de todas as entidades no contentor de blob e gera os seguintes resultados:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **Blobs**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **ListBlobs** para o nome da vista e selecione **adicionar**.

1. Abra `ListBlobs.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Executar a aplicação e selecione **listar blobs** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Lista de blob](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Transferir blobs

Esta secção ilustra como um blob de transferir e a manter armazenamento local ou ler o conteúdo numa cadeia. As referências do código de exemplo a *contentor de BLOBs de teste* criou na secção, [criar um contentor de blob](#create-a-blob-container).

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método denominado **DownloadBlob** que devolve um **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dentro do **DownloadBlob** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudBlobClient** objeto representa um cliente do serviço blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obter um **CloudBlobContainer** objeto que representa uma referência para o nome do contentor de blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. A obtenção de um objeto de referência de blob chamando **CloudBlobContainer.GetBlockBlobReference** ou **CloudBlobContainer.GetPageBlobReference** método. (Alterar  *&lt;nome do blob >* para o nome do blob estiver a transferir.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Para transferir um blob, utilize o **CloudBlockBlob.DownloadToStream** ou **CloudPageBlob.DownloadToStream** método, dependendo do tipo de blob. O seguinte código utiliza de fragmento a **CloudBlockBlob.DownloadToStream** método para transferir conteúdos de um blob para um objeto de fluxo que, em seguida, é transferido para um ficheiro local: (alteração  *&lt;nome de ficheiro local >* nome que representa onde pretende que o blob transferido para o ficheiro completamente qualificado.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Executar a aplicação e selecione **transferir BLOBs** para transferir o blob. O blob especificado no **CloudBlobContainer.GetBlockBlobReference** transferências de chamada de método para a localização que especificou no **File.OpenWrite** chamada de método. 

## <a name="delete-blobs"></a>Eliminar blobs

Os passos seguintes ilustram como eliminar um blob:

> [!NOTE]
> 
> O código nesta secção assume que concluiu os passos na secção, [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `BlobsController.cs`.

1. Adicione um método denominado **DeleteBlob** que devolve um **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudBlobClient** objeto representa um cliente do serviço blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obter um **CloudBlobContainer** objeto que representa uma referência para o nome do contentor de blob. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. A obtenção de um objeto de referência de blob chamando **CloudBlobContainer.GetBlockBlobReference** ou **CloudBlobContainer.GetPageBlobReference** método. (Alterar  *&lt;nome do blob >* para o nome do blob serão eliminados.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Executar a aplicação e selecione **eliminar BLOBs** para eliminar o blob especificado no **CloudBlobContainer.GetBlockBlobReference** chamada de método. 

## <a name="next-steps"></a>Passos seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao table storage do Azure e o Visual Studio ligado serviços (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
