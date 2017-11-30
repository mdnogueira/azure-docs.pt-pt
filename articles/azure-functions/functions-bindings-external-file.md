---
title: "Enlaces de ficheiro externo funções do Azure (pré-visualização) | Microsoft Docs"
description: "Utilizar enlaces de ficheiro externo nas funções do Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: c7a1ff4d4488b37b1969edfbb6f935eccd63413c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Enlaces de ficheiro externo funções do Azure (pré-visualização)
Este artigo mostra como manipular ficheiros a partir de fornecedores de SaaS diferentes (por exemplo, o OneDrive, o Dropbox) dentro da função de utilização de enlaces incorporadas. Suporta as funções do Azure aciona, de entrada e saída enlaces para o ficheiro externo.

Este enlace cria API ligações a fornecedores de SaaS ou utiliza ligações API existentes do grupo de recursos da sua aplicação de função.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Ligações de ficheiro suportadas

|Conector|Acionador|Input|Saída|
|:-----|:---:|:---:|:---:|
|[Caixa](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive para Empresas](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Unidade do Google](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Ligações de ficheiro externas, também podem ser utilizadas no [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

## <a name="external-file-trigger-binding"></a>Ficheiro externo acionar o enlace

O acionador de ficheiro externo do Azure permite-lhe monitorizar uma pasta remota e executar o código de função quando são detetadas alterações.

O acionador de ficheiro externo utiliza os seguintes objetos JSON no `bindings` matriz de function.json

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>Padrões de nome
Pode especificar um padrão de nome de ficheiro no `path` propriedade. A pasta referenciada tem de existir no fornecedor de SaaS.
Exemplos:

```json
"path": "input/original-{name}",
```

Este caminho seria localizar um ficheiro denominado *original File1.txt* no *entrada* pasta e o valor do `name` variável no código da função seria `File1.txt`.

Outro exemplo:

```json
"path": "input/{filename}.{fileextension}",
```

Este caminho também poderia localizar um ficheiro denominado *original File1.txt*e o valor do `filename` e `fileextension` variáveis no código da função seria *original File1* e *txt* .

Pode restringir o tipo de ficheiro de ficheiros utilizando um valor fixo para a extensão de ficheiro. Por exemplo:

```json
"path": "samples/{name}.png",
```

Neste caso, apenas *PNG* ficheiros no *amostras* pasta acionar a função.

Chavetas são carateres especiais no nome. Para especificar os nomes de ficheiros que tenham chavetas no nome, faça duplo chavetas.
Por exemplo:

```json
"path": "images/{{20140101}}-{name}",
```

Este caminho seria localizar um ficheiro denominado *{20140101}-soundfile.mp3* no *imagens* pasta e o `name` valor da variável no código da função seria *soundfile.mp3*.

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>Processamento de ficheiros nocivas
Quando uma função de Acionador de ficheiro externo falha, as funções do Azure tentará novamente essa função até 5 vezes por predefinição (incluindo a primeira tentativa) para um determinado ficheiro.
Se a todas as tentativas de 5 falharem, as funções adiciona uma mensagem para uma fila de armazenamento com o nome *webjobs-apihubtrigger-poison*. A mensagem da fila para ficheiros nocivas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato  *&lt;nome da aplicação de função >*. Funções.  *&lt;nome de função >*)
* FileType
* FolderName
* nome de ficheiro
* ETag (um identificador de versão do ficheiro, por exemplo: "0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilização de Acionador
C# funções, vincular os dados de ficheiro de entrada através de um parâmetro com nome na sua assinatura da função, como `<T> <name>`.
Onde `T` é o tipo de dados que pretende anular a serialização de dados e `paramName` é o nome especificado no [acionar JSON](#trigger). Funções de Node.js, vai aceder a dados de ficheiro de entrada utilizando `context.bindings.<name>`.

O ficheiro pode ser desserializado em qualquer um dos seguintes tipos:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para dados de ficheiro serializados para JSON.
  Se declarar um tipo de entrada personalizado (por exemplo, `FooType`), as funções do Azure tentar anular a serialização os dados JSON para o tipo especificado.
* Cadeia - útil para dados de ficheiro de texto.

C# funções, é também possível vincular a qualquer um dos seguintes tipos e o tempo de execução de funções tenta anular a serialização dos dados de ficheiros utilizando esse tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="trigger-sample"></a>Exemplo de Acionador
Suponha que tem o seguinte function.json, que define um acionador de ficheiro externo:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Consulte o exemplo de específicas do idioma que regista o conteúdo de cada ficheiro que é adicionado à pasta monitorizada.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Utilização de Acionador em c# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Utilização de Acionador no Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Enlace de entrada do ficheiro externo
O enlace de entrada do ficheiro externo do Azure permite-lhe utilizar um ficheiro a partir de uma pasta na sua função externa.

A entrada de ficheiro externo para uma função utiliza os seguintes objetos JSON no `bindings` matriz de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Tenha em atenção o seguinte:

* `path`tem de conter o nome de pasta e o nome de ficheiro. Por exemplo, se tiver um [acionador de fila](functions-bindings-storage-queue.md) na sua função, pode utilizar `"path": "samples-workitems/{queueTrigger}"` para apontar para um ficheiro a `samples-workitems` pasta com um nome que corresponda ao nome de ficheiro especificado na mensagem de Acionador.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilização de entrada
C# funções, vincular os dados de ficheiro de entrada através de um parâmetro com nome na sua assinatura da função, como `<T> <name>`.
Onde `T` é o tipo de dados que pretende anular a serialização de dados e `paramName` é o nome especificado no [enlace de entrada](#input). Funções de Node.js, vai aceder a dados de ficheiro de entrada utilizando `context.bindings.<name>`.

O ficheiro pode ser desserializado em qualquer um dos seguintes tipos:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para dados de ficheiro serializados para JSON.
  Se declarar um tipo de entrada personalizado (por exemplo, `InputType`), as funções do Azure tentar anular a serialização os dados JSON para o tipo especificado.
* Cadeia - útil para dados de ficheiro de texto.

C# funções, é também possível vincular a qualquer um dos seguintes tipos e o tempo de execução de funções tenta anular a serialização dos dados de ficheiros utilizando esse tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`


<a name="output"></a>

## <a name="external-file-output-binding"></a>Enlace de saída do ficheiro externo
O ficheiro externo do Azure enlace de saída permite-lhe escrever ficheiros para uma pasta na sua função externa.

O ficheiro externo para uma função utiliza os seguintes objetos JSON de saída a `bindings` matriz de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Tenha em atenção o seguinte:

* `path`tem de conter o nome de pasta e o nome de ficheiro para escrita. Por exemplo, se tiver um [acionador de fila](functions-bindings-storage-queue.md) na sua função, pode utilizar `"path": "samples-workitems/{queueTrigger}"` para apontar para um ficheiro a `samples-workitems` pasta com um nome que corresponda ao nome de ficheiro especificado na mensagem de Acionador.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilização de saída
C# funções, vincular o ficheiro de saída utilizando o nomeado `out` como parâmetro a assinatura da função, `out <T> <name>`, onde `T` é o tipo de dados que pretende serializar os dados para, e `paramName` é o nome especificado no o [vínculo de saída](#output). Funções de Node.js, vai aceder a ficheiros de saída com `context.bindings.<name>`.

Pode escrever o ficheiro de saída com qualquer um dos seguintes tipos:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para serialização JSON.
  Se declarar um tipo de saída personalizado (por exemplo, `out OutputType paramName`), as funções do Azure tentar serializar o objeto em JSON. Se o parâmetro de saída é nulo quando a função sai, o tempo de execução de funções cria um ficheiro como um objeto nulo.
* Cadeia - (`out string paramName`) útil para dados de ficheiro de texto. o tempo de execução de funções cria um ficheiro apenas se o parâmetro de cadeia não nulo quando a função sai.

C# funções também pode apresentar a qualquer um dos seguintes tipos:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Entrada + exemplo de saída
Suponha que tem o seguinte function.json, que define um [acionador de fila de armazenamento](functions-bindings-storage-queue.md), um ficheiro externo de entrada e saída de um ficheiro externo:

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Consulte o exemplo de específicas do idioma que copia o ficheiro de entrada para o ficheiro de saída.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Utilização em c# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Utilização no Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
