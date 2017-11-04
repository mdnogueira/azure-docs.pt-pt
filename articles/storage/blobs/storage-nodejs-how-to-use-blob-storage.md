---
title: Como utilizar o Blob storage do Node.js | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos)."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: e52f38d5fb3c100e4275032f9a2a1234961c672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Como utilizar o Blob Storage do Node.js
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Descrição geral
Este artigo mostra como efetuar cenários comuns utilizando o Blob storage. Os exemplos são escritos através da API do Node.js. Os cenários abrangidos incluem como carregar, listar, transferir e eliminar blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Para obter instruções sobre como criar uma aplicação Node.js, consulte [criar uma aplicação de web de Node.js no App Service do Azure], [criar e implementar uma aplicação Node.js num serviço em nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) – com o Windows PowerShell, ou [criar e implementar uma aplicação web Node.js no Azure utilizando a Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para aceder ao armazenamento
Para utilizar o armazenamento do Azure, terá do SDK de armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST do storage.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o Gestor de pacote de nó (NPM) para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix), para navegar para a pasta onde criou o seu exemplo de aplicação.
2. Tipo **npm instalar storage do azure** na janela de comandos. Saída do comando é semelhante ao seguinte exemplo de código.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Pode executar manualmente o **ls** comando para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, localizar o **storage do azure** pacote, que contém as bibliotecas que precisa de aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Utilizar o bloco de notas ou noutro editor de texto, adicione o seguinte na parte superior do **server.js** ficheiro da aplicação em que pretende utilizar o armazenamento:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do Azure irá ler as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING`, para obter informações necessárias para ligar a sua conta do storage do Azure. Se estas variáveis de ambiente não estiver definidas, tem de especificar as informações de conta ao chamar **createBlobService**.

## <a name="create-a-container"></a>Criar um contentor
O **BlobService** objeto permite-lhe trabalhar com blobs e contentores. O código seguinte cria um **BlobService** objeto. Adicione o seguinte perto do topo da **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Pode aceder anonimamente um blob utilizando **createBlobServiceAnonymous** e fornecer o endereço do anfitrião. Por exemplo, utilizar `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Para criar um novo contentor, utilize **createContainerIfNotExists**. Exemplo de código seguinte cria um novo contentor designado 'mycontainer':

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Se o contentor é criado recentemente, `result.created` é verdadeiro. Se o contentor já existir, `result.created` é falso. `response`contém informações sobre a operação, incluindo as informações de ETag do contentor.

### <a name="container-security"></a>Segurança de contentor
Por predefinição, os novos contentores são privados e não podem ser acedidos anonimamente. Para tornar o contentor público, para que possam aceder anonimamente, pode definir acesso do contentor para **blob** ou **contentor**.

* **blob** -permite o acesso de leitura anónimo ao conteúdo de blob e metadados dentro neste contentor, mas não os metadados do contentor como listar todos os blobs num contentor
* **contentor** -permite o acesso de leitura anónimo ao conteúdo de blob e metadados, bem como metadados do contentor

Exemplo de código seguinte demonstra a definição de nível para o acesso **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

Em alternativa, pode modificar o nível de acesso de um contentor utilizando **setContainerAcl** para especificar o nível de acesso. Exemplo de código seguinte altera o nível de acesso ao contentor:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

O resultado contém informações sobre a operação, incluindo atual **ETag** para o contentor.

### <a name="filters"></a>Filtros
Pode aplicar opcionais operações de filtragem para operações executadas utilizando **BlobService**. Operações de filtragem pode incluir registo automaticamente repetir, etc. Os filtros são objetos que implementam um método com a assinatura:

```nodejs
function handle (requestOptions, next)
```

Depois de efetuar a respetiva pré-processamentos nas opções de pedido, o método tem de chamar "seguinte", transmitir uma chamada de retorno com a assinatura seguinte:

```nodejs
function (returnObject, finalCallback, next)
```

Esta chamada de retorno e após o processamento de returnObject (a resposta do pedido para o servidor), a chamada de retorno tem de invocar, em seguida, se existir para continuar a processar outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementa lógica de repetição são incluídos com o Azure SDK para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte cria um **BlobService** objeto que utiliza o **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Existem três tipos de blobs: blobs de blocos, blobs de páginas e blobs de acréscimo. Os blobs de blocos permitem que mais eficientemente, carregar dados de grandes dimensões. Acrescentar blobs estão otimizados para operações de acréscimo. Os blobs de página estão otimizados para operações de leitura/escrita. Para obter mais informações, consulte [compreender os Blobs de blocos, os Blobs de acréscimo e Blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Blobs de bloco
Carregar dados para um blob de blocos, utilize o seguinte:

* **createBlockBlobFromLocalFile** - cria um novo blob de bloco e carrega o conteúdo de um ficheiro
* **createBlockBlobFromStream** - cria um novo blob de bloco e carrega o conteúdo de um fluxo
* **createBlockBlobFromText** - cria um novo blob de bloco e carrega o conteúdo de uma cadeia
* **createWriteStreamToBlockBlob** -fornece uma sequência de escrita para um blob de bloco

Exemplo de código seguinte carrega o conteúdo do **test.txt** de ficheiros para **myblob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

O `result` devolvido por estes métodos contém informações sobre a operação, tais como o **ETag** do blob.

### <a name="append-blobs"></a>Blobs de acréscimo
Carregar dados para um novo blob de acréscimo, utilize o seguinte:

* **createAppendBlobFromLocalFile** - cria um novo blob de acréscimo e carrega o conteúdo de um ficheiro
* **createAppendBlobFromStream** - cria um novo blob de acréscimo e carrega o conteúdo de um fluxo
* **createAppendBlobFromText** - cria um novo blob de acréscimo e carrega o conteúdo de uma cadeia
* **createWriteStreamToNewAppendBlob** - cria um novo blob de acréscimo e, em seguida, fornece uma sequência de escrita

Exemplo de código seguinte carrega o conteúdo do **test.txt** de ficheiros para **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Acrescentar um bloco de para um blob de acréscimo existente, utilize o seguinte:

* **appendFromLocalFile** -acrescentar o conteúdo de um ficheiro para um blob de acréscimo existente
* **appendFromStream** -acrescentar o conteúdo de um fluxo para um blob de acréscimo existente
* **appendFromText** -acrescentar o conteúdo de uma cadeia para um blob de acréscimo existente
* **appendBlockFromStream** -acrescentar o conteúdo de um fluxo para um blob de acréscimo existente
* **appendBlockFromText** -acrescentar o conteúdo de uma cadeia para um blob de acréscimo existente

> [!NOTE]
> appendFromXXX APIs irá efetuar alguns validação do lado do cliente falhem fast evitar chamadas do desnecessários. appendBlockFromXXX não.
>
>

Exemplo de código seguinte carrega o conteúdo do **test.txt** de ficheiros para **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Blobs de páginas
Carregar dados para um blob de página, utilize o seguinte:

* **createPageBlob** -cria um novo blob de página com um comprimento específico
* **createPageBlobFromLocalFile** - cria um novo blob de página e carrega o conteúdo de um ficheiro
* **createPageBlobFromStream** - cria um novo blob de página e carrega o conteúdo de um fluxo
* **createWriteStreamToExistingPageBlob** -fornece uma sequência de escrita para um blob de página existente
* **createWriteStreamToNewPageBlob** - cria um novo blob de página e, em seguida, fornece uma sequência de escrita

Exemplo de código seguinte carrega o conteúdo do **test.txt** de ficheiros para **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Os blobs de páginas consistem de 512 bytes 'páginas'. Receberá um erro ao carregar dados com um tamanho que não é um múltiplo de 512.
>
>

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
Para listar os blobs num contentor, utilize o **listBlobsSegmented** método. Se gostaria de voltar a blobs com um prefixo específico, utilize **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

O `result` contém um `entries` coleção, o que é uma matriz de objetos que descrevem cada blob. Se todos os blobs não podem ser devolvidos, o `result` também fornece um `continuationToken`, que pode utilizar como o segundo parâmetro para obter entradas adicionais.

## <a name="download-blobs"></a>Transferir blobs
Para transferir dados de um blob, utilize o seguinte:

* **getBlobToLocalFile** -escreve os conteúdos do blob no ficheiro
* **getBlobToStream** -escreve os conteúdos do blob para uma transmissão em fluxo
* **getBlobToText** -escreve os conteúdos do blob numa cadeia
* **createReadStream** -fornece uma transmissão em fluxo para ler a partir do blob

Exemplo de código seguinte demonstra a utilização **getBlobToStream** para transferir os conteúdos do **myblob** blob e armazená-las para o **output.txt** ficheiros utilizando uma sequência:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

O `result` contém informações sobre o blob, incluindo **ETag** informações.

## <a name="delete-a-blob"></a>Eliminar um blob
Por fim, para eliminar um blob, chame **deleteBlob**. Exemplo de código seguinte elimina o blob com o nome **myblob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Acesso simultâneo
Para suportar o acesso em simultâneo para um blob de vários clientes ou de múltiplas instâncias do processo, pode utilizar **ETags** ou **concessões**.

* **ETag** -fornece uma forma de detetar que o blob ou contentor foi modificado por outro processo
* **Concessão** -fornece uma forma de obter exclusivo, renováveis, escrever ou eliminar o acesso a um blob para um período de tempo

### <a name="etag"></a>ETag
Utilize ETags se precisar de permitir que várias instâncias ou clientes ao escrever para o bloco Blob ou página Blob em simultâneo. O ETag permite-lhe determinar se o contentor ou um blob foi modificada uma vez que inicialmente ler ou criou, que lhe permite evitar a substituir o alterações consolidadas por outro processo ou cliente.

Pode definir condições de ETag utilizando a opção `options.accessConditions` parâmetro. O seguinte código de exemplo de carregamentos apenas o **test.txt** ficheiros se o blob já existe e tem o valor ETag contido `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

Quando estiver a utilizar ETags, é o padrão geral:

1. Obter o ETag como resultado de uma operação get, a lista ou a criar.
2. Efetue uma ação, a verificação de que o valor ETag não foi modificado.

Se o valor foi modificado, indica que o se outra instância ou cliente modificadas o blob ou contentor, uma vez que obteve o valor ETag.

### <a name="lease"></a>Concessão
Pode adquirir uma concessão de novo utilizando o **acquireLease** método, especificando o blob ou um contentor que pretende obter de uma concessão no. Por exemplo, o seguinte código adquire uma concessão no **myblob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

Operações subsequentes no **myblob** tem de fornecer o `options.leaseId` parâmetro. A concessão ID é devolvido como `result.id` de **acquireLease**.

> [!NOTE]
> Por predefinição, a duração da concessão é infinita. Pode especificar um período de tempo infinito não (entre 15 e 60 segundos), fornecendo o `options.leaseDuration` parâmetro.
>
>

Para remover uma concessão, utilize **releaseLease**. Para interromper a uma concessão, mas impedir outras pessoas de obtenção de uma concessão novo até que a duração original expirou, utilize **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso partilhado
Assinaturas de acesso partilhado (SAS) são uma forma segura para fornecer acesso granular para os blobs e contentores sem fornecer o nome da conta de armazenamento ou chaves. Assinaturas de acesso partilhado são frequentemente utilizadas para fornecer acesso limitado aos seus dados, tais como permitir que uma aplicação móvel aceder aos blobs.

> [!NOTE]
> Enquanto também podem permitir o acesso anónimo para blobs, assinaturas de acesso partilhado permitem-lhe fornecer mais o acesso controlado, dado que tem de gerar a SAS.
>
>

Uma aplicação como um serviço baseado na nuvem fidedigna gera assinaturas de acesso partilhado com o **generateSharedAccessSignature** do **BlobService**e fornece-o a uma aplicação não fidedigna ou por fidedigna, como uma aplicação móvel. Assinaturas geradas através de uma política, que descreve o início e de fim datas durante os quais as assinaturas de acesso partilhado são válidas de acesso partilhado, bem como o nível de acesso concedido o marcador de posição de assinaturas de acesso partilhado.

Exemplo de código seguinte gera uma nova política de acesso partilhado, o que permite que o marcador de posição de assinaturas de acesso partilhado efetuar operações de leitura no **myblob** blob e expira 100 minutos após a hora é criado.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

Tenha em atenção que as informações do anfitrião é necessário especificar também, conforme necessário, quando o marcador de posição de assinaturas de acesso partilhado tenta aceder ao contentor.

A aplicação de cliente, em seguida, utiliza assinaturas de acesso partilhado com **BlobServiceWithSAS** para executar operações contra o blob. O seguinte obtém as informações **myblob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Uma vez que as assinaturas de acesso partilhado foram geradas com acesso só de leitura, se é efetuada uma tentativa de modificar o blob, será devolvido um erro.

### <a name="access-control-lists"></a>Lista de controlo de acesso
Também pode utilizar uma lista de controlo de acesso (ACL) para definir a política de acesso para SAS. Isto é útil se pretender permitir que os vários clientes aceder a um contentor, mas fornecem as políticas de acesso diferentes para cada cliente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. Exemplo de código seguinte define duas políticas, uma para 'user1' e outra para 'Utilizador2':

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Exemplo de código seguinte obtém a ACL atual para **mycontainer**e, em seguida, adiciona as novas políticas utilizando **setBlobAcl**. Esta abordagem permite:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Depois de configurada a ACL, em seguida, pode criar com base no ID de uma política de assinaturas de acesso partilhado. Exemplo de código seguinte cria novas assinaturas de acesso partilhado para 'Utilizador2':

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos.

* [Storage do azure SDK para o nó a referência da API] [Storage do azure SDK para o nó a referência da API]  
* [Blogue da equipa de armazenamento do azure] [Blogue da equipa de armazenamento do azure]  
* [SDK de armazenamento do Azure para o nó] [ Azure Storage SDK for Node] repositório no GitHub  
* [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)  
* [Transferir dados com o utilitário de linha de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Criar e implementar uma aplicação Node.js num serviço em nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Blogue da equipa de armazenamento do azure]: http://blogs.msdn.com/b/windowsazurestorage/  
[Storage do azure SDK para o nó a referência da API]: http://dl.windowsazure.com/nodestoragedocs/index.html  
