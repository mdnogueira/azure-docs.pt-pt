---
title: "Enlaces de armazenamento de BLOBs de funções do Azure"
description: "Compreenda como utilizar o Blob do Azure armazenamento acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 31a2fa3d3c87c16109514b130c95e731f401f8bd
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Enlaces de armazenamento de BLOBs de funções do Azure

Este artigo explica como trabalhar com enlaces de armazenamento de Blobs do Azure das funções do Azure. Funções do Azure suporta acionam, de entrada e saída enlaces para blobs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [As contas de armazenamento apenas de BLOBs](../storage/common/storage-create-storage-account.md#blob-storage-accounts) não são suportadas. Blob storage acionadores e enlaces precisam de uma conta de armazenamento para fins gerais. 

## <a name="blob-storage-trigger"></a>Acionador de armazenamento de BLOBs

Utilize um acionador de armazenamento de BLOBs para iniciar uma função quando é detetado um blob novo ou atualizado. Os conteúdos do blob são fornecidos como entrada para a função.

> [!NOTE]
> Quando estiver a utilizar um acionador de blob um plano de consumo, podem existir até um atraso de 10 minutos processar novos blobs, depois de uma aplicação de função tornou-se inativo. Depois da aplicação de função está em execução, blobs são processados imediatamente. Para evitar este atraso inicial, considere uma das seguintes opções:
> - Utilize um plano de serviço de aplicações com Always On ativado.
> - Utilize outro mecanismo para acionar o blob processar, tais como uma mensagem de fila que contém o nome do blob. Por exemplo, consulte o [exemplo de enlaces de entrada/saída de blob neste artigo](#input--output---example).

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#trigger---c-example)
* [Script do c#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Acionador - c# exemplo

O seguinte exemplo mostra [pré-compilada c#](functions-dotnet-class-library.md) código que escreve um registo quando um blob é adicionado ou atualizado no `samples-workitems` contentor.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Para obter mais informações sobre o `BlobTrigger` de atributos, consulte [acionador - atributos para pré-compilada c#](#trigger---attributes-for-precompiled-c).

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um acionador de blob enlace num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza o enlace. A função escreve um registo quando um blob é adicionado ou atualizado no `samples-workitems` contentor.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o script código c# que está vinculado a um `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Eis o script código c# que está vinculado a um `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um acionador de blob enlace num *function.json* de ficheiros e [código JavaScript] (funções-referência-node.md) que utiliza o enlace. A função escreve um registo quando um blob é adicionado ou atualizado no `samples-workitems` contentor.

Eis o *function.json* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>Acionador - atributos para pré-compilada c#

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize os seguintes atributos para configurar um acionador de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  O construtor do atributo demora uma cadeia de caminho que indica o contentor para ver e, opcionalmente, um [padrão de nome de blob](#trigger---blob-name-patterns). Eis um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

  Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo seguinte:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Outra forma para especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo seguinte mostra o nível de classe e o nível de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

A conta de armazenamento a utilizar é determinada pela seguinte ordem:

* O `BlobTrigger` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento predefinido para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `BlobTrigger` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | tem de ser definido como `blobTrigger`. Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure.|
|**direção** | n/d | tem de ser definido como `in`. Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure. Exceções são apresentadas na [utilização](#trigger---usage) secção. |
|**nome** | n/d | O nome da variável que representa o blob no código da função. | 
|**caminho** | **BlobPath** |O contentor para monitorizar.  Pode ser um [padrão de nome de blob](#trigger-blob-name-patterns). | 
|**ligação** | **Ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação de armazenamento predefinida na definição de aplicação com o nome `AzureWebJobsStorage`.<br><br>A cadeia de ligação tem de ser para uma conta do storage para fins gerais, não um [conta de armazenamento apenas de BLOBs](../storage/common/storage-create-storage-account.md#blob-storage-accounts).<br>Quando estiver a desenvolver localmente, as definições de aplicação enviadas para os valores de [local.settings.json ficheiro](functions-run-local.md#local-settings-file).|

## <a name="trigger---usage"></a>Acionador - utilização

Em c# e c# script, aceder aos dados blob utilizando um parâmetro de método como `Stream paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. É possível vincular a qualquer um dos seguintes tipos:

* `TextReader`
* `Stream`
* `ICloudBlob`(requer a direção de enlace "inout" no *function.json*)
* `CloudBlockBlob`(requer a direção de enlace "inout" no *function.json*)
* `CloudPageBlob`(requer a direção de enlace "inout" no *function.json*)
* `CloudAppendBlob`(requer a direção de enlace "inout" no *function.json*)

Conforme indicado, alguns destes tipos requerem um `inout` enlace direção no *function.json*. Esta não é suportada pelo editor padrão no portal do Azure, pelo que deverá utilizar o editor de avançadas.

Se os blobs de texto são esperados, é possível vincular o `string` tipo. Só é recomendada se o tamanho do blob é pequeno, como os conteúdos do blob todo são carregados na memória. Geralmente, é preferível utilizar um `Stream` ou `CloudBlockBlob` tipo.

Em JavaScript, aceder aos dados de blob de entrada utilizando `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Acionador - padrões de nome de blob

Pode especificar um padrão de nome do blob no `path` propriedade no *function.json* ou no `BlobTrigger` construtor de atributos. O padrão de nome pode ser um [expressão de filtro ou enlace](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>Filtrar por nome do blob

Os acionadores de exemplo seguintes apenas em blobs a `input` contentor que começam com a cadeia "original-":

```json
"path": "input/original-{name}",
```
 
Se o nome do blob é *original Blob1.txt*, o valor da `name` variável no código da função é `Blob1`.

### <a name="filter-on-file-type"></a>Filtrar por tipo de ficheiro

O exemplo seguinte é acionado apenas no *PNG* ficheiros:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrar chavetas em nomes de ficheiros

Para procurar chavetas em nomes de ficheiros, como as chavetas utilizando dois chavetas de escape. Os seguintes filtros de exemplo para blobs tem chavetas no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se o blob com o nome *{20140101}-soundfile.mp3*, a `name` valor da variável no código da função é *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Obter nome de ficheiro e extensão

O exemplo seguinte mostra como à qual vincular o nome de ficheiro de blob e extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```
Se o blob com o nome *original Blob1.txt*, o valor da `blobname` e `blobextension` as variáveis no código de função são *original Blob1* e *txt*.

## <a name="trigger---metadata"></a>Acionador - metadados

O acionador de blob fornece várias propriedades de metadados. Estas propriedades podem ser utilizadas como parte das expressões de enlace noutros enlaces ou como parâmetros no seu código. Estes valores têm a mesma semântica como o [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) tipo.


|Propriedade  |Tipo  |Descrição  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho para o blob acionadora.|
|`Uri`|`System.Uri`|URI do blob para a localização primária.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Propriedades do sistema do blob. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo utilizador para o blob.|

## <a name="trigger---blob-receipts"></a>Acionador - recibos de blob

O tempo de execução das funções do Azure garante que nenhuma função de Acionador de blob obtém chamada mais do que uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de blob especificado foi processada, mantém *blob recibos*.

Arquivos de funções do Azure recibos num contentor com o nome do blob *anfitriões de webjobs do azure* na conta do storage do Azure para a sua aplicação de função (definido pela definição de aplicação `AzureWebJobsStorage`). Um recibo de blob tem as seguintes informações:

* A função accionada ("*&lt;nome da aplicação de função >*. Funções.  *&lt;nome de função >*", por exemplo:"MyFunctionApp.Functions.CopyBlob")
* O nome do contentor
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, elimine a receção de BLOBs para esse blob do *anfitriões de webjobs do azure* contentor manualmente.

## <a name="trigger---poison-blobs"></a>Acionador - nocivas blobs

Quando uma função de Acionador de blob falha para um determinado blob, tentará novamente as funções do Azure funcionar um total de 5 vezes por predefinição. 

Se a todas as tentativas de 5 falharem, as funções do Azure adiciona uma mensagem para uma fila de armazenamento com o nome *webjobs-blobtrigger-poison*. A mensagem da fila para blobs nocivas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato  *&lt;nome da aplicação de função >*. Funções.  *&lt;nome de função >*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>Acionador - a consultar grandes contentores

Se o contentor de blob a ser monitorizado contém mais de 10 000 blobs, análises de tempo de execução funções ficheiros de registo da observar blobs novos ou alterados. Este processo pode resultar em atrasos. Uma função pode não obter uma acionada até vários minutos ou já depois de criado o blob. Além disso, [os registos de armazenamento são criados em "melhor esforço"](/rest/api/storageservices/About-Storage-Analytics-Logging) base. Não há nenhuma garantia de que todos os eventos são capturados. Em algumas condições, os registos podem ser omitidos. Se necessitar de processamento mais rápido ou mais fiável do blob, considere criar um [mensagem da fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, utilize um [acionador de fila](functions-bindings-storage-queue.md) em vez de um acionador de BLOBs para processar o blob. Outra opção consiste em utilizar a grelha de eventos; consulte o tutorial [Automate redimensionamento carregado imagens com eventos grelha](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="blob-storage-input--output-bindings"></a>Armazenamento de BLOBs de entrada e saída enlaces

Utilizar o Blob storage de entrada e saída enlaces para leitura e escrita de blobs.

## <a name="input--output---example"></a>Entrada e de saída - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#input--output---c-example)
* [Script do c#](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>Entrada e de saída - c# exemplo

O exemplo seguinte é um [pré-compilada c#](functions-dotnet-class-library.md) função que utiliza um acionador de blob e dois enlaces de blob de saída. A função é acionada pela criação de um blob de imagem no *imagens de exemplo* contentor. Cria cópias de pequenas e médias o tamanho do blob de imagem. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="input--output---c-script-example"></a>Entrada e de saída - exemplo de script do c#

O exemplo seguinte mostra o blob de entrada e saída de enlaces num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza os enlaces. A função faz uma cópia de um blob de texto. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O novo blob é denominado *{originalblobname}-cópia*.

No *function.json* ficheiro, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

O [configuração](#input--output---configuration) secção explica estas propriedades.

Eis o código de script do c#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>Entrada e de saída - exemplo de JavaScript

O exemplo seguinte mostra o blob de entrada e saída de enlaces num *function.json* de ficheiros e [código JavaScript] (funções-referência-node.md) que utiliza os enlaces. A função faz uma cópia de um blob. A função é acionada por uma mensagem de fila que contém o nome do blob para copiar. O novo blob é denominado *{originalblobname}-cópia*.

No *function.json* ficheiro, o `queueTrigger` propriedade de metadados é utilizada para especificar o nome do blob no `path` propriedades:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

O [configuração](#input--output---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes-for-precompiled-c"></a>Entrada e de saída - atributos para pré-compilada c#

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), que está definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O construtor do atributo demora o caminho para o blob e um `FileAccess` parâmetro com a indicação de leitura ou escrita, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
```

Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
```

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento ao nível de classe, método ou parâmetro. Para obter mais informações, consulte [acionador - atributos para pré-compilada c#](#trigger---attributes-for-precompiled-c).

## <a name="input--output---configuration"></a>Entrada e de saída - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `Blob` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | tem de ser definido como `blob`. |
|**direção** | n/d | Tem de ser definido como `in` para um enlace de entrada ou saída para um enlace de saída. Exceções são apresentadas na [utilização](#input--output---usage) secção. |
|**nome** | n/d | O nome da variável que representa o blob no código da função.  Definido como `$return` para referenciar o valor de retorno da função.|
|**caminho** |**BlobPath** | O caminho para o blob. | 
|**ligação** |**Ligação**| O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação de armazenamento predefinida na definição de aplicação com o nome `AzureWebJobsStorage`.<br><br>A cadeia de ligação tem de ser para uma conta do storage para fins gerais, não um [conta de armazenamento apenas de BLOBs](../storage/common/storage-create-storage-account.md#blob-storage-accounts).<br>Quando estiver a desenvolver localmente, as definições de aplicação enviadas para os valores de [local.settings.json ficheiro](functions-run-local.md#local-settings-file).|
|n/d | **Acesso** | Indica se irá ser ler ou escrever. |

## <a name="input--output---usage"></a>Entrada e de saída - utilização

Pré-compilada c# e c# script, aceder a blob utilizando um parâmetro de método como `Stream paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. É possível vincular a qualquer um dos seguintes tipos:

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob`(requer a direção de enlace "inout" no *function.json*)
* `CloudBlockBlob`(requer a direção de enlace "inout" no *function.json*)
* `CloudPageBlob`(requer a direção de enlace "inout" no *function.json*)
* `CloudAppendBlob`(requer a direção de enlace "inout" no *function.json*)

Conforme indicado, alguns destes tipos requerem um `inout` enlace direção no *function.json*. Esta não é suportada pelo editor padrão no portal do Azure, pelo que deverá utilizar o editor de avançadas.

Se a leitura de blobs de texto, é possível vincular a um `string` tipo. Este tipo só é recomendado se o tamanho do blob é pequeno, como os conteúdos do blob todo são carregados na memória. Geralmente, é preferível utilizar um `Stream` ou `CloudBlockBlob` tipo.

Em JavaScript, aceder a dados de blob utilizando `context.bindings.<name>`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ir para um guia de introdução que utiliza um acionador de armazenamento de BLOBs](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
