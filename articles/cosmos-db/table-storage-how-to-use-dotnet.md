---
title: "Introdução ao Armazenamento de Tabelas do Azure através do .NET | Microsoft Docs"
description: "Armazene dados estruturados na nuvem através do Table Storage do Azure, um arquivo de dados NoSQL."
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 04/10/2017
ms.author: mimig
ms.openlocfilehash: fadad88be94552dd1db061146d8dfab86d797f61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Introdução ao Table Storage do Azure através do .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

O Armazenamento de tabelas do Azure é um serviço que armazena dados NoSQL estruturados na cloud, fornecendo uma loja de atributos/chaves com um design sem esquemas. Uma vez que o Table Storage não tem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso a dados do Armazenamento de tabelas é rápido e rentável para muitos tipos de aplicações, e é normalmente mais económico do que o SQL tradicional para semelhantes volumes de dados.

Pode utilizar o Armazenamento de Tabelas para armazenar conjuntos de dados flexíveis, como os dados do utilizador para aplicações Web, livros de endereços, informações do dispositivo, ou outros tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

### <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial mostra-lhe como utilizar a [Biblioteca de Cliente de Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) em alguns cenários comuns de Armazenamento de tabelas do Azure. Estes cenários são apresentados com exemplos C#, para criar e eliminar uma tabela e inserir, atualizar, eliminar e consultar dados de tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial com êxito, precisa do seguinte:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Biblioteca de Clientes do Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Gestor de Configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais exemplos
Para obter exemplos adicionais que utilizam o Armazenamento de Tabelas, veja [Introdução ao Armazenamento de Tabelas do Azure do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Pode transferir a aplicação de exemplo e executá-la ou procurar o código no GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Adicionar com diretivas
Adicione as seguintes diretivas de **utilização** à parte superior do ficheiro `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Criar o cliente do serviço Tabela
A classe [CloudTableClient][dotnet_CloudTableClient] permite-lhe obter tabelas e entidades armazenadas no Armazenamento de tabelas. Eis uma forma de criar o Cliente do serviço de tabelas:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Agora, está pronto para escrever código que lê dados de e escreve dados para o Table Storage.

## <a name="create-a-table"></a>Criar uma tabela
Este exemplo mostra como criar uma tabela se ainda não existir:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
As entidades mapeiam para objetos C# através de uma classe personalizada derivada de [TableEntity][dotnet_TableEntity]. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código seguinte define uma classe de entidade que utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam-na de forma exclusiva na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. As entidades a serem armazenadas em tabelas têm de ter um tipo suportado, por exemplo derivadas da classe [TableEntity][dotnet_TableEntity]. As propriedades de entidade que pretende armazenar numa tabela têm de ser propriedades públicas do tipo e suportar a introdução e a definição de valores. Além disso, o tipo de entidade *tem de* expor um construtor sem parâmetros.

```csharp
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

As operações de tabela que envolvem entidades são efetuadas através do objeto [CloudTable][dotnet_CloudTable] que criou anteriormente na secção "Criar uma tabela". A operação a realizar é representada por um objeto [TableOperation][dotnet_TableOperation]. O exemplo de código seguinte mostra a criação do objeto [CloudTable][dotnet_CloudTable] e, em seguida, um objeto **CustomerEntity**. Para preparar a operação, é criado um objeto [TableOperation][dotnet_TableOperation] para inserir a entidade de cliente na tabela. Por fim, a operação é executada chamando [CloudTable][dotnet_CloudTable].[Executar][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir um lote de entidades numa tabela numa operação de escrita. Algumas outras notas sobre operações em lote:

* Pode efetuar atualizações, eliminações e inserções na mesma operação em lote única.
* Uma única operação em lote pode incluir até 100 entidades.
* Todas as entidades numa única operação em lote têm de ter a mesma chave de partição.
* Embora seja possível executar uma consulta como uma operação em lote, tem de ser a única operação no lote.

O exemplo de código seguinte cria dois objetos de entidade e adiciona cada um deles a [TableBatchOperation][dotnet_TableBatchOperation] através do método [Inserir][dotnet_TableBatchOperation_Insert]. Em seguida, chama-se [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] para executar a operação.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para consultar uma tabela para todas as entidades numa partição, utilize um objeto [TableQuery][dotnet_TableQuery]. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades numa partição
Se não pretender consultar todas as entidades de uma partição, pode especificar um intervalo através da combinação do filtro da chave de partição com um filtro da chave da fila. O exemplo de código seguinte utiliza dois filtros para obter todas as entidades na partição “Santos”, em que a chave da fila (nome próprio) começa com uma letra anterior ao “E” do alfabeto e, em seguida, imprime os resultados da consulta.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza [TableOperation][dotnet_TableOperation] para especificar o cliente “Bernardo Santos”. Este método devolve apenas uma entidade em vez de uma coleção e o valor devolvido em [TableResult.Result][dotnet_TableResult].[Resultado][dotnet_TableResult_Result] é um objeto **CustomerEntity**. Especificar as chaves de partição e da fila numa consulta é a forma mais rápida de obter uma única entidade a partir do serviço Tabela.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Substituir uma entidade
Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, guarde as alterações novamente no serviço Tabela. O código seguinte altera o número de telefone de um cliente existente. Em vez de chamar [Inserir][dotnet_TableOperation_Insert], este código utiliza [Substituir][dotnet_TableOperation_Replace]. [Substituir][dotnet_TableOperation_Replace] faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi obtida, caso em que a operação falhará. Esta falha é para impedir que a sua aplicação substitua inadvertidamente uma alteração efetuada entre a obtenção e a atualização por outro componente da aplicação. O processamento adequado desta falha é para obter a entidade novamente, efetuar as alterações (se ainda forem válidas) e, em seguida, executar outra operação de [Substituição][dotnet_TableOperation_Replace]. A secção seguinte irá mostrar como substituir este comportamento.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade
As operações de [Substituição][dotnet_TableOperation_Replace] irão falhar se a entidade tiver sido alterada desde que foi obtida do servidor. Além disso, tem de obter primeiro a entidade do servidor para que a operação de [Substituição][dotnet_TableOperation_Replace] seja concluída com êxito. Por vezes, no entanto, não sabe se a entidade existe no servidor e os valores atuais armazenados na mesma são irrelevantes. A atualização deve substituí-los a todos. Para tal, utilizaria uma operação [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Esta operação insere a entidade se esta não existir ou substitui-a se existir, independentemente de quando foi efetuada a última atualização.

No seguinte exemplo de código, uma entidade de cliente para "Pedro Silva" é criada e inserida na tabela "pessoas". Em seguida, utilizamos a operação [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] para guardar uma entidade com a mesma chave de partição (Silva) e a chave de linha (Pedro) para o servidor, desta vez com um valor diferente para a propriedade PhoneNumber. Uma vez que utilizamos [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], todos os respetivos valores de propriedade são substituídos. No entanto, se uma entidade "Pedro Silva" já não existir na tabela, este seria inserido.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade
Uma consulta de tabela pode obter apenas algumas propriedades de uma entidade em vez de todas as propriedades de entidade. Esta técnica, denominada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades grandes. A consulta no seguinte código devolve apenas os endereços de e-mail de entidades na tabela. Isto é feito utilizando uma consulta de [DynamicTableEntity][dotnet_DynamicTableEntity] e também [EntityResolver][dotnet_EntityResolver]. Pode saber mais sobre a projeção na [Mensagem de blogue Apresentação do Upsert e da Projeção da Consulta][blog_post_upsert]. A projeção não é suportada pelo emulador de armazenamento, pelo que este código é executado apenas quando estiver a utilizar uma conta do serviço Tabela.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Eliminar uma tabela
Finalmente, o seguinte exemplo de código elimina uma tabela a partir de uma conta do Storage. Uma tabela que foi eliminada estará indisponível para ser recriada durante um determinado período de tempo após a eliminação.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Obter entidades em páginas no modo assíncrono
Se estiver a ler um grande número de entidades e pretende processar/apresentar entidades à medida que são obtidas em vez de aguardar que todas regressem, pode obter entidades utilizando uma consulta segmentada. Este exemplo mostra como devolver resultados nas páginas utilizando o padrão Async-Await para que a execução não esteja bloqueada enquanto está a aguardar pela devolução de um grande conjunto de resultados. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Programação assíncrona com Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do Table Storage, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas:

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* Ver mais exemplos do Armazenamento de Tabelas em [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:
* [Referência da Biblioteca de Clientes do Storage para o .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Referência da API REST](http://msdn.microsoft.com/library/azure/dd179355)
* Saiba como simplificar o código de escrita para trabalhar com o Storage do Azure utilizando o [SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.
* [Introdução ao Blob Storage do Azure utilizando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
* [Ligar à Base de Dados SQL com o .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
