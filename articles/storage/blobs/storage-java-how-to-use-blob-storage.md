---
title: Como utilizar o Blob storage do Azure (armazenamento de objetos) de Java | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: java
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 2e223b38-92de-4c2f-9254-346374545d32
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 91ef09916dbb587305572ea640fb4408ea9aebb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-java"></a>Como utilizar o Blob Storage do Java
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Descrição geral
O Blob Storage do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos.

Este artigo irá mostrar como efetuar cenários comuns utilizando o armazenamento de Blobs do Microsoft Azure. Os exemplos são escritos em Java e utilize o [SDK de armazenamento do Azure para Java][Azure Storage SDK for Java]. Os cenários abrangidos incluem **carregar**, **listagem**, **transferir**, e **eliminar** blobs. Para obter mais informações sobre os blobs, consulte o [passos](#Next-Steps) secção.

> [!NOTE]
> Um SDK está disponível para programadores que estiver a utilizar o armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte o [SDK de armazenamento do Azure para Android][Azure Storage SDK for Android].
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Neste artigo, irá utilizar as funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação Java localmente ou numa código em execução numa função da web ou função de trabalho no Azure.

Para tal, terá de instalar o Kit de desenvolvimento Java (JDK) e criar uma conta de armazenamento do Azure na sua subscrição do Azure. Assim que tiver feito, terá de verificar se o sistema de desenvolvimento cumpre os requisitos mínimos e as dependências que são apresentadas no [SDK de armazenamento do Azure para Java] [ Azure Storage SDK for Java] repositório no GitHub. Se o seu sistema cumpre os requisitos, pode seguir as instruções para transferir e instalar as bibliotecas de armazenamento do Azure para Java no seu sistema desse repositório. Depois de concluir essas tarefas, poderá criar uma aplicação de Java que utiliza os exemplos neste artigo.

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de BLOBs
Adicione as seguintes declarações de importação na parte superior do ficheiro de Java onde pretende que utilizem as APIs de armazenamento do Azure para aceder aos blobs.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Storage do Azure
Um cliente do Storage do Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e credenciais para aceder aos serviços de gestão de dados. Quando em execução numa aplicação de cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, com o nome da sua conta de armazenamento e a chave de acesso primária para a conta de armazenamento indicados no [portal do Azure](https://portal.azure.com) para o *AccountName* e *AccountKey* valores. O exemplo seguinte mostra como podem declarar um campo estático para conter a cadeia de ligação.

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Na execução aplicação dentro de uma função no Microsoft Azure, esta cadeia pode ser armazenada no ficheiro de configuração do serviço, *serviceconfiguration. Cscfg*e pode ser acedido através de uma chamada para o **RoleEnvironment.getConfigurationSettings** método. O exemplo seguinte obtém a cadeia de ligação de um **definição** elemento com o nome *StorageConnectionString* no ficheiro de configuração do serviço.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="create-a-container"></a>Criar um contentor
A **CloudBlobClient** objeto permite-lhe obter objetos da referência de contentores e blobs. O código seguinte cria um **CloudBlobClient** objeto.

> [!NOTE]
> Existem formas adicionais para criar **CloudStorageAccount** objetos; para obter mais informações, consulte **CloudStorageAccount** no [referência do SDK de cliente de armazenamento de Azure].
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Utilize o **CloudBlobClient** objeto para obter uma referência ao contentor que pretende utilizar. Pode criar o contentor, se não existir com o **createIfNotExists** método, o que caso contrário, irá devolver o contentor existente. Por predefinição, o novo contentor é privado, pelo que tem de especificar a chave de acesso de armazenamento (tal como fez anteriormente) para transferir blobs deste contentor.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>Opcional: Configurar um contentor para acesso público
Permissões de um contentor estão configuradas para acesso privado por predefinição, mas pode facilmente configurar permissões de um contentor para permitir o acesso público só de leitura para todos os utilizadores na Internet:

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Para carregar um ficheiro para um blob, obtenha uma referência de contentor e utilizá-la para obter uma referência de blob. Depois de ter uma referência de blob, pode carregar qualquer fluxo chamando carregamento na referência de blob. Esta operação irá criar o blob, se não existir ou substituí-lo se existir. O exemplo de código seguinte mostra esse e parte do princípio de que o contentor já foi criado.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor como fez para carregar um blob. Pode utilizar o contentor **listBlobs** método com um **para** ciclo. O seguinte código produz o Uri de cada blob num contentor para a consola.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Tenha em atenção que pode atribuir o nome blobs com as informações de caminho os respetivos nomes. Isto cria uma estrutura de diretório virtual que pode organizar e percorrer tal como faria com um sistema de ficheiros tradicional. Tenha em atenção que a estrutura de diretório é apenas virtual – os únicos recursos disponíveis no Blob Storage são contentores e blobs. No entanto, a biblioteca de cliente oferece uma **CloudBlobDirectory** objeto para fazer referência a um diretório virtual e simplificar o processo de trabalhar com blobs que se encontram organizados desta forma.

Por exemplo, pode ter um contentor com o nome "fotografias", na qual pode carregar blobs com o nome "rootphoto1", "2010/photo1", "2010/photo2" e "2011/photo1". Isto iria criar os diretórios virtuais "2010" e "2011" dentro do contentor "photos". Quando chamar **listBlobs** no contentor "photos", a coleção devolvida irá conter **CloudBlobDirectory** e **CloudBlob** objetos que representam os diretórios e os blobs contidos no nível superior. Neste caso, seria devolvidas diretórios "2010" e "2011", bem como fotografia "rootphoto1". Pode utilizar o **instanceof** operador para distinguir estes objetos.

Opcionalmente, pode passar parâmetros para o **listBlobs** método com o **useFlatBlobListing** parâmetro definido como verdadeiro. Este procedimento resultará em todos os BLOBs devolvido, independentemente do diretório. Para obter mais informações, consulte **CloudBlobContainer.listBlobs** no [referência do SDK de cliente de armazenamento de Azure].

## <a name="download-a-blob"></a>Transferir um blob
Para transferir blobs, siga os mesmos passos, tal como fez para carregar um blob para obter uma referência de blob. No exemplo carregamento, chamado carregamento no objeto de blob. No exemplo seguinte, chamar transferência para transferir os conteúdos do blob para um objeto de fluxo, tal como um **FileOutputStream** que pode utilizar para manter o blob para um ficheiro local.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>Eliminar um blob
Para eliminar um blob, obter uma referência de blob e chamada **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>Eliminar um contentor do blob
Por fim, para eliminar um contentor de blob, obtenha um blob de referência de contentor e chamada **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do Blob storage, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

* [Armazenamento do Azure SDK para Java][Azure Storage SDK for Java]
* [Referência do SDK de cliente de armazenamento do Azure][referência do SDK de cliente de armazenamento de Azure]
* [API REST do Storage do Azure][Azure Storage REST API]
* [Blogue da equipa do Storage do Azure][Azure Storage Team Blog]

Para obter mais informações, consulte também [do Azure para programadores de Java](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[referência do SDK de cliente de armazenamento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
