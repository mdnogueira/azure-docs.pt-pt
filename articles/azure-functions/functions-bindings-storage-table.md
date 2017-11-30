---
title: "Enlaces de armazenamento de tabela do Azure para as funções do Azure"
description: "Compreenda como utilizar os enlaces de armazenamento de tabelas do Azure das funções do Azure."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: a1305432d98c2e9f9f8bc30cacc62d49b1a8ba36
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Enlaces de armazenamento de tabela do Azure para as funções do Azure

Este artigo explica como trabalhar com enlaces de armazenamento de tabelas do Azure das funções do Azure. Funções do Azure suporta entrada e saída os enlaces do Table storage do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="input"></a>Input

Utilize o enlace de entrada de armazenamento de tabelas do Azure para ler uma tabela de uma conta de armazenamento do Azure.

## <a name="input---example"></a>Entrada - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c# ler uma entidade](#input---c-example-1)
* [Pré-compilada c# várias entidades de leitura](#input---c-example-2)
* [Script do c# - ler uma entidade](#input---c-script-example-1)
* [Script do c# - várias entidades de leitura](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Entrada - c# exemplo 1

O seguinte exemplo mostra [pré-compilada c#](functions-dotnet-class-library.md) código que lê uma linha única tabela. 

O valor de chave de linha "{queueTrigger}" indica que a chave de linha provenientes de cadeia de mensagem de fila.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### <a name="input---c-example-2"></a>Entrada - c# exemplo 2

O seguinte exemplo mostra [pré-compilada c#](functions-dotnet-class-library.md) código que lê várias linhas de tabela. Tenha em atenção que o `MyPoco` classe deriva de `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>Entrada - c# exemplo de script 1

O exemplo seguinte mostra um enlace de entrada de tabela num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza o enlace. A função utiliza um acionador de fila para uma linha única tabela de leitura. 

O *function.json* ficheiro Especifica um `partitionKey` e um `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a chave de linha provenientes de cadeia de mensagem de fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) secção explica estas propriedades.

Eis o código de script do c#:

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

### <a name="input---c-script-example-2"></a>Entrada - c# exemplo de script 2

O exemplo seguinte mostra um enlace de entrada de tabela num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza o enlace. A função lê entidades para uma chave de partição que é especificado numa mensagem de fila.

Eis o *function.json* ficheiro:

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) secção explica estas propriedades.

O código de script do c# adiciona uma referência ao SDK de armazenamento do Azure, para que o tipo de entidade pode derivar `TableEntity`:

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

### <a name="input---f-example"></a>Entrada - F # exemplo

O exemplo seguinte mostra um enlace de entrada de tabela num *function.json* ficheiro e [F # script](functions-reference-fsharp.md) código que utiliza o enlace. A função utiliza um acionador de fila para uma linha única tabela de leitura. 

O *function.json* ficheiro Especifica um `partitionKey` e um `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a chave de linha provenientes de cadeia de mensagem de fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) secção explica estas propriedades.

Eis o código F #:

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

### <a name="input---javascript-example"></a>Entrada - exemplo de JavaScript

O exemplo seguinte mostra um enlace de entrada de tabela num *function.json* de ficheiros e [código JavaScript] (funções-referência-node.md) que utiliza o enlace. A função utiliza um acionador de fila para uma linha única tabela de leitura. 

O *function.json* ficheiro Especifica um `partitionKey` e um `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a chave de linha provenientes de cadeia de mensagem de fila.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>Entrada - atributos
 
Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize os seguintes atributos para configurar um enlace de entrada de tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), que está definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  O construtor do atributo tem o nome da tabela, a chave de partição e a chave de linha. Pode ser utilizado um parâmetro out ou o valor de retorno da função, conforme mostrado no exemplo seguinte:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo seguinte:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Para obter um exemplo completado, consulte [entrada - pré-compilada c# exemplo](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definida no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Outra forma para especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo seguinte mostra o nível de classe e o nível de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a utilizar é determinada pela seguinte ordem:

* O `Table` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento predefinido para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `Table` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | tem de ser definido como `table`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure.|
|**direção** | n/d | tem de ser definido como `in`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a tabela ou entidade no código da função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. A chave de partição da entidade de tabela para leitura. Consulte o [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | Opcional. A chave de linha da entidade de tabela para leitura. Consulte o [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**tirar** |**Tirar** | Opcional. O número máximo de entidades para ler em JavaScript. Consulte o [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**filtro** |**Filtro** | Opcional. Uma expressão de filtro de OData para a tabela de entrada em JavaScript. Consulte o [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**ligação** |**Ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação de armazenamento predefinida na definição de aplicação com o nome `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - utilização

O enlace de entrada do Table storage suporta os seguintes cenários:

* **Ler uma linha em c# ou script do c#**

  Definir `partitionKey` e `rowKey`. Aceder os dados da tabela utilizando um parâmetro de método `T <paramName>`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. `T`Normalmente, é um tipo que implemente `ITableEntity` ou deriva de `TableEntity`. O `filter` e `take` propriedades não são utilizadas neste cenário. 

* **Ler uma ou mais linhas em c# ou script do c#**

  Aceder os dados da tabela utilizando um parâmetro de método `IQueryable<T> <paramName>`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. `T`tem de ser um tipo que implemente `ITableEntity` ou deriva de `TableEntity`. Pode utilizar `IQueryable` métodos de fazer qualquer filtragem necessário. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são utilizadas neste cenário.  

> [!NOTE]
> `IQueryable`não funciona no .NET Core, pelo que não funcionará [funções v2 runtime](functions-versions.md).

  Uma alternativa consiste em utilizar um `CloudTable paramName` parâmetro de método para ler a tabela utilizando o SDK de armazenamento do Azure.

* **Ler uma ou mais linhas em JavaScript**

  Definir o `filter` e `take` propriedades. Não defina `partitionKey` ou `rowKey`. Aceder à entrada tabela entidade (ou entidades) utilizando `context.bindings.<name>`. Os objetos de serialização anulados tem `RowKey` e `PartitionKey` propriedades.

## <a name="output"></a>Saída

Utilize uma saída de armazenamento de Azure Table enlace escrever entidades a uma tabela de uma conta de armazenamento do Azure.

## <a name="output---example"></a>De saída - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#output---c-example)
* [Script do c#](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - c# exemplo

O seguinte exemplo mostra [pré-compilada c#](functions-dotnet-class-library.md) código que utiliza um acionador HTTP escreveu uma fila única tabela. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

O exemplo seguinte mostra uma saída de tabela enlace num *function.json* ficheiro e [c# script](functions-reference-csharp.md) código que utiliza o enlace. A função escreve várias entidades de tabela.

Eis o *function.json* ficheiro:

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O [configuração](#output---configuration) secção explica estas propriedades.

Eis o código de script do c#:

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

### <a name="output---f-example"></a>Saída - F # exemplo

O exemplo seguinte mostra uma saída de tabela enlace num *function.json* ficheiro e [F # script](functions-reference-fsharp.md) código que utiliza o enlace. A função escreve várias entidades de tabela.

Eis o *function.json* ficheiro:

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O [configuração](#output---configuration) secção explica estas propriedades.

Eis o código F #:

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

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

O exemplo seguinte mostra uma saída de tabela enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função escreve várias entidades de tabela.

Eis o *function.json* ficheiro:

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O [configuração](#output---configuration) secção explica estas propriedades.

Eis o código JavaScript:

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

## <a name="output---attributes"></a>Saída - atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), que está definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O construtor do atributo tem o nome da tabela. Pode ser utilizado num `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Para obter um exemplo completado, consulte [resultado - pré-compilada c# exemplo](#output---c-example).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento ao nível de classe, método ou parâmetro. Para obter mais informações, consulte [entrada - atributos](#input---attributes-for-precompiled-c).

## <a name="output---configuration"></a>De saída - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `Table` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | tem de ser definido como `table`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure.|
|**direção** | n/d | tem de ser definido como `out`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure. |
|**nome** | n/d | O nome da variável utilizado no código de função que representa a tabela ou entidade. Definido como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | A chave de partição da entidade de tabela para escrita. Consulte o [secção utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela para escrita. Consulte o [secção utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**ligação** |**Ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o resto do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução de funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o tempo de execução de funções utiliza a cadeia de ligação de armazenamento predefinida na definição de aplicação com o nome `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

O armazenamento de tabelas de saída do enlace suporta os seguintes cenários:

* **Escrever uma linha em qualquer idioma**

  Em c# e c# script, aceder a entidade da tabela de saída utilizando um parâmetro de método como `out T paramName` ou a função de valor de retorno. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. `T`pode ser qualquer tipo serializável se a chave de partição e a chave de linha são fornecidos pelo *function.json* ficheiro ou o `Table` atributo. Caso contrário, `T` tem de ser um tipo que inclui `PartitionKey` e `RowKey` propriedades. Neste cenário, `T` , normalmente, implementa `ITableEntity` ou deriva de `TableEntity`, mas não tem.

* **Uma ou mais linhas de escrita em c# ou c#**

  Em c# e c# script, aceder a entidade da tabela de saída através da utilização de um parâmetro de método `ICollector<T> paramName` ou `ICollectorAsync<T> paramName`. No script do c#, `paramName` é o valor especificado no `name` propriedade *function.json*. `T`Especifica o esquema das entidades que pretende adicionar. Normalmente, `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas não tem. A chave de partição e a linha de valores da chave *function.json* ou `Table` construtor de atributos não são utilizados neste cenário.

  Uma alternativa consiste em utilizar um `CloudTable paramName` parâmetro de método para escrever a tabela utilizando o SDK de armazenamento do Azure.

* **Escrever uma ou mais linhas em JavaScript**

  Nas funções de JavaScript, aceder à tabela de saída utilizando `context.bindings.<name>`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
