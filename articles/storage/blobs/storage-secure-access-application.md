---
title: "Proteger o acesso aos dados de uma aplicação na nuvem com o Storage do Azure | Microsoft Docs"
description: "Utilize tokens SAS, encriptação e HTTPS para proteger os dados da sua aplicação na nuvem"
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
ms.openlocfilehash: 11b141617bea5962c45e1b91cf6629c2edd26c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Acesso seguro aos dados de uma aplicação na nuvem

Este tutorial é parte três de uma série. Saiba como proteger o acesso à conta de armazenamento. 

Na parte três da série, saiba como:

> [!div class="checklist"]
> * Utilize SAS tokens para aceder a imagens em miniatura
> * Ativar encriptação do lado do servidor
> * Ativar o transporte HTTPS só

[Armazenamento de Blobs do Azure](../common/storage-introduction.md#blob-storage) fornece um serviço robusto para armazenar os ficheiros para aplicações. Este tutorial expande [tópico anterior] [ previous-tutorial] para mostrar como proteger o acesso à sua conta de armazenamento a partir de uma aplicação web. Quando tiver terminado, as imagens são encriptadas e a aplicação web utiliza os tokens SAS segurados para aceder às imagens em miniatura.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter concluído o tutorial de armazenamento anterior: [Automate redimensionamento carregado imagens com eventos grelha][previous-tutorial]. 

## <a name="set-container-public-access"></a>Acesso de público de contentor do conjunto

Nesta parte da série tutorial, os tokens SAS são utilizados para aceder ao miniaturas. Neste passo, configurou o acesso público o _thumbs_ contentor para `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurar os tokens SAS de miniaturas

Na parte de uma série neste tutorial, a aplicação web foi Mostrar imagens a partir de um contentor público. Nesta parte da série, utilize [assinatura de acesso seguro (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) tokens para obter as imagens em miniatura. Os tokens SAS permitem-lhe fornecer acesso restrito a um contentor ou BLOBs com base em IP, o protocolo, o intervalo de tempo ou direitos permitidos.

Neste exemplo, o repositório de código de origem utiliza a `sasTokens` ramo, que tem um exemplo de código atualizado. Elimine a implementação existente do GitHub com o [delete de origem de implementação de webapp az](/cli/azure/webapp/deployments/source#delete). Em seguida, configurar a implementação de GitHub para a aplicação web com o [az webapp configuração da origem de implementação](/cli/azure/webapp/deployment/source#config) comando.  

O comando seguinte, `<web-app>` é o nome da sua aplicação web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

O `sasTokens` ramo do repositório atualizações a `StorageHelper.cs` ficheiro. Substitui o `GetThumbNailUrls` tarefa com o exemplo de código abaixo. A tarefa atualizada obtém a miniatura URLs, definindo um [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) para especificar a hora de início, hora de expiração e as permissões para o token SAS. Depois de implementar a aplicação web agora obtém miniaturas com um URL através de um token SAS. A tarefa de atualização é apresentada no exemplo seguinte:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

As seguintes classes, propriedades e métodos são utilizados na tarefa anterior:

|Classe  |Propriedades| Métodos  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Resultados](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Permissões](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Encriptação do lado do servidor

[Encriptação de serviço de armazenamento do Azure (SSE)](../common/storage-service-encryption.md) ajuda a proteger e a salvaguardar os seus dados. SSE encripta dados inativos, processamento de encriptação, desencriptação e gestão de chaves. Todos os dados são encriptados utilizando 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um bloco de maior cifras disponíveis.

No seguinte exemplo ativar a encriptação para os blobs. Os blobs existentes criados antes de ativar a encriptação não estão encriptados. O `x-ms-server-encrypted` cabeçalho num pedido para um blob mostra o estado de encriptação do blob.

```azurecli-interactive
az storage account update --encryption-services blob --name <storage-account-name> --resource-group myResourceGroup
```

Carregue uma nova imagem para a aplicação web, agora que a encriptação está ativada.

Utilizar `curl` com o comutador `-I` para obter apenas os cabeçalhos, substitua os seus próprios valores para `<storage-account-name>`, `<container>`, e `<blob-name>`.  

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<blob-name>?$sasToken -I
```

Na resposta, tenha em atenção o `x-ms-server-encrypted` cabeçalho mostra `true`. Este cabeçalho identifica que os dados já estão encriptados com SSE.

```
HTTP/1.1 200 OK
Content-Length: 209489
Content-Type: image/png
Last-Modified: Mon, 11 Sep 2017 19:27:42 GMT
Accept-Ranges: bytes
ETag: "0x8D4F94B2BE76D45"
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 57047db3-001e-0050-3e34-2ba769000000
x-ms-version: 2017-04-17
x-ms-lease-status: unlocked
x-ms-lease-state: available
x-ms-blob-type: BlockBlob
x-ms-server-encrypted: true
Date: Mon, 11 Sep 2017 19:27:46 GMT
```

## <a name="enable-https-only"></a>Permitir apenas HTTPS

Para garantir que os pedidos de dados de e para uma conta de armazenamento são seguros, pode limitar pedidos apenas para HTTPS. Atualize o protocolo necessário de conta de armazenamento utilizando o [atualização az da conta de armazenamento](/cli/azure/storage/account#update) comando.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testar a ligação utilizando `curl` utilizando o `HTTP` protocolo.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Agora que a transferência segura não é necessária, receber a mensagem seguinte:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Passos seguintes

Na parte três da série, aprendeu a proteger o acesso à conta de armazenamento, tais como:

> [!div class="checklist"]
> * Utilize SAS tokens para aceder a imagens em miniatura
> * Ativar encriptação do lado do servidor
> * Ativar o transporte HTTPS só

Avançar para a parte quatro da série para saber como monitorizar e resolver problemas de uma aplicação de armazenamento na nuvem.

> [!div class="nextstepaction"]
> [Monitorizar e resolver problemas de armazenamento de aplicação na nuvem de aplicação](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json