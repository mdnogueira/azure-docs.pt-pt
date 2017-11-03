---
title: Carregar dados de imagem na nuvem com o Storage do Azure | Microsoft Docs
description: "Utilizar o blob storage do Azure com uma aplicação web para armazenar dados de aplicação"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: a204498016ff837c5247009eaaffbd4f79285d0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Carregar dados de imagem na nuvem com o Storage do Azure

Este tutorial faz parte de um de uma série. Este tutorial mostra como implementar uma aplicação web que utiliza a biblioteca de clientes de armazenamento do Azure para carregar as imagens para uma conta de armazenamento. Quando tiver terminado, terá uma aplicação web, armazenar e apresentar imagens a partir do armazenamento do Azure.

![Vista de contentor de imagens](media/storage-upload-process-images/figure2.png)

Na parte de uma série, saiba como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contentor e definir permissões
> * Obter uma chave de acesso
> * Configurar as definições da aplicação
> * Implementar uma aplicação Web no Azure
> * Interagir com a aplicação web

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos 

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.
 
O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
 
O exemplo carrega imagens para um contentor de BLOBs numa conta do Storage do Azure. Uma conta do storage fornece um espaço de nomes exclusivo para armazenar e aceder aos seus objetos de dados de armazenamento do Azure. Utilize o comando [az storage account create](/cli/azure/storage/account#create) para criar uma conta de armazenamento no grupo de recursos que criou. 

> [!IMPORTANT] 
> Parte 2 do tutorial utiliza o subscrições de eventos para o blob storage. Subscrições de evento são atualmente suportados apenas para contas do Blob storage na Central EUA oeste e EUA oeste 2. Devido a esta restrição, tem de criar uma conta de armazenamento de BLOBs que é utilizada pela aplicação de exemplo para armazenar imagens e miniaturas.   

O seguinte comando, substitua o seus próprios nome globalmente exclusivo para a conta de armazenamento de BLOBs onde vir o `<blob_storage_account>` marcador de posição.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Criar contentores de armazenamento de BLOBs
 
A aplicação utiliza dois contentores na conta de armazenamento de Blobs. Contentores são semelhantes às pastas e são utilizados para armazenar blobs. O _imagens_ contentor é onde a aplicação carrega imagens de resolução de completo. Na parte posterior da série, uma aplicação de função do Azure carrega as miniaturas de imagem dimensionado para o _thumbs_ contentor. 

Obter a chave de conta de armazenamento utilizando o [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list) comando. Em seguida, utilizar esta chave para criar dois contentores utilizando o [criar contentor de armazenamento az](/cli/azure/storage/container#create) comando.  
 
Neste caso, `<blob_storage_account>` é o nome da conta de armazenamento de BLOBs que criou. O _imagens_ acesso de público de contentores está definido como `off`, a _thumbs_ acesso de público de contentores está definido como `container`. O `container` miniaturas ser visualizado às pessoas que visitar a página web permite a definição de acesso público.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbs --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Tome nota do nome de conta do blob storage e da chave. A aplicação de exemplo utiliza estas definições para ligar à conta de armazenamento para carregar as imagens. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações 

Um [plano do serviço de aplicações](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) especifica o local, tamanho e funcionalidades da farm de servidores Web que aloja a aplicação. 

Crie um plano do Serviço de Aplicações com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

O exemplo seguinte cria um plano do Serviço de Aplicações com o nome `myAppServicePlan`, que utiliza o escalão de preços **Gratuito**. 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Criar uma aplicação Web 

A aplicação web fornece um espaço de alojamento para o código de aplicação de exemplo que for implementado a partir do repositório de exemplo do GitHub. Crie uma [aplicação Web](../../app-service/app-service-web-overview.md) no plano do `myAppServicePlan`Serviço de Aplicações com o comando[az webapp create](/cli/azure/webapp#create).  
 
O seguinte comando, substitua `<web_app>` com um nome exclusivo (carateres válidos são `a-z`, `0-9`, e `-`). Se `<web_app>` é não exclusivo, obtém a mensagem de erro: _Web site com o nome fornecido `<web_app>` já existe._ O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementar a aplicação de exemplo do repositório GitHub 

App Service suporte várias formas de implementar o conteúdo para uma aplicação web. Neste tutorial, implementa a aplicação de um repositório de exemplo público do GitHub: [https://github.com/Azure-Samples/storage-blob-upload-from-webapp](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configurar a implementação do GitHub para a aplicação web com o [az webapp configuração da origem de implementação](/cli/azure/webapp/deployment/source#config) comando. Substitua `<web_app>` com o nome da aplicação web que criou no passo anterior.

O projeto de exemplo contém um [ASP.NET MVC](https://www.asp.net/mvc) aplicação que aceita uma imagem, guarda-o para uma conta de armazenamento e apresenta imagens a partir de um contentor em miniatura. A aplicação web utiliza o [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet)e o [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) espaços de nomes do armazenamento do Azure biblioteca de cliente para interagir com o storage do Azure. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Configurar definições da aplicação web 

As utilizações de aplicação de web de exemplo a [biblioteca de clientes do Storage do Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) para pedir acesso tokens que são utilizados para carregar as imagens. As credenciais da conta de armazenamento utilizadas pelo SDK de armazenamento são definidas nas definições da aplicação para a aplicação web. Adicionar as definições da aplicação para a aplicação implementada com o [az webapp configuração appsettings conjunto](/cli/azure/webapp/config/appsettings#set) comando. 

O comando seguinte, `<blob_storage_account>` é o nome da sua conta do Blob storage e `<blob_storage_key>` é a chave associada. Substitua `<web_app>` com o nome da aplicação web que criou no passo anterior.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbs \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Depois da aplicação web é implementada e configurada, pode testar a funcionalidade de carregamento da imagem na aplicação.   

## <a name="upload-an-image"></a>Carregar uma imagem 

Para testar a aplicação web, navegue para o URL da sua aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`. Selecione o **carregar fotografias** região para selecionar e carregar um ficheiro ou arrastar e largar um ficheiro na região. A imagem desaparece se carregado com êxito.

![Aplicação de ImageResizer](media/storage-upload-process-images/figure1.png)

No código de exemplo, o `UploadFiletoStorage` de tarefas no `Storagehelper.cs` ficheiro é utilizado para carregar as imagens para a `images` contentor dentro da conta de armazenamento utilizando o [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) método. O exemplo de código seguinte contém os `UploadFiletoStorage` tarefas. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Métodos e as classes seguintes são utilizados na tarefa anterior:

|Classe  |Método  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Certifique-se de que a imagem é apresentada na conta de armazenamento

Inicie sessão no https://portal.azure.com. No menu à esquerda, selecione **contas do Storage**, em seguida, selecione o nome da sua conta de armazenamento. Em **descrição geral**, selecione o **imagens** contentor.

Certifique-se de que a imagem é apresentada no contentor.

![Vista de contentor de imagens](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Visualização de miniaturas de teste

Para testar a visualização de miniaturas, deverá carregar uma imagem para o contentor de miniaturas para garantir que a aplicação pode ler o contentor em miniatura.

Inicie sessão no https://portal.azure.com. No menu à esquerda, selecione **contas do Storage**, em seguida, selecione o nome da sua conta de armazenamento. Selecione **contentores** em **serviço Blob** e selecione o **thumbs** contentor. Selecione **carregar** para abrir o **carregar blob** painel.

Escolha um ficheiro com o Seletor de ficheiros e selecione **carregar**.

Navegue de volta para a sua aplicação para verificar que a imagem carregada para o **thumbs** contentor está visível.

![Vista de contentor de imagens](media/storage-upload-process-images/figure2.png)

No **thumbs** contentor no portal do Azure, selecione a imagem carregou e selecione **eliminar** eliminou a imagem. Na parte dois da série, estiver a automatizar a criação de imagens em miniatura, pelo que esta imagem de teste não é necessária.

CDN pode ser ativada para o conteúdo da cache da sua conta de armazenamento do Azure. Enquanto não descritas neste tutorial, para saber como ativar a CDN com a sua conta de armazenamento do Azure, pode visitar: [integrar uma conta de armazenamento do Azure com o Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passos seguintes

Parte de uma série, aprendeu sobre como configurar uma aplicação web de interagir com o armazenamento, tais como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contentor e definir permissões
> * Obter uma chave de acesso
> * Configurar as definições da aplicação
> * Implementar uma aplicação Web no Azure
> * Interagir com a aplicação web

Avançar para a parte dois da série para saber mais sobre como utilizar a grelha de eventos para acionar uma função do Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Utilize a grelha de eventos para acionar uma função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
