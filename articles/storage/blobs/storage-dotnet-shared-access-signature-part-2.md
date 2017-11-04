---
title: Criar e utilizar uma assinatura de acesso partilhado (SAS) com o Blob storage do Azure | Microsoft Docs
description: "Este tutorial mostra como criar assinaturas de acesso partilhado para utilização com o Blob storage e como aceder aos mesmos nas suas aplicações de cliente."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Partilhado assinaturas de acesso, parte 2: Criar e utilizar um SAS com o Blob storage

[Parte 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) deste tutorial, explorou partilhada assinaturas de acesso (SAS) e explicado melhores práticas para utilizá-los. Parte 2 mostra como gerar e, em seguida, utilizar assinaturas de acesso partilhado com armazenamento de Blobs. Os exemplos são escritos em c# e utilizam a biblioteca de cliente de armazenamento do Azure para .NET. Os exemplos neste tutorial:

* Gerar uma assinatura de acesso partilhado num contentor
* Gerar uma assinatura de acesso partilhado no blob
* Criar uma política de acesso armazenada para gerir assinaturas nos recursos de um contentor
* Testar as assinaturas de acesso partilhado numa aplicação de cliente

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Neste tutorial, vamos criar duas aplicações de consola que demonstram criar e utilizar assinaturas de acesso partilhado para contentores e blobs:

**Aplicação 1**: A aplicação de gestão. Gera uma assinatura de acesso partilhado para um contentor e um blob. Inclui a chave de acesso da conta de armazenamento no código fonte.

**Aplicação 2**: A aplicação de cliente. Acede contentor de blob recursos e utilizando as assinaturas de acesso partilhado criadas com a primeira aplicação. Utiliza as assinaturas de acesso partilhado para o contentor de acesso e de recursos do blob – faz *não* incluem a chave de acesso da conta de armazenamento.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Criar uma aplicação de consola para gerar assinaturas de acesso partilhado
Em primeiro lugar, certifique-se de que tem a biblioteca de clientes de armazenamento do Azure para .NET instalado. Pode instalar o [pacote NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "pacote NuGet") que contém as assemblagens mais atualizadas para a biblioteca de cliente. Este é o método recomendado para se certificar de que tem as correções mais recentes. Também pode transferir a biblioteca de cliente como parte da versão mais recente do [Azure SDK para .NET](https://azure.microsoft.com/downloads/).

No Visual Studio, crie uma nova aplicação de consola do Windows e dê-lhe nome **GenerateSharedAccessSignatures**. Adicione as referências [configurationmanager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) e [Windowsazure](https://www.nuget.org/packages/WindowsAzure.Storage/) utilizando uma das abordagens seguintes:

* Utilize o [Gestor de pacotes NuGet](https://docs.nuget.org/consume/installing-nuget) no Visual Studio. Selecione **projeto** > **gerir pacotes NuGet**, procure online por cada pacote (configurationmanager e Windowsazure) e instalá-los.
* Em alternativa, localize estas assemblagens na instalação do SDK do Azure e adicionar referências aos mesmos:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Na parte superior do ficheiro Program.cs, adicione o seguinte **utilizando** diretivas:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Edite o ficheiro App. config para que contém uma definição de configuração com uma cadeia de ligação que aponta para a sua conta do storage. O ficheiro App. config deve ter um aspeto semelhante a esta:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Gerar uma assinatura de acesso partilhado URI para um contentor
A lugar com, iremos adicionar um método para gerar uma assinatura de acesso partilhado num novo contentor. Neste caso, a assinatura não está associada uma política de acesso armazenada, para acarreta no URI as informações que indica a hora de expiração e as permissões concede acesso.

Em primeiro lugar, adicione o código para o **main ()** método para autenticar o acesso à sua conta de armazenamento e criar um novo contentor:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Em seguida, adicione um método que gera a assinatura de acesso partilhado do contentor e devolve a assinatura do URI:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Adicione as seguintes linhas na parte inferior do **main ()** método, antes da chamada para **Console.ReadLine()**, para chamar **GetContainerSasUri()** e escrita a assinatura URI para o janela de consola:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Compilar e executar para a assinatura de acesso partilhado URI para o novo contentor de saída. O URI será semelhante ao seguinte:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Assim que tiver executado o código, a assinatura de acesso partilhado que criou para o contentor irá ser válida durante as próximas 24 horas. A assinatura atribui uma permissão de cliente para blobs de lista no contentor e escrever os novos blobs no contentor.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Gerar uma assinatura de acesso partilhado URI para um blob
Em seguida, iremos escrever código semelhante para criar um novo blob no contentor e gerar uma assinatura de acesso partilhado para o mesmo. Esta assinatura de acesso partilhado não está associada uma política de acesso armazenada, modo a que inclua a hora de início, hora de expiração e as informações de permissão no URI.

Adicione um novo método que cria um novo blob e escreve algum texto, em seguida, gera uma assinatura de acesso partilhado e devolve a assinatura do URI:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Na parte inferior do **main ()** método, adicione as seguintes linhas ao chamar **GetBlobSasUri()**, antes da chamada para **Console.ReadLine()**e de escrita de assinatura de acesso partilhado URI para a janela de consola:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Compilar e executar para a assinatura de acesso partilhado URI para o novo blob de saída. O URI será semelhante ao seguinte:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Criar uma política de acesso armazenada no contentor
Agora vamos criar uma política de acesso armazenada no contentor, que será definem as restrições para as assinaturas de acesso partilhado que estão associadas ao mesmo.

Nos exemplos anteriores, iremos especificar a hora de início (implícita ou explicitamente), a hora de expiração e as permissões a assinatura de acesso partilhado URI próprio. Nos exemplos seguintes, vamos especificá-los na política de acesso armazenada, não na assinatura de acesso partilhado. Se o fizer, permite-nos alterar estas restrições sem emitir novamente a assinatura de acesso partilhado.

É possível ter uma ou mais das restrições na assinatura de acesso partilhado e o resto na política de acesso armazenada. No entanto, apenas pode especificar a hora de início, hora de expiração e as permissões no local ou o outro. Por exemplo, não é possível especificar permissões na assinatura de acesso partilhado e também especifique-os na política de acesso armazenada.

Quando adicionar uma política de acesso armazenada para um contentor, tem de obter permissões existentes no contentor, adicionar a nova política de acesso e, em seguida, definir permissões do contentor.

Adicione um novo método que cria uma nova política de acesso armazenado num contentor e devolve o nome da política:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

Na parte inferior do **main ()** método, antes da chamada para **Console.ReadLine()**, adicione as seguintes linhas ao limpar primeiro quaisquer políticas de acesso existentes e, em seguida, chame o  **CreateSharedAccessPolicy()** método:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Quando desmarcar as políticas de acesso num contentor, tem primeiro obter as permissões existentes no contentor, em seguida, desmarque as permissões e defina novamente as permissões.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Gerar uma assinatura de acesso partilhado URI no contentor que utiliza uma política de acesso
Em seguida, iremos criar outro assinatura de acesso partilhado do contentor que criámos anteriormente, mas, desta vez, associar a assinatura com a política de acesso armazenada que no exemplo anterior foi criada.

Adicione um novo método para gerar outro assinatura de acesso partilhado do contentor:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Na parte inferior do **main ()** método, antes da chamada para **Console.ReadLine()**, adicione as seguintes linhas ao chamar o **GetContainerSasUriWithPolicy** método:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Gerar uma assinatura de acesso partilhado URI no Blob que utiliza uma política de acesso
Por último, iremos adicionar um método para criar outro blob e gerar uma assinatura de acesso partilhado que está associada a uma política de acesso armazenada semelhante.

Adicione um novo método para criar um blob e gerar uma assinatura de acesso partilhado:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Na parte inferior do **main ()** método, antes da chamada para **Console.ReadLine()**, adicione as seguintes linhas ao chamar o **GetBlobSasUriWithPolicy** método:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

O **main ()** método deve agora ter este aspeto na íntegra. Execute-o para a assinatura de acesso partilhado os URIs de escrita para a janela de consola, em seguida, copie e cole-os para um ficheiro de texto para utilização na segunda parte deste tutorial.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Quando executar a aplicação de consola GenerateSharedAccessSignatures, verá um resultado semelhante ao seguinte. Estas são as assinaturas de acesso partilhado que utilizar na parte 2 do tutorial.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Criar uma aplicação de consola para testar as assinaturas de acesso partilhado
Para testar as assinaturas de acesso partilhado criadas nos exemplos anteriores, podemos criar uma segunda aplicação de consola que utiliza as assinaturas para executar operações no contentor e um blob.

> [!NOTE]
> Se mais de 24 horas passaram, uma vez que concluir a primeira parte do tutorial, as assinaturas gerou já não será válidas. Neste caso, deve executar o código na primeira aplicação de consola para gerar assinaturas de acesso partilhado raiz para utilização na segunda parte do tutorial.
>

No Visual Studio, crie uma nova aplicação de consola do Windows e dê-lhe nome **ConsumeSharedAccessSignatures**. Adicione as referências [configurationmanager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) e [Windowsazure](https://www.nuget.org/packages/WindowsAzure.Storage/), tal como fez anteriormente.

Na parte superior do ficheiro Program.cs, adicione o seguinte **utilizando** diretivas:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

No corpo do **main ()** método, adicione as seguintes constantes de cadeia, alterar os respetivos valores para as assinaturas de acesso partilhado gerado na parte 1 do tutorial.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Adicione um método para tentar operações de contentor utilizando uma assinatura de acesso partilhado
Em seguida, iremos adicionar um método que os testes de algumas operações de contentor com uma assinatura de acesso partilhado do contentor. A assinatura de acesso partilhado é utilizada para devolver uma referência para o contentor, autenticar o acesso ao contentor com base na assinatura individualmente.

Adicione o seguinte método para Program.cs:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Atualização do **main ()** método a chamar **UseContainerSAS()** com ambas as assinaturas de acesso partilhado que criou no contentor:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Adicione um método para tentar operações de BLOBs de utilizar uma assinatura de acesso partilhado
Por último, iremos adicionar um método que os testes de algumas operações de blob a utilizar uma assinatura de acesso partilhado no blob. Neste caso, utilizamos o construtor **CloudBlockBlob(String)**, passando na assinatura de acesso partilhado, para devolver uma referência para o blob. Não outra é necessária autenticação; baseia-se na assinatura individualmente.

Adicione o seguinte método para Program.cs:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Atualização do **main ()** método a chamar **UseBlobSAS()** com ambas as assinaturas de acesso partilhado que criou no blob:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Executar a aplicação de consola e observe o resultado para ver as operações são permitidas para as assinaturas. O resultado na janela da consola será semelhante ao seguinte:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Passos Seguintes

* [Assinaturas de acesso partilhado, parte 1: Compreender o modelo SAS](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Gerir o acesso de leitura anónimo a contentores e blobs](storage-manage-access-to-resources.md)
* [Delegar o acesso com uma assinatura de acesso partilhado (REST API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduzir tabela e fila SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
