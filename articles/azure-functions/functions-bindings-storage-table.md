---
title: "Enlaces de funções tabela de armazenamento do Azure | Microsoft Docs"
description: "Compreenda como utilizar os enlaces de armazenamento do Azure das funções do Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-storage-table-bindings"></a>Enlaces de tabela de armazenamento de funções do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e enlaces de tabela de armazenamento do Azure de código das funções do Azure. Funções do Azure suporta entrada e saída enlaces para tabelas de armazenamento do Azure.

O enlace da tabela de armazenamento suporta os seguintes cenários:

* **Ler uma única linha de uma função de c# ou Node.js** – configure `partitionKey` e `rowKey`. O `filter` e `take` propriedades não são utilizadas neste cenário.
* **Ler várias linhas de uma função de c#** -funções o tempo de execução fornece um `IQueryable<T>` objeto vinculado à tabela. Tipo `T` tem de derivar de `TableEntity` ou implementar `ITableEntity`. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são utilizadas neste cenário; pode utilizar o `IQueryable` objeto fazer qualquer filtragem necessário. 
* **Ler várias linhas de uma função de nó** – configure a `filter` e `take` propriedades. Não defina `partitionKey` ou `rowKey`.
* **Escrever uma ou mais linhas de uma função de c#** -funções o tempo de execução fornece um `ICollector<T>` ou `IAsyncCollector<T>` vinculada à tabela, onde `T` Especifica o esquema das entidades que pretende adicionar. Normalmente, escreva `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas não tem. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são utilizadas neste cenário.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Enlace de entrada de tabela de armazenamento
O enlace de entrada de tabela de armazenamento do Azure permite-lhe utilizar uma tabela de armazenamento na sua função. 

A entrada de tabela de armazenamento para uma função utiliza os seguintes objetos JSON no `bindings` matriz de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Tenha em atenção o seguinte: 

* Utilize `partitionKey` e `rowKey` em conjunto para ler uma única entidade. Estas propriedades são opcionais. 
* `connection`tem de conter o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. No portal do Azure, o editor padrão no **integrar** separador configura esta definição de aplicação para que quando cria um armazenamento de conta ou seleciona um existente. Também pode [configurar esta aplicação manualmente a definição](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilização de entrada
C# funções, vincular a entidade da tabela de entrada (ou entidades) utilizando um parâmetro com nome na sua assinatura da função, como `<T> <name>`.
Onde `T` é o tipo de dados que pretende anular a serialização de dados e `paramName` é o nome especificado no [enlace de entrada](#input). Funções de Node.js, vai aceder a entidade da tabela de entrada (ou entidades) utilizando `context.bindings.<name>`.

Os dados de entrada podem ser desserializados funções Node.js ou c#. Os objetos de serialização anulados tem `RowKey` e `PartitionKey` propriedades.

C# funções, é também possível vincular a qualquer um dos seguintes tipos e o tempo de execução de funções irá tentar anular a serialização dos dados de tabela utilizando esse tipo:

* Qualquer tipo que implemente`ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Exemplo de entrada
Deveria que ter o function.json seguintes, que utiliza um acionador de fila para ler a linha de uma única tabela. Especifica o JSON `PartitionKey`  
 `RowKey`. `"rowKey": "{queueTrigger}"`indica que a chave de linha provenientes de cadeia de mensagem de fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Consulte o exemplo de específicas do idioma que lê uma entidade única tabela.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Exemplo de entrada em c# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Exemplo de entrada no F # #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Exemplo de entrada no Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Enlace de saída do tabela de armazenamento
O resultado da tabela de armazenamento do Azure enlace permite escrever entidades para o armazenamento de um tabela na sua função. 

A tabela de armazenamento para uma função utiliza os seguintes objetos JSON de saída a `bindings` matriz de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Tenha em atenção o seguinte: 

* Utilize `partitionKey` e `rowKey` em conjunto para escrever uma única entidade. Estas propriedades são opcionais. Também pode especificar `PartitionKey` e `RowKey` quando criar os objetos de entidade no código da função.
* `connection`tem de conter o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. No portal do Azure, o editor padrão no **integrar** separador configura esta definição de aplicação para que quando cria um armazenamento de conta ou seleciona um existente. Também pode [configurar esta aplicação manualmente a definição](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilização de saída
C# funções, vincular à saída de tabela, utilizando o nomeado `out` como parâmetro a assinatura da função, `out <T> <name>`, onde `T` é o tipo de dados que pretende serializar os dados para, e `paramName` é o nome especificado no [vínculo de saída](#output). Funções de Node.js, vai aceder a tabela de saída utilizando `context.bindings.<name>`.

Pode serializar objetos nas funções de Node.js ou c#. C# funções, é possível vincular também para os seguintes tipos:

* Qualquer tipo que implemente`ITableEntity`
* `ICollector<T>`(para a saída várias entidades. Consulte [exemplo](#outcsharp).)
* `IAsyncCollector<T>`(versão assíncrona de `ICollector<T>`)
* `CloudTable`(utilizando o SDK de armazenamento do Azure. Consulte [exemplo](#readmulti).)

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemplo de saída
O seguinte *function.json* e *run.csx* exemplo demonstra como escrever várias entidades de tabela.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Consulte o exemplo de específicas do idioma que cria várias entidades de tabela.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemplo de saída em c# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemplo de saída no F # #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemplo de saída no Node.js
```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Exemplo: Ler várias entidades de tabela em c#  #
O seguinte *function.json* e exemplo de código c# lê entidades para uma chave de partição que é especificada na mensagem de fila.

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O código c# adiciona uma referência ao SDK de armazenamento do Azure, para que o tipo de entidade pode derivar `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

