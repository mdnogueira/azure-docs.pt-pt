---
title: Desenvolver para os ficheiros do Azure com Java | Microsoft Docs
description: "Saiba como desenvolver aplicações Java e serviços que utilizam ficheiros do Azure para armazenar dados de ficheiro."
services: storage
documentationcenter: java
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 8cd3698d4281b933881c45dfa5e7868bd7b0bdaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-files-with-java"></a>Desenvolver para os ficheiros do Azure com Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial irá demonstram as noções básicas da utilização de Java para desenvolver aplicações ou serviços que utilizam ficheiros do Azure para armazenar dados de ficheiro. Neste tutorial, iremos criar uma aplicação de consola simples e mostra como efetuar ações básicas com Java e ficheiros do Azure:

* Criar e eliminar as partilhas de ficheiros do Azure
* Criar e eliminar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros do Azure
* Carregar, transferir e eliminar um ficheiro

> [!Note]  
> Porque os ficheiros do Azure podem ser acedidos através de SMB, é possível escrever as aplicações simples que aceder à partilha de ficheiros do Azure utilizando as classes de e/s de Java padrão. Este artigo descreve como escrever as aplicações que utilizam o SDK de Java de armazenamento do Azure, que utiliza o [API REST da Azure ficheiros](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) para falar com ficheiros do Azure.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Para criar os exemplos, terá do Kit de desenvolvimento Java (JDK) e o [[SDK de armazenamento do Azure para Java]]. Deve também criou uma conta de armazenamento do Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar os ficheiros do Azure
Para utilizar as APIs de armazenamento do Azure, adicione a seguinte instrução na parte superior do ficheiro onde pretende aceder ao serviço de armazenamento de Java.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Para utilizar ficheiros do Azure, terá de ligar à sua conta do storage do Azure. É o primeiro passo configurar uma cadeia de ligação, que iremos utilizar para ligar à sua conta de armazenamento. Vamos definir uma variável estática fazê-lo.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Substitua os valores reais para a sua conta de armazenamento your_storage_account_name e your_storage_account_key.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Ligar a uma conta de armazenamento do Azure
Para ligar a sua conta do storage, tem de utilizar o **CloudStorageAccount** objeto, passar uma cadeia de ligação à respetiva **analisar** método.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** emite um InvalidKeyException, pelo que terá para colocá-la no interior de um bloco try/catch.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Todos os ficheiros e diretórios nos ficheiros de Azure residem num contentor chamado um **partilha**. A conta do storage pode ter partilhas tantos como permite a capacidade de conta. Para obter acesso a uma partilha e o respetivo conteúdo, tem de utilizar um cliente de ficheiros do Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Utilizando o cliente de ficheiros do Azure, em seguida, pode obter uma referência a uma partilha.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para criar efetivamente a partilha, utilize o **createIfNotExists** método do objeto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste momento, **partilhar** contém uma referência a uma partilha denominada **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Eliminar uma partilha de ficheiros do Azure
Eliminar uma partilha é feito chamando a **deleteIfExists** método num objeto CloudFileShare. Eis o código de exemplo que suporta que.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Criar um diretório
Também pode organizar armazenamento colocando os ficheiros secundárias diretórios em vez de ter todos eles no diretório de raiz. Ficheiros do Azure permite-lhe criar tantas diretórios como irá permitir a sua conta. O código abaixo, irá criar um subdiretório com o nome **sampledir** sob o diretório de raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Eliminar um diretório
Eliminar um diretório é uma tarefa bastante simple, embora deve ser salientado que não é possível eliminar um diretório que contiver ficheiros ou outros diretórios.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros do Azure
Obter uma lista de ficheiros e diretórios dentro de uma partilha facilmente é feito chamando **listFilesAndDirectories** numa referência CloudFileDirectory. O método devolve uma lista de objetos de ListFileItem que pode iterar. Por exemplo, o seguinte código irá listar ficheiros e diretórios dentro do diretório de raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Carregar um ficheiro
Um ficheiro de Azure partilha contém em muito menos, um diretório de raiz, onde podem residir os ficheiros. Nesta secção, irá aprender como carregar um ficheiro a partir do armazenamento local para o diretório de raiz de uma partilha.

O primeiro passo no carregamento de um ficheiro é para obter uma referência para o diretório onde deve residir. Fazê-lo ao chamar o **getRootDirectoryReference** método do objeto de partilha.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Agora que tem uma referência para o diretório de raiz da partilha, pode carregar um ficheiro no mesmo utilizando o seguinte código.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Transferir um ficheiro
Uma das operações mais frequentes que será executa relativamente aos ficheiros do Azure é transferir ficheiros. No exemplo seguinte, o código transfere SampleFile.txt e apresenta o respetivo conteúdo.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Há outra operação de ficheiros do Azure comum é a eliminação de ficheiros. O seguinte código elimina um ficheiro denominado SampleFile.txt armazenados dentro de um diretório com o nome **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Passos seguintes
Se quiser obter mais informações sobre outras APIs de armazenamento do Azure, siga estas ligações.

* [Do Azure para programadores de Java](/java/azure)/)
* [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
* [Armazenamento do Azure SDK para Android](https://github.com/azure/azure-storage-android)
* [Referência do SDK de cliente de armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md)
* [Resolução de problemas de Ficheiros do Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)