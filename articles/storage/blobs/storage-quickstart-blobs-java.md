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
ms.openlocfilehash: b096b9d79c049d8659a4171a0cbb42a99e245776
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
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
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
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

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

A primeira coisa a fazer é criar as referências a objectos utilizados para aceder e gerir o armazenamento de Blobs. Criar estes objetos entre si – cada um é utilizada pela seguinte na lista.

* Criar uma instância do **CloudStorageAccount** objeto apontar para o [conta de armazenamento](/java/api/com.microsoft.azure.management.storage._storage_account).

O **CloudStorageAccount** objeto é uma representação da conta de armazenamento e permite-lhe definir e aceder às propriedades da conta de armazenamento através de programação. Utilizar o **CloudStorageAccount** objeto pode criar uma instância do **CloudBlobClient**, que é necessário para aceder ao serviço blob.

* Criar uma instância do **CloudBlobClient** objeto, que aponta para o [serviço Blob](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) na sua conta de armazenamento.

O **CloudBlobClient** fornece-lhe um ponto de acesso ao serviço blob, permitindo-lhe definir e aceder às propriedades de armazenamento de BLOBs através de programação. Utilizar o **CloudBlobClient** pode criar uma instância do **CloudBlobContainer** objeto, que é necessário para criar contentores.

* Criar uma instância do **CloudBlobContainer** objeto que representa o [contentor](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) estão a aceder. Contentores são utilizados para organizar os blobs como utilizar as pastas no seu computador para organizar os seus ficheiros.    

Assim que tiver o **CloudBlobContainer**, pode criar uma instância do **CloudBlockBlob** objeto que aponta para o específicos [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) no qual está interessado, e efetue um carregamento, transferência, copiar, operação etc.

> [!IMPORTANT]
> Os nomes de contentor tem de ser em minúsculas. Consulte [nomenclatura e referência de contentores, Blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) para obter mais informações sobre os nomes de contentor e BLOBs.

### <a name="create-a-container"></a>Criar um contentor 

Nesta secção, criar uma instância dos objetos, criar um novo contentor e, em seguida, definir as permissões no contentor para que os blobs são públicos e podem ser acedidos com apenas um URL. O contentor é designado por **quickstartblobs**. 

Este exemplo utiliza **CreateIfNotExists** uma vez que queremos criar um novo contentor sempre que a amostra é executada. Num ambiente de produção onde utiliza o mesmo contentor ao longo de uma aplicação, é melhor prática só é possível chamar **CreateIfNotExists** depois. Em alternativa, pode criar o contentor antecedência, por isso não terá de criá-la no código.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Carregar os blobs no contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados mais frequentemente e que é o que é utilizado neste guia de introdução. 

Para carregar um ficheiro para um blob, obtenha uma referência para o blob no contentor de destino. Assim que tiver a referência de blob, pode carregar dados para a mesma utilizando [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Esta operação cria o blob caso ainda não existir ou substitui-lo se já existir.

O código de exemplo cria um ficheiro local para ser utilizado para o carregamento e transferir. armazenar o ficheiro a carregar como **origem** e o nome do blob no **blob**. O exemplo seguinte carrega o ficheiro para o contentor denominado **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Existem vários [carregar métodos](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) que pode utilizar com o Blob storage. Por exemplo, se tiver uma cadeia, pode utilizar o método UploadText em vez do método de carregamento. 

Os blobs de blocos podem ser qualquer tipo de ficheiro binário ou de texto. Os blobs de páginas são utilizados principalmente para os ficheiros VHD utilizados para fazer uma cópia de VMs de IaaS. Acrescentar blobs são utilizados para registo, por exemplo, se pretender escrever um ficheiro e, em seguida, mantenha a adição de mais informações. A maioria dos objetos armazenados no Blob storage são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de ficheiros no contentor com [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). O código seguinte obtém a lista de blobs, em seguida, repetido ao longo, que mostra os URIs dos blobs encontrados. Pode copiar o URI a partir da janela de comando e cole-o num browser para ver o ficheiro.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Transferir blobs

Transferir blobs para a sua utilização de disco local [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

O código seguinte transfere o blob carregado numa secção anterior, adicionar um sufixo de "_DOWNLOADED" para o nome do blob para que possa ver ambos os ficheiros no disco local. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de blobs carregados este início rápido, pode eliminar o contentor inteiro utilizando [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Isto também elimina os ficheiros no contentor.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como transferir ficheiros entre um disco local e o Blob storage do Azure utilizando Java. Para obter mais informações sobre como trabalhar com armazenamento de BLOBs, continue para o Blob storage procedimentos.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de BLOBs](storage-java-how-to-use-blob-storage.md)

Para obter mais informações sobre o Explorador de armazenamento e Blobs, consulte [recursos de armazenamento de Blobs do Azure de gerir com o Explorador de armazenamento](../../vs-azure-tools-storage-explorer-blobs.md).