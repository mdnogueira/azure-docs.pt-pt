---
title: "Como começar com o Visual Studio e o armazenamento de tabelas ligadas serviços (ASP.NET Core) | Microsoft Docs"
description: "Como começar a utilizar o Table storage do Azure num projeto ASP.NET Core no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligada a serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: 81f0304850a108fc688dd862ff5ab677d6ebc28e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Introdução ao Table storage do Azure e o Visual Studio como ligada a serviços

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Este artigo descreve como começar a utilizar o Table storage do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto ASP.NET Core utilizando o Visual Studio **serviços ligados** funcionalidade. O **serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto. (Consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Storage do Azure.)

O serviço de armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Para obter mais informações gerais sobre como utilizar o Table storage do Azure, consulte [introdução ao Table storage do Azure através do .NET](../storage/storage-dotnet-how-to-use-tables.md).

Para começar a utilizar, crie uma tabela na sua conta de armazenamento. Este artigo, em seguida, mostra como criar uma tabela em c# e como realizar operações de tabela básica, tais como adicionar, modificar, ler e remover entradas de tabela.  O código utiliza a biblioteca de clientes de armazenamento do Azure para .NET. Para mais informações sobre o ASP.NET, consulte [ASP.NET](http://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo parte do princípio de métodos assíncronos estão a ser utilizados. Consulte [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-tables-in-code"></a>Tabelas de acesso no código

Para aceder a tabelas nos projetos de ASP.NET Core, tem de incluir os seguintes itens para c# ficheiros de origem que acedem ao table storage do Azure.

1. Adicionar o necessário `using` instruções:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obter um `CloudStorageAccount` objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um `CloudTableClient` objeto referenciar os objetos de tabela na sua conta de armazenamento:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um `CloudTable` o objecto de referência para fazer referência a uma tabela específica e entidades:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable table = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Criar uma tabela no código

Para criar a tabela do Azure, chamada "CreateIfNotExistsAsync()':

```cs
// Create the CloudTable if it does not exist
await table.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código seguinte define uma classe de entidade chamada `CustomerEntity` que utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição.

```cs
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

A tabela que envolvem entidades utilização de operações a `CloudTable` objeto criado anteriormente na [acedam a tabelas no código](#access-tables-in-code). O `TableOperation` objeto representa a operação a ser realizado. Exemplo de código seguinte mostra como criar um `CloudTable` objeto e um `CustomerEntity` objeto. Para preparar a operação, uma `TableOperation` é criada para inserir a entidade de cliente na tabela. Por fim, a operação é executada chamando `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Pode inserir várias entidades numa tabela numa operação de escrita única. Exemplo de código seguinte cria dois objetos de entidade ("Jorge Santos" e "Bernardo Santos"), adiciona-lhes uma `TableBatchOperation` objeto utilizando o `Insert` método, e, em seguida, inicia a operação chamando `CloudTable.ExecuteBatchAsync`.

```cs
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades numa partição

Para consultar uma tabela para todas as entidades numa partição, utilize um `TableQuery` objeto. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Obter uma única entidade

Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza um `TableOperation` objeto para especificar um cliente com o nome "Bernardo Santos". O método devolve apenas uma entidade, em vez de uma coleção e o valor devolvido em `TableResult.Result` é um `CustomerEntity` objeto. A especificação de chaves de partição e da fila numa consulta é a forma mais rápida para obter uma única entidade a partir de `Table` serviço.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode eliminar uma entidade depois de encontrá-lo. O seguinte código procura e elimina uma entidade de cliente "Bernardo Santos" com o nome:

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
