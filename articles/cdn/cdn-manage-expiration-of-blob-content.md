---
title: "Gerir a expiração do Blob storage do Azure numa rede de entrega de conteúdo do Azure | Microsoft Docs"
description: "Saiba mais sobre as opções para controlar o time-to-live para os blobs na CDN do Azure a colocação em cache."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: c2b49058ec7dd52b5063e815447697fa17ddb53a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Gerir a expiração do Blob storage do Azure numa rede de entrega de conteúdo do Azure
> [!div class="op_single_selector"]
> * [Conteúdo da web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [serviço de armazenamento de BLOBs](../storage/common/storage-introduction.md#blob-storage) no armazenamento do Azure é uma das várias origens baseado no Azure integrada com rede de entrega de conteúdos (CDN) do Azure. Qualquer conteúdo de blob acessível publicamente pode ser colocadas em cache na CDN do Azure depois de decorrido o time-to-live (TTL). O valor de TTL é determinado pelo `Cache-Control` cabeçalho de resposta HTTP do servidor de origem. Este artigo descreve as várias formas que pode definir o `Cache-Control` cabeçalho de um blob no Storage do Azure.

> [!TIP]
> Pode optar por não definir nenhum valor de TTL no blob. Neste caso, o Azure CDN aplica automaticamente uma predefinição TTL de sete dias.
> 
> Para obter mais informações sobre como funciona o CDN do Azure para acelerar o acesso para os blobs e outros ficheiros, consulte [descrição geral da rede de entrega de conteúdos de Azure](cdn-overview.md).
> 
> Para obter mais informações sobre o armazenamento de Blobs do Azure, consulte [introdução ao Blob storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="azure-powershell"></a>Azure PowerShell
[O Azure PowerShell](/powershell/azure/overview) é uma das formas mais rápidas e mais poderosas para administrar os serviços do Azure. Utilize o `Get-AzureStorageBlob` cmdlet para obter uma referência para o blob, em seguida, defina o `.ICloudBlob.Properties.CacheControl` propriedade. 

Por exemplo:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Também pode utilizar o PowerShell para [gerir os seus perfis da CDN e pontos finais](cdn-manage-powershell.md).
> 
>

## <a name="azure-storage-client-library-for-net"></a>Biblioteca de clientes do Storage do Azure para .NET
Para definir um blob `Cache-Control` cabeçalho utilizando o .NET, utilize o [biblioteca de clientes do Storage do Azure para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para definir o [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) propriedade.

Por exemplo:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Existem mais exemplos de código do .NET no [exemplos de armazenamento de Blobs do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Outros métodos
* [Interface de Linha de Comandos do Azure](../cli-install-nodejs.md)
  
    Quando carregar um blob, pode definir o *cacheControl* propriedade utilizando o `-p` comutador na Interface de linha de comandos do Azure. O exemplo a seguir define o valor de TTL para uma hora (3600 segundos):
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Definir explicitamente o *x-ms-BLOBs-cache-control* propriedade num [colocar Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [colocar a lista de bloqueios](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx), ou [definir propriedades de Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx) pedido.

* Ferramentas de gestão de armazenamento de terceiros
  
    Algumas ferramentas de gestão de armazenamento do Azure de terceiros permitem-lhe definir o **CacheControl** propriedade em blobs. 

## <a name="testing-the-cache-control-header"></a>O cabeçalho de Cache-Control de teste
Pode facilmente verificar as definições de TTL dos blobs. Com o seu browser [ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), teste o blob inclui o `Cache-Control` cabeçalho de resposta. Também pode utilizar uma ferramenta como **wget**, [Postman](https://www.getpostman.com/), ou [Fiddler](http://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Saiba como gerir a expiração de conteúdo do serviço em nuvem na CDN do Azure](cdn-manage-expiration-of-cloud-service-content.md)

