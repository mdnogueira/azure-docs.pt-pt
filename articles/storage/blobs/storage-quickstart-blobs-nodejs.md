---
title: "Guia de introdução do Azure - transferência de objetos de armazenamento de Blobs do Azure utilizando Node.js| / Microsoft Docs"
description: "Saiba rapidamente a transferência de objetos do Blob storage do Azure com o Node.js"
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: 9ea7f77d3bbe45de49c798fe3d51151e1a5a6658
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Transferência de objetos para/de Blob storage do Azure com o Node.js

Este guia de introdução, irá aprender a utilizar o Node.js para carregar, transfira e lista de blobs de blocos num contentor de Blob storage do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar o [Node. js](https://nodejs.org/en/)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

O [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) utilizado neste início rápido é uma aplicação de consola básico. 

Utilize [git](https://git-scm.com/) para transferir uma cópia da aplicação para o ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Este comando clones o repositório para a pasta de local git. Para abrir o aspeto da aplicação para a pasta de armazenamento-blobs-nó-guia de introdução, abra-o e faça duplo clique no index.js.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Na aplicação, tem de fornecer a cadeia de ligação para a sua conta de armazenamento. Abra o `index.js` ficheiro, localize o `connectionString` variável. Substitua o valor com o valor da cadeia de ligação com o que guardou no portal do Azure completo. A cadeia de ligação de armazenamento deve ter um aspeto semelhante ao seguinte:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Instalar pacotes necessários

No diretório de aplicação executado `npm install` instalar qualquer necessário pacotes listados o `package.json` ficheiro.

```javascript
npm install
```

## <a name="run-the-sample"></a>Executar o exemplo

Este exemplo cria um ficheiro de teste nos meus documentos, esta carrega-o para o Blob storage, apresenta uma lista de blobs no contentor, em seguida, transfere o ficheiro com um novo nome, pelo que pode comparar os antigos e novos ficheiros.

Executar o exemplo escrevendo `node index.js`. É o seguinte resultado de um sistema Windows.  Pode ser esperado um resultado semelhante com caminhos de ficheiros adequado se utilizar o Linux.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Antes de continuar, verifique MyDocuments para os dois ficheiros. Pode abrir e ver que se são idênticos.

Também pode utilizar uma ferramenta como o [Explorador de armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver os ficheiros no Blob storage. Explorador de armazenamento do Azure é uma ferramenta de plataforma livre que lhe permite aceder às suas informações de conta de armazenamento.

Depois de verificar que os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que faz o exemplo, abra o ficheiro de index.js para ver o código. 

## <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

A primeira coisa a fazer é criar a referência para o `BlobService` utilizado para aceder e gerir o armazenamento de Blobs. Criar estes objetos entre si – cada um é utilizada pela seguinte na lista.

* Criar uma instância do  **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)**  objeto, que aponta para o serviço Blob na sua conta de armazenamento.

* Criar um novo contentor e, em seguida, definir as permissões no contentor para que os blobs são públicos e podem ser acedidos com apenas um URL. O contentor começa com **quickstartcontainer -**.

Este exemplo utiliza [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) uma vez que queremos criar um novo contentor sempre que a amostra é executada. Num ambiente de produção onde utiliza o mesmo contentor ao longo de uma aplicação, é melhor prática só é possível chamar CreateIfNotExists uma vez. Em alternativa, pode criar o contentor antecedência, por isso não terá de criá-la no código.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

## <a name="upload-blobs-to-the-container"></a>Carregar os blobs no contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são mais frequentemente utilizados. Estes são ideais para armazenar o texto e os dados binários, que é o motivo pelo qual que são utilizados neste guia de introdução.

Para carregar um ficheiro para um blob, pode utilizar o [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) método. Esta operação cria o blob caso ainda não existir ou substitui-lo se já existir.

O código de exemplo cria um ficheiro local para ser utilizado para o carregamento e transferência, armazenar o ficheiro a carregar como **localPath** e o nome do blob no **localFileToUpload**. O exemplo seguinte carrega o ficheiro para o contentor que começam com **quickstartcontainer -**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Existem vários métodos de carregamento que pode utilizar com o Blob storage. Por exemplo, se tiver uma sequência de memória, pode utilizar o [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) método vez [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Em seguida, a aplicação obtém uma lista de ficheiros no contentor com [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). O código seguinte obtém a lista de blobs, em seguida, repetido ao longo, que mostra os URIs dos blobs encontrados. Pode copiar o URI a partir da janela de comando e cole-o num browser para ver o ficheiro.

Se tiver de 5000 ou menos blobs no contentor, todos os nomes dos BLOBs são obtidos numa chamada para [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Se tiver mais de 5.000 blobs no contentor, o serviço obtém a lista de conjuntos de 5000 até que todos os nomes dos BLOBs foram obtidos. A primeira é chamado esta API, devolve os nomes de 5000 blob primeiro e um token de continuação. Na segunda vez, que fornece o token, o serviço obtém o seguinte conjunto de nomes de blob e assim sucessivamente, até que o token de continuação tem o valor null, que indica que todos os nomes dos BLOBs foram obtidos.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

## <a name="download-blobs"></a>Transferir blobs

Transferir blobs para a sua utilização de disco local [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

O código seguinte transfere o blob carregado numa secção anterior, adicionar um sufixo de "_DOWNLOADED" para o nome do blob para que possa ver ambos os ficheiros no disco local. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de blobs carregados este início rápido, pode eliminar o contentor inteiro utilizando [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) e [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Também elimine os ficheiros criados se já não são necessárias. Isto está Tratado na aplicação ao premir introduza para sair da aplicação.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como transferir ficheiros entre um disco local e o Blob storage do Azure com o Node.js. Para obter mais informações sobre como trabalhar com armazenamento de BLOBs, continue para o Blob storage procedimentos.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de BLOBs](storage-nodejs-how-to-use-blob-storage.md)

Para obter mais informações sobre o Explorador de armazenamento e Blobs, consulte [recursos de armazenamento de Blobs do Azure de gerir com o Explorador de armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
