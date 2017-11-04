---
title: 'Tutorial: Encriptar e desencriptar blobs no armazenamento do Azure com o Cofre de chaves do Azure | Microsoft Docs'
description: "Como encriptar e desencriptar um blob a utilizar a encriptação do lado do cliente para armazenamento do Microsoft Azure com o Cofre de chaves do Azure."
services: storage
documentationcenter: 
author: adhurwit
manager: jasonsav
editor: tysonn
ms.assetid: 027e8631-c1bf-48c1-9d9b-f6843e88b583
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 01/23/2017
ms.author: adhurwit
ms.openlocfilehash: fc4286b39ade5558a9dabd5832be05a7a0d6f0c7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Tutorial: Encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Cofre de chaves do Azure
## <a name="introduction"></a>Introdução
Este tutorial abrange como tornar a utilização de encriptação de armazenamento do lado do cliente com o Cofre de chaves do Azure. -Explica como encriptar e desencriptar um blob numa aplicação de consola utilização destas tecnologias.

**Tempo estimado para concluir:** 20 minutos

Para obter informações gerais sobre o Cofre de chaves do Azure, consulte [que é o Cofre de chaves do Azure?](../../key-vault/key-vault-whatis.md).

Para obter informações gerais sobre a encriptação do lado do cliente para o Storage do Azure, consulte [encriptação do lado do cliente e o Cofre de chaves do Azure para armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, deve ter o seguinte:

* Uma conta de armazenamento do Azure
* Visual Studio 2013 ou posterior
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Descrição geral de encriptação do lado do cliente
Para obter uma descrição geral de encriptação do lado do cliente para o Storage do Azure, consulte [encriptação do lado do cliente e o Cofre de chaves do Azure para armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Eis uma breve descrição como funciona a encriptação do lado do cliente:

1. O SDK do cliente do Storage do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica one time utilização.
2. Dados de cliente são encriptados utilizando este CEK.
3. O CEK, em seguida, é moldado (encriptado) utilizando a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerida localmente ou armazenada no Cofre de chaves do Azure. O cliente do armazenamento nunca tem acesso para a KEK. Apenas invoca o algoritmo de chave de encapsulamento de aplicações fornecido pelo Cofre de chaves. Os clientes podem optar por utilizar fornecedores personalizados para a chave de encapsulamento de aplicações/abertura se pretenderem.
4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure.

## <a name="set-up-your-azure-key-vault"></a>Configurar o seu Cofre de chaves do Azure
Para continuar com este tutorial, terá de efetuar os passos seguintes, que são descritos no tutorial [introdução ao Cofre de chaves do Azure](../../key-vault/key-vault-get-started.md):

* Crie um cofre de chaves.
* Adicione uma chave ou segredo ao Cofre de chaves.
* Registe uma aplicação no Azure Active Directory.
* Autorize a aplicação a utilizar a chave ou segredo.

Anote o ClientID e ClientSecret que foram gerados ao registar uma aplicação no Azure Active Directory.

Crie ambas as chaves no Cofre de chaves. Partimos do pressuposto para o resto do tutorial que utiliza os seguintes nomes: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar uma aplicação de consola com pacotes e AppSettings
No Visual Studio, crie uma nova aplicação de consola.

Adicione pacotes nuget necessárias na consola do Gestor de pacotes.

```
Install-Package WindowsAzure.Storage

// This is the latest stable release for ADAL.
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adicione AppSettings para o App. config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adicione o seguinte `using` diretivas e certifique-se de adicionar uma referência System ao projeto.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;        
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicione um método para obter um token para a aplicação de consola
O método seguinte é utilizado pelas classes do Cofre de chaves que têm de autenticar para o acesso ao seu Cofre de chaves.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        ConfigurationManager.AppSettings["clientId"],
        ConfigurationManager.AppSettings["clientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-storage-and-key-vault-in-your-program"></a>Armazenamento de acesso e o Cofre de chaves no seu programa
Na função principal, adicione o seguinte código.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    ConfigurationManager.AppSettings["accountName"],
       ConfigurationManager.AppSettings["accountKey"]);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modelos de objetos do Cofre de chaves
> 
> É importante compreender que existem, na verdade, duas Cofre de chaves modelos de objetos a ter em consideração: um baseia-se na API REST (espaço de nomes do KeyVault) e o outro é uma extensão para a encriptação do lado do cliente.
> 
> O cliente do Cofre de chave interage com a API REST e compreende JSON Web chaves e segredos dos dois tipos de ações que estão incluídas no Cofre de chaves.
> 
> As extensões do Cofre de chave são as classes que parecem criadas especificamente para a encriptação do lado do cliente no armazenamento do Azure. Contêm uma interface de chaves (IKey) e classes com base no conceito de uma resolução de chave. Existem duas implementações do IKey que precisa de saber: RSAKey e SymmetricKey. Agora acontecer operação com as coisas que estão incluídas no Cofre de chaves, mas neste momento estão classes independentes (para a chave e o segredo obtido pelo cliente do Cofre de chave não implementam IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Encriptar blob e carregue
Adicione o seguinte código para encriptar um blob e carregue-o à sua conta de armazenamento do Azure. O **ResolveKeyAsync** método que é utilizado devolve um IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
// Remember that we used the names contosokeyvault and testrsakey1.
var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

Segue-se uma captura de ecrã do [Portal clássico do Azure](https://manage.windowsazure.com) para um blob que foram encriptado através de encriptação do lado do cliente com uma chave armazenada num cofre de chaves. O **KeyId** propriedade é o URI para a chave no Cofre de chaves que age como o KEK. O **EncryptedKey** propriedade contém a versão encriptada do CEK.

![Captura de ecrã que mostra os metadados do Blob que inclui os metadados de encriptação](./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png)

> [!NOTE]
> Se observar o construtor de BlobEncryptionPolicy, verá que possa aceitar uma chave de e/ou de uma resolução. Tenha em atenção que agora não é possível utilizar uma resolução para a encriptação porque faz atualmente não suportam uma chave de predefinição.
> 
> 

## <a name="decrypt-blob-and-download"></a>Desencriptar BLOBs e transferir
Desencriptação é realmente o utilizador quando utilizar as classes de resolução sentido. O ID da chave utilizada para encriptação está associado com o blob nos respetivos metadados, pelo que não existe nenhuma razão para obter a chave e lembre-se a associação entre a chave e BLOBs. Tem de certificar-se de que a chave permanece no Cofre de chaves.   

A chave privada de uma chave RSA permanece no Cofre de chaves para desencriptação ocorrer, a chave encriptada os metadados do blob que contém o CEK é enviada ao Cofre de chaves de desencriptação.

Adicione o seguinte para desencriptar o blob que acabou de carregar.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Existem alguns dos outros tipos de resoluções para facilitar a gestão de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Utilize os segredos do Cofre de chaves
A forma de utilizar um segredo com encriptação do lado do cliente é através da classe de SymmetricKey porque um segredo é, essencialmente, uma chave simétrica. No entanto, conforme indicado acima, um segredo no Cofre de chaves não está mapeado exatamente a um SymmetricKey. Existem alguns aspetos a compreender:

* A chave num SymmetricKey tem de ser um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
* A chave num SymmetricKey deve ser codificado em Base64.
* Um segredo do Cofre de chaves que será utilizado como um SymmetricKey tem de ter um tipo de conteúdo de "application/octet-stream" no Cofre de chaves.

Eis um exemplo do PowerShell de criar um segredo no Cofre de chaves que podem ser utilizados como uma SymmetricKey.
Tenha em atenção que o valor hard-coded, $key, destina-se apenas a fins de demonstração. No seu próprio código poderá ser útil gerar esta chave.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Na sua aplicação de consola, pode utilizar a mesma chamada como antes de obter este segredo como um SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
E já está. Divirta-se!

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como utilizar o armazenamento do Microsoft Azure com c#, consulte [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para obter mais informações sobre a API de REST do Blob, consulte [API de REST do serviço Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para informações mais recentes no armazenamento do Microsoft Azure, visite o [blogue de equipa de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/).
