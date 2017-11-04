---
title: "Guia de introdução do Azure - transferência de objetos de armazenamento de Blobs do Azure utilizando Java / | Microsoft Docs"
description: "Saiba rapidamente a transferência de objetos do Blob storage do Azure utilizando Java"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 4c917a500e8d230c5b9885d9c0a96424201588f7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Transferência de objetos de armazenamento de Blobs do Azure utilizando Java /

Este guia de introdução, irá aprender a utilizar o Java para carregar, transfira e lista de blobs de blocos num contentor de Blob storage do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar um IDE com integração do Maven

* Em alternativa, instalar e configurar o Maven para trabalhar a partir da linha de comandos

Este tutorial utiliza [Eclipse](http://www.eclipse.org/downloads/) com a configuração de "Eclipse IDE para Java programadores".

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

O [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-java-quickstart) utilizado neste início rápido é uma aplicação de consola básico. 

Utilize [git](https://git-scm.com/) para transferir uma cópia da aplicação para o ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Este comando clones o repositório para a pasta de local git. Para abrir o projeto, inicie o Eclipse e fechar o ecrã de boas-vindas. Selecione **ficheiro** , em seguida, **abrir projetos a partir do sistema de ficheiros...** . Certifique-se **detetar e configurar natures projeto** está marcada. Selecione **diretório** , em seguida, navegue até à localização onde armazenou o repositório clonado, dentro de, selecione o **javaBlobsQuickstart** pasta. Certifique-se de que o **javaBlobsQuickstarts** projeto é apresentado como um projeto Eclipse, em seguida, selecione **concluir**.

Depois do projeto de conclusão da importação, abra **AzureApp.java** (localizado na **blobQuickstart.blobAzureApp** dentro do **src/main/java**) e substitua o `accountname`e `accountkey` dentro do `storageConnectionString` cadeia. Em seguida, execute a aplicação.
     

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
    
Na aplicação, tem de fornecer a cadeia de ligação para a sua conta de armazenamento. Abra o **AzureApp.Java** ficheiro. Localizar o `storageConnectionString` variável. Substitua o `AccountName` e `AccountKey` valores na cadeia de ligação com os valores que guardou no portal do Azure. O `storageConnectionString` deve ter um aspeto semelhante ao seguinte:

```java
    public static final String storageConnectionString ="DefaultEndpointsProtocol=https;" +
     "AccountName=<Namehere>;" +
    "AccountKey=<Keyhere>";
```

## <a name="run-the-sample"></a>Executar o exemplo

Este exemplo cria um ficheiro de teste no seu diretório predefinido (os meus documentos, para os utilizadores do windows), esta carrega-o para o Blob storage, apresenta uma lista de blobs no contentor, em seguida, transfere o ficheiro com um novo nome, pelo que pode comparar os antigos e novos ficheiros. 

Executar o exemplo premindo **Ctrl + F11** no Eclipse.

Se pretender executar o exemplo com o Maven na linha de comandos, abra uma shell e navegue para **blobAzureApp** dentro do seu diretório clonado. Em seguida, introduza `mvn compile exec:java`.

Segue-se um exemplo de saída se pretendesse executar a aplicação no Windows.

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 Antes de continuar, verifique o seu diretório predefinido (os meus documentos, para os utilizadores do windows) para os dois ficheiros. Pode abrir e ver que se são idênticos. Copie o URL para o blob fora da janela de consola e cole-o num browser para ver os conteúdos do ficheiro no armazenamento de Blobs. Ao premir a tecla enter, elimina o contentor de armazenamento e os ficheiros.

Também pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver os ficheiros no Blob storage. Explorador de armazenamento do Azure é uma ferramenta de plataforma livre que lhe permite aceder às suas informações de conta de armazenamento. 

Depois de verificar que os ficheiros, prima a tecla enter para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que faz o exemplo, abra o **AzureApp.java** ficheiro para ver o código. 

## <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

A primeira coisa a fazer é criar as referências a objectos utilizados para aceder e gerir o armazenamento de Blobs. Criar estes objetos entre si – cada um é utilizada pela seguinte na lista.

* Criar uma instância do **CloudStorageAccount** objeto apontar para o [conta de armazenamento](/java/api/com.microsoft.azure.management.storage._storage_account). 

* Criar uma instância do **CloudBlobClient** objeto, que aponta para o [serviço Blob](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) na sua conta de armazenamento. 

* Criar uma instância do **CloudBlobContainer** objeto que representa o [contentor](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) estão a aceder. Contentores são utilizados para organizar os blobs como utilizar as pastas no seu computador para organizar os seus ficheiros.

Assim que tiver o **CloudBlobContainer**, pode criar uma instância do **CloudBlockBlob** objeto que aponta para o específicos [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) no qual está interessado, e efetue um carregamento, transferência, copiar, operação etc.

> [!IMPORTANT]
> Os nomes de contentor tem de ser em minúsculas. Consulte [nomenclatura e referência de contentores, Blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) para obter mais informações sobre os nomes de contentor e BLOBs.

Nesta secção, criar uma instância dos objetos, criar um novo contentor e, em seguida, definir as permissões no contentor para que os blobs são públicos e podem ser acedidos com apenas um URL. O contentor é designado por **quickstartblobs**. 

Este exemplo utiliza **CreateIfNotExists** uma vez que queremos criar um novo contentor sempre que a amostra é executada. Num ambiente de produção onde utiliza o mesmo contentor ao longo de uma aplicação, é melhor prática só é possível chamar **CreateIfNotExists** depois. Em alternativa, pode criar o contentor antecedência, por isso não terá de criá-la no código.

```java
CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
        
CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

// Get a reference to a container.
// The container name must be lower case
CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

// Create the container if it does not exist.
container.createIfNotExists();

// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-blobs-to-the-container"></a>Carregar os blobs no contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados mais frequentemente e que é o que é utilizado neste guia de introdução. 

Para carregar um ficheiro para um blob, obtenha uma referência para o blob no contentor de destino. Assim que tiver a referência de blob, pode carregar dados para a mesma utilizando [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Esta operação cria o blob caso ainda não existir ou substitui-lo se já existir.

O código de exemplo cria um ficheiro local para ser utilizado para o carregamento e transferência, armazenar o ficheiro a carregar como **origem** e o nome do blob no **blob**. O exemplo seguinte carrega o ficheiro para o contentor denominado **quickstartblobs**.

```java
//Getting the path to user's myDocuments folder
String myDocs = FileSystemView.getFileSystemView().getDefaultDirectory().getPath();

//Creating a file in the myDocuments folder
File source = new File(myDocs + File.separator + "results.txt");
try( Writer output = new BufferedWriter(new FileWriter(source)))
{
    System.out.println("Location of file: " + source.toString());

    output.write("Hello Azure!");
    output.close();
    
    System.out.println("File has been written");
}
catch(IOException x) 
{
    System.err.println(x);
}

//Getting blob reference
CloudBlockBlob blob = container.getBlockBlobReference("results.txt");

//Creating a FileInputStream as it is necessary for the upload
FileInputStream myFile = new FileInputStream(source);

//Creating blob and uploading file to it
blob.upload( myFile, source.length());

//Closing FileInputStream
myFile.close();
```

Existem vários métodos de carregamento que pode utilizar com o Blob storage. Por exemplo, se tiver uma sequência de memória, pode utilizar o método UploadFromStreamAsync em vez do UploadFromFileAsync. 

Os blobs de blocos podem ser qualquer tipo de ficheiro binário ou de texto. Os blobs de páginas são utilizados principalmente para os ficheiros VHD utilizados para fazer uma cópia de VMs de IaaS. Acrescentar blobs são utilizados para registo, por exemplo, se pretender escrever um ficheiro e, em seguida, mantenha a adição de mais informações. A maioria dos objetos armazenados no Blob storage são blobs de blocos.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de ficheiros no contentor com [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). O código seguinte obtém a lista de blobs, em seguida, repetido ao longo, que mostra os URIs dos blobs encontrados. Pode copiar o URI a partir da janela de comando e cole-o num browser para ver o ficheiro.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

## <a name="download-blobs"></a>Transferir blobs

Transferir blobs para a sua utilização de disco local [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

O código seguinte transfere o blob carregado numa secção anterior, adicionar um sufixo de "_DOWNLOADED" para o nome do blob para que possa ver ambos os ficheiros no disco local. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in your default directory.
blob.downloadToFile(myDocs + File.separator + "results_DOWNLOADED.txt");
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de blobs carregados este início rápido, pode eliminar o contentor inteiro utilizando [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Isto também elimina os ficheiros no contentor.

```java
//Deletes container if it exists then deletes files created locally
container.deleteIfExists();
downloadedFile.deleteOnExit();
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como transferir ficheiros entre um disco local e o Blob storage do Azure utilizando Java. Para obter mais informações sobre como trabalhar com armazenamento de BLOBs, continue para o Blob storage procedimentos.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de BLOBs](storage-java-how-to-use-blob-storage.md)

Para obter mais informações sobre o Explorador de armazenamento e Blobs, consulte [recursos de armazenamento de Blobs do Azure de gerir com o Explorador de armazenamento](../../vs-azure-tools-storage-explorer-blobs.md).