---
title: "Ativar o acesso de leitura público para contentores e blobs do armazenamento de Blobs do Azure | Microsoft Docs"
description: "Saiba como tornar disponível para acesso anónimo a contentores e blobs e como aceder às mesmas através de programação."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: f52079c72be298daaa45074e516f911022780392
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerir o acesso de leitura anónimo a contentores e blobs
Pode ativar o acesso de leitura anónimo, público para um contentor e os respetivos blobs no armazenamento de Blobs do Azure. Ao fazê-lo, pode conceder acesso só de leitura a estes recursos sem partilha a chave de conta e, sem necessidade de uma assinatura de acesso partilhado (SAS).

Acesso de leitura público é melhor para cenários onde pretende que determinados blobs sempre estar disponível para acesso de leitura anónimo. Para um controlo mais preciso, pode criar uma assinatura de acesso partilhado. Assinaturas de acesso partilhado permitem-lhe fornecer acesso restrito com permissões diferentes, durante um período de tempo específico. Para obter mais informações sobre como criar partilhado assinaturas de acesso, consulte [Using partilhado assinaturas de acesso (SAS) no Storage do Azure](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de utilizadores anónimos a contentores e blobs
Por predefinição, um contentor e blobs dentro do mesmo podem ser acedidos apenas pelo proprietário da conta de armazenamento. Para atribuir utilizadores anónimos permissões de leitura para um contentor e os respetivos blobs, pode definir as permissões de contentor para permitir o acesso público. Utilizadores anónimos podem ler os blobs num contentor acessível publicamente sem autenticar o pedido.

Pode configurar um contentor com as seguintes permissões:

* **Acesso de leitura não público:** o contentor e os respetivos blobs podem ser acedidos apenas por proprietário da conta de armazenamento. Esta é a predefinição para todos os novos contentores.
* **Acesso para blobs só de leitura de público:** Blobs no contentor podem ser lidos por pedido anónimo, mas não estão disponíveis dados de contentor. Os clientes anónimos não é possível enumerar os blobs no contentor.
* **Total de acesso de leitura público:** todos os dados de blob e contentor podem ser lidos por pedido anónimo. Os clientes podem enumerar os blobs no contentor por pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.

Pode utilizar o seguinte para definir as permissões do contentor:

* [Portal do Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [CLI 2.0 do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Através de programação, ao utilizar uma das bibliotecas de cliente de armazenamento ou a API REST

### <a name="set-container-permissions-in-the-azure-portal"></a>Definir permissões de contentor no portal do Azure
Definir as permissões do contentor do [portal do Azure](https://portal.azure.com), siga estes passos:

1. Abra o **conta de armazenamento** painel no portal. Pode encontrar a conta do storage ao selecionar **contas do Storage** no painel do menu principal do portal.
1. Em **serviço BLOB** no painel de menu, selecione **contentores**.
1. Faça duplo clique na linha contentor ou selecione o botão de reticências para abrir o contentor **menu de contexto**.
1. Selecione **política de acesso** no menu de contexto.
1. Selecione um **aceder tipo** no menu pendente.

    ![Metadados do contentor caixa de diálogo Editar](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Defina as permissões de contentor com o .NET
Para definir as permissões para um contentor com c# e a biblioteca de clientes de armazenamento para .NET, obter permissões existentes no contentor ao chamar o **GetPermissions** método. Em seguida, defina o **PublicAccess** propriedade para o **BlobContainerPermissions** objeto é devolvido pelo **GetPermissions** método. Por fim, chame o **SetPermissions** método com as permissões atualizados.

O exemplo a seguir define as permissões o contentor para acesso de leitura público completa. Para definir as permissões para acesso de leitura público para apenas blobs, defina o **PublicAccess** propriedade **BlobContainerPublicAccessType.Blob**. Para remover todas as permissões para utilizadores anónimos, defina a propriedade como **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Aceder anonimamente a contentores e blobs
Um cliente que acede ao contentores e blobs anonimamente pode utilizar construtores que não necessitem de credenciais. Os exemplos seguintes mostram algumas formas diferentes de referenciar recursos do serviço Blob anonimamente.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anónimo
Pode criar um novo objeto de cliente de serviço para acesso anónimo ao fornecer o ponto final do serviço Blob para a conta. No entanto, também deve conhecer o nome de um contentor nessa conta que está disponível para acesso anónimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Referência a um contentor anonimamente
Se tiver o URL para um contentor anonimamente disponível, pode utilizá-lo para o contentor de referenciar diretamente.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Referenciar um blob anonimamente
Se tiver o URL para um blob que está disponível para acesso anónimo, pode referenciar o blob diretamente utilizando esse URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funcionalidades disponíveis para utilizadores anónimos
Mostra o seguinte tabela que operações podem ser chamadas por utilizadores anónimos quando ACL um contentor está definido para permitir o acesso público.

| Operação REST | Permissão com acesso de leitura público completa | Permissão com acesso de leitura público para blobs apenas |
| --- | --- | --- |
| Contentores de lista |Apenas o proprietário |Apenas o proprietário |
| Criar contentor |Apenas o proprietário |Apenas o proprietário |
| Obter as propriedades do contentor |Todos |Apenas o proprietário |
| Obter metadados do contentor |Todos |Apenas o proprietário |
| Definir os metadados do contentor |Apenas o proprietário |Apenas o proprietário |
| Obter o contentor ACL |Apenas o proprietário |Apenas o proprietário |
| Definir o contentor ACL |Apenas o proprietário |Apenas o proprietário |
| Eliminar o contentor |Apenas o proprietário |Apenas o proprietário |
| Lista de Blobs |Todos |Apenas o proprietário |
| Colocar Blob |Apenas o proprietário |Apenas o proprietário |
| Obter o Blob |Todos |Todos |
| Obter propriedades de Blob |Todos |Todos |
| Defina as propriedades de Blob |Apenas o proprietário |Apenas o proprietário |
| Obter metadados do Blob |Todos |Todos |
| Definir os metadados do Blob |Apenas o proprietário |Apenas o proprietário |
| Colocar bloco |Apenas o proprietário |Apenas o proprietário |
| Obter a lista de bloqueios (apenas blocos consolidados) |Todos |Todos |
| Obter a lista de bloqueios (apenas blocos não consolidados ou todos os blocos) |Apenas o proprietário |Apenas o proprietário |
| Colocar a lista de bloqueios |Apenas o proprietário |Apenas o proprietário |
| Eliminar o Blob |Apenas o proprietário |Apenas o proprietário |
| Copiar Blob |Apenas o proprietário |Apenas o proprietário |
| Blob de instantâneo |Apenas o proprietário |Apenas o proprietário |
| Blob de concessão |Apenas o proprietário |Apenas o proprietário |
| Colocar a página |Apenas o proprietário |Apenas o proprietário |
| Obter intervalos de página |Todos |Todos |
| Blob de acréscimo |Apenas o proprietário |Apenas o proprietário |

## <a name="next-steps"></a>Passos seguintes

* [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Utilizar assinaturas de acesso partilhado (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegar Acesso com uma Assinatura de Acesso Partilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)
