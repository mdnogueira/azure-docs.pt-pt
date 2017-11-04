---
title: "Enlaces de funções Blob Storage do Azure | Microsoft Docs"
description: "Compreenda como utilizar o armazenamento do Azure acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 79b9dbee89a4e33a1768343b9242d6b2b1118355
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Enlaces de armazenamento de BLOBs de funções do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com enlaces de armazenamento de Blobs do Azure das funções do Azure. Acionamento de suporta funções do Azure, de entrada e saída enlaces para o Blob storage do Azure. Para as funcionalidades que estão disponíveis em todos os enlaces, consulte [acionadores de funções do Azure e conceitos de enlaces](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> A [apenas a conta de armazenamento de BLOBs](../storage/common/storage-create-storage-account.md#blob-storage-accounts) não é suportada. Blob storage acionadores e enlaces precisam de uma conta de armazenamento para fins gerais. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Blob storage acionadores e enlaces

Utilizando o acionador de armazenamento de Blobs do Azure, o código de função é chamado quando é detetado um blob novo ou atualizado. Os conteúdos do blob são fornecidos como entrada para a função.

Definir um armazenamento de BLOBs acionador utilizando o **integrar** separador no portal de funções. O portal cria a seguinte definição no **enlaces** secção *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

Blob de entrada e saída enlaces são definidos utilizando `blob` como o tipo de enlace:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* O `path` suporta a propriedade de enlace expressões e parâmetros de filtro. Consulte [padrões de nome](#pattern).
* O `connection` propriedade tem de conter o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. No portal do Azure, o editor padrão no **integrar** separador configura esta definição de aplicação para, quando seleciona uma conta de armazenamento.

> [!NOTE]
> Quando estiver a utilizar um acionador de blob um plano de consumo, podem existir até um atraso de 10 minutos processar novos blobs, depois de uma aplicação de função tornou-se inativo. Depois da aplicação de função está em execução, blobs são processados imediatamente. Para evitar este atraso inicial, considere uma das seguintes opções:
> - Utilize um plano de serviço de aplicações com Always On ativado.
> - Utilize outro mecanismo para acionar o blob processar, tais como uma mensagem de fila que contém o nome do blob. Por exemplo, consulte [enlace de entrada de Acionador de fila com o blob](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Padrões de nome
Pode especificar um padrão de nome do blob no `path` propriedade, o que pode ser uma expressão de filtro ou enlace. Consulte [expressões e padrões de enlace](functions-triggers-bindings.md#binding-expressions-and-patterns).

Por exemplo, para filtrar para blobs que começam com a cadeia "original", utilize a seguinte definição. Este caminho localiza um blob com o nome *original Blob1.txt* no *entrada* contentor e o valor do `name` variável no código da função é `Blob1`.

```json
"path": "input/original-{name}",
```

Para vincular o nome de ficheiro de blob e extensão separadamente, utilize dois padrões. Este caminho também localiza um blob com o nome *original Blob1.txt*e o valor do `blobname` e `blobextension` as variáveis no código de função são *original Blob1* e *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

Pode restringir o tipo de ficheiro de blobs através de um valor fixo para a extensão de ficheiro. Por exemplo, para acionar apenas nos ficheiros PNG, utilize o padrão do seguinte:

```json
"path": "samples/{name}.png",
```

Chavetas são carateres especiais no nome. Para especificar os nomes de blob tem chavetas no nome, pode terminar as chavetas utilizando dois chavetas. O exemplo seguinte localiza um blob com o nome *{20140101}-soundfile.mp3* no *imagens* contentor e o `name` valor da variável no código da função é *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Metadados de Acionador

O acionador de blob fornece várias propriedades de metadados. Estas propriedades podem ser utilizadas como parte das expressões de enlaces noutros enlaces ou como parâmetros no seu código. Estes valores têm a mesma semântica como [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Digite `string`. O caminho de blob acionadora
- **URI**. Digite `System.Uri`. URI do blob para a localização primária.
- **Propriedades**. Digite `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. Propriedades do sistema do blob.
- **Metadados**. Digite `IDictionary<string,string>`. Os metadados definidos pelo utilizador para o blob.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Recibos de blob
O tempo de execução das funções do Azure garante que nenhuma função de Acionador de blob obtém chamada mais do que uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de blob especificado foi processada, mantém *blob recibos*.

Arquivos de funções do Azure recibos num contentor com o nome do blob *anfitriões de webjobs do azure* na conta do storage do Azure para a sua aplicação de função (definido pela definição de aplicação `AzureWebJobsStorage`). Um recibo de blob tem as seguintes informações:

* A função accionada ("*&lt;nome da aplicação de função >*. Funções.  *&lt;nome de função >*", por exemplo:"MyFunctionApp.Functions.CopyBlob")
* O nome do contentor
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, elimine a receção de BLOBs para esse blob do *anfitriões de webjobs do azure* contentor manualmente.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Processamento nocivas blobs
Quando uma função de Acionador de blob falha para um determinado blob, tentará novamente as funções do Azure funcionar um total de 5 vezes por predefinição. 

Se a todas as tentativas de 5 falharem, as funções do Azure adiciona uma mensagem para uma fila de armazenamento com o nome *webjobs-blobtrigger-poison*. A mensagem da fila para blobs nocivas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato  *&lt;nome da aplicação de função >*. Funções.  *&lt;nome de função >*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>A consultar grandes contentores de BLOBs
Se o contentor de blob a ser monitorizado contém mais de 10 000 blobs, análises de tempo de execução funções ficheiros de registo da observar blobs novos ou alterados. Este processo não é em tempo real. Uma função pode não obter uma acionada até vários minutos ou já depois de criado o blob. Além disso, [os registos de armazenamento são criados em "melhor esforço"](/rest/api/storageservices/About-Storage-Analytics-Logging) base. Não há nenhuma garantia de que todos os eventos são capturados. Em algumas condições, os registos podem ser omitidos. Se necessitar de processamento mais rápido ou mais fiável do blob, considere criar um [mensagem da fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, utilize um [acionador de fila](functions-bindings-storage-queue.md) em vez de um acionador de BLOBs para processar o blob.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Utilizar um acionador de blob e o enlace de entrada
Funções de .NET, acedem aos dados blob utilizar como um parâmetro de método `Stream paramName`. Aqui, `paramName` é o valor especificado no [configuração acionador](#trigger). Funções de Node.js, acedem aos dados de blob de entrada utilizando `context.bindings.<name>`.

No .NET, é possível vincular a qualquer um dos tipos na lista abaixo. Se for utilizado como um enlace de entrada, alguns destes tipos de requisitos requerem um `inout` enlace direção no *function.json*. Esta não é suportada pelo editor padrão, pelo que deverá utilizar o editor de avançadas.

* `TextReader`
* `Stream`
* `ICloudBlob`(requer a direção de enlace "inout")
* `CloudBlockBlob`(requer a direção de enlace "inout")
* `CloudPageBlob`(requer a direção de enlace "inout")
* `CloudAppendBlob`(requer a direção de enlace "inout")

Se os blobs de texto são esperados, também pode vincular a um .NET `string` tipo. Só é recomendada se o tamanho do blob é pequeno, como os conteúdos do blob todo são carregados na memória. Geralmente, é preferível utilizar um `Stream` ou `CloudBlockBlob` tipo.

## <a name="trigger-sample"></a>Exemplo de Acionador
Suponha que tem o seguinte function.json que define um acionador de armazenamento de BLOBs:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Consulte o exemplo de específicas do idioma que regista o conteúdo de cada blob que é adicionado ao contentor monitorizado.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>Exemplos de Acionador de blob em c# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Exemplo de Acionador no Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Utilizar um blob de saída do enlace

Nas funções de .NET, deve utilizar um `out string` parâmetro na assinatura de função ou utilize um dos tipos na lista seguinte. Funções de Node.js, vai aceder a utilização de blob de saída `context.bindings.<name>`.

Nas funções de .NET pode apresentar a qualquer um dos seguintes tipos:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Exemplo de entrada e de saída do acionador de fila com o blob
Suponha que tem o seguinte function.json, que define um [acionador de armazenamento de filas](functions-bindings-storage-queue.md), um armazenamento de BLOBs de entrada e saída de um armazenamento de Blobs. Tenha em atenção a utilização do `queueTrigger` propriedade de metadados. na entrada de BLOBs e de saída `path` propriedades:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Consulte o exemplo de específicas do idioma que copia o blob de entrada para o blob de saída.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>Exemplo de enlace de blob em c# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out Stream myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Exemplo de enlace de blob no Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

