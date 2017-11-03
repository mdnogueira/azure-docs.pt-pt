---
title: "Azure Cosmos DB enlaces de funções | Microsoft Docs"
description: "Compreenda como utilizar o Azure Cosmos DB acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: d05c0342e771e229a7175570ad227c4359980990
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Azure Cosmos DB enlaces de funções
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e enlaces de BD do Cosmos Azure de código das funções do Azure. Suporta as funções aciona, de entrada e saída enlaces para Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para obter mais informações sobre a informática sem servidor com o Azure Cosmos DB, consulte [BD do Cosmos do Azure: através das funções do Azure de computação de base de dados sem servidor](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Acionador do Cosmos BD do Azure

O acionador de base de dados do Azure Cosmos utiliza o [Azure Cosmos DB alteração Feed](../cosmos-db/change-feed.md) para escutar alterações de em partições. O acionador requer uma segunda coleção, que utiliza para armazenar _concessões_ através de partições.

A coleção que está a ser monitorizado e a coleção que contém os concessões tem de estar disponíveis para acionar a funcionar.

O acionador de base de dados do Azure Cosmos suporta as seguintes propriedades:

|Propriedade  |Descrição  |
|---------|---------|
|**tipo** | tem de ser definido como `cosmosDBTrigger`. |
|**nome** | O nome da variável utilizado no código de função que representa a lista de documentos com as alterações. | 
|**direção** | tem de ser definido como `in`. Este parâmetro é definido automaticamente quando criar o acionador no portal do Azure. |
|**connectionStringSetting** | O nome de uma definição de aplicação que contém a cadeia de ligação utilizada para ligar à conta de base de dados do Azure Cosmos a ser monitorizada. |
|**databaseName** | O nome da base de dados do Azure Cosmos DB com a coleção que está a ser monitorizada. |
|**collectionName** | O nome da coleção que está a ser monitorizado. |
| **leaseConnectionStringSetting** | (Opcional) O nome de uma definição de aplicação que contenha a cadeia de ligação ao serviço que contém a coleção de concessão. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é automaticamente definido quando o enlace é criado no portal. |
| **leaseDatabaseName** | (Opcional) O nome da base de dados que contém a coleção utilizada para armazenar concessões. Quando não definido, o valor da `databaseName` definição é utilizada. Este parâmetro é automaticamente definido quando o enlace é criado no portal. |
| **leaseCollectionName** | (Opcional) O nome da coleção utilizado para armazenar concessões. Quando não definido, o valor `leases` é utilizado. |
| **createLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando já não existir. O valor predefinido é `false`. |
| **leaseCollectionThroughput** | (Opcional) Define a quantidade de unidades de pedido para atribuir quando é criada a coleção de concessões. Esta definição é apenas utilizado quando `createLeaseCollectionIfNotExists` está definido como `true`. Este parâmetro é automaticamente definido quando o enlace é criado utilizando o portal.

>[!NOTE] 
>A cadeia de ligação utilizada para ligar à coleção de concessões tem de ter permissões de escrita.

Estas propriedades podem ser definidas no separador integrar para a função no portal do Azure ou ao editar o `function.json` ficheiro de projeto.

## <a name="using-an-azure-cosmos-db-trigger"></a>Utilizar um acionador de base de dados do Azure Cosmos

Esta secção contém exemplos de como utilizar o acionador de base de dados do Azure Cosmos. Os exemplos assumem um acionador metadados que o seguinte aspeto:

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "<connection-app-setting>",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```
 
Para obter um exemplo de como criar um acionador de base de dados do Azure Cosmos a partir de uma aplicação de função no portal, consulte [criar uma função acionada por base de dados do Azure Cosmos](functions-create-cosmos-db-triggered-function.md). 

### <a name="trigger-sample-in-c"></a>Exemplo de Acionador em c# #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>Exemplo de Acionador em JavaScript
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Enlace de entrada de API do DocumentDB
O enlace de entrada do DocumentDB API obtém um documento de base de dados do Azure Cosmos e passa-a para o parâmetro de entrada com o nome da função. O documento que ID pode ser determinado com base no acionador que invoca a função. 

O enlace de entrada de API do DocumentDB tem as seguintes propriedades no *function.json*:

|Propriedade  |Descrição  |
|---------|---------|
|**nome**     | Nome do parâmetro de enlace que representa o documento na função.  |
|**tipo**     | tem de ser definido como `documentdb`.        |
|**databaseName** | A base de dados que contém o documento.        |
|**collectionName**  | O nome da coleção que contém o documento. |
|**ID**     | O ID do documento para obter. Esta propriedade suporta parâmetros de enlaces. Para obter mais informações, consulte [vincular a propriedades personalizadas de entrada numa expressão de enlace](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | Uma consulta de base de dados SQL do Azure Cosmos utilizada para obter vários documentos. A consulta suporta enlaces de tempo de execução, como no exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**ligação**     |O nome da definição de aplicação que contém a cadeia de ligação de base de dados do Azure Cosmos.        |
|**direção**     | tem de ser definido como `in`.         |

Não é possível definir ambos os **id** e **sqlQuery** propriedades. Se nenhum dos dois estiver definida, é obtida a coleção completa.

## <a name="using-a-documentdb-api-input-binding"></a>Utilizar um enlace de entrada de API do DocumentDB

* Em c# e F # funções, quando a função sai com sucesso, quaisquer alterações efetuadas para o documento de entrada através dos parâmetros de entrada com nome são mantidas automaticamente. 
* Nas funções de JavaScript, as atualizações não são efetuadas automaticamente após a saída da função. Em alternativa, utilize `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para disponibilizar atualizações. Consulte o [JavaScript exemplo](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Exemplo de entrada para único documento
Suponha que tem as seguintes DocumentDB API entrada enlace no `bindings` matriz de function.json:

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_COSMOSDB",     
  "direction": "in"
}
```

Consulte o exemplo de específicas do idioma que utiliza este enlace de entrada para atualizar o valor de texto do documento.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Exemplo de entrada em c# #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Exemplo de entrada no F # #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Este exemplo requer um `project.json` ficheiro que especifica o `FSharp.Interop.Dynamic` e `Dynamitey` NuGet dependências:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Para adicionar um `project.json` de ficheiros, consulte [gestão de pacotes F #](functions-reference-fsharp.md#package).

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Exemplo de entrada em JavaScript

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Exemplo de entrada com vários documentos

Suponha que pretende obter vários documentos especificados por uma consulta SQL, utilizando um acionador de fila para personalizar os parâmetros de consulta. 

Neste exemplo, o acionador de filas fornece um parâmetro `departmentId`. Uma mensagem de fila de `{ "departmentId" : "Finance" }` iria devolver todos os registos para o departamento financeiro. Utilize o seguinte no *function.json*:

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>Exemplo de entrada com vários documentos em c#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Exemplo de entrada com vários documentos em JavaScript

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }       
    context.done();
};
```

## <a id="docdboutput"></a>Enlace de saída de API do DocumentDB
A saída de API do DocumentDB enlace permite escrever um novo documento para uma base de dados do Azure Cosmos DB. Tem as seguintes propriedades *function.json*:

|Propriedade  |Descrição  |
|---------|---------|
|**nome**     | Nome do parâmetro de enlace que representa o documento na função.  |
|**tipo**     | tem de ser definido como `documentdb`.        |
|**databaseName** | A base de dados que contém a coleção onde o documento é criado.     |
|**collectionName**  | O nome da coleção em que o documento é criado. |
|**createIfNotExists**     | Um valor booleano para indicar se a coleção é criada quando não existe. A predefinição é *falso*. Isto acontece porque novas coleções são criadas com débito reservado, que tem custos implicações. Para obter mais detalhes, visite o [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**ligação**     |O nome da definição de aplicação que contém a cadeia de ligação de base de dados do Azure Cosmos.        |
|**direção**     | tem de ser definido como `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>Utilizar uma API de DocumentDB vínculo de saída
Esta secção mostra como utilizar a saída de API do DocumentDB enlace no código da função.

Por predefinição, quando escreve para o parâmetro de saída na sua função, um documento é criado na base de dados. Este documento tem um GUID automaticamente gerado como o ID do documento. Pode especificar o ID do documento documento de saída, especificando o `id` propriedade no objeto JSON transmitido para o parâmetro de saída. 

>[!Note]  
>Quando especificar o ID de um documento existente, obtém substituído pelo novo documento de saída. 

A saída vários documentos, também pode vincular ao `ICollector<T>` ou `IAsyncCollector<T>` onde `T` é um dos tipos suportados.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>Exemplo de enlace de saída de API do DocumentDB
Suponha que tem as seguintes DocumentDB API enlace de saída a `bindings` matriz de function.json:

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_COSMOSDB",     
  "direction": "out"
}
```

E tem um enlace de entrada da fila para uma fila que recebe JSON no seguinte formato:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

E que pretende criar a base de dados do Azure Cosmos documentos no seguinte formato para cada registo:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Consulte o exemplo de específicas do idioma que utiliza este enlace de saída para adicionar documentos na base de dados.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemplo de saída em c# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemplo de saída no F # #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

Este exemplo requer um `project.json` ficheiro que especifica o `FSharp.Interop.Dynamic` e `Dynamitey` NuGet dependências:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Para adicionar um `project.json` de ficheiros, consulte [gestão de pacotes F #](functions-reference-fsharp.md#package).

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Exemplo de saída em JavaScript

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```
