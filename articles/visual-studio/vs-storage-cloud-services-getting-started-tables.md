---
title: "Introdução ao armazenamento de tabelas e o Visual Studio ligado services (Serviços de cloud) | Microsoft Docs"
description: "Como começar a utilizar o Table storage do Azure num projeto de serviço em nuvem no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio ligada a serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 51b71d783806d9b0d58d4473b8c07f77441dadd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao table storage do Azure e o Visual Studio ligado serviços (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o table storage do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto de serviços em nuvem utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo. O **adicionar serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto.

O serviço de armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Para começar, terá primeiro de criar uma tabela na sua conta de armazenamento. Vamos mostrar-lhe como criar uma tabela do Azure no código e também como tabela básica e operações de entidade, tais como adicionar, modificar, ler e ler as entidades da tabela. Os exemplos são escritos no C\# código e utilizar o [biblioteca de clientes de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Nota:** algumas das APIs que efetuar chamadas enviados para o armazenamento do Azure são assíncronas. Consulte [programação assíncrona com Async-Await](http://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código abaixo parte do princípio de métodos de programação assíncrona estão a ser utilizados.

* Consulte [introdução ao Table storage do Azure através do .NET](../storage/storage-dotnet-how-to-use-tables.md) para obter mais informações sobre manipular programaticamente tabelas.
* Consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Storage do Azure.
* Consulte [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços em nuvem do Azure.
* Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre a programação de aplicações do ASP.NET.

## <a name="access-tables-in-code"></a>Tabelas de acesso no código
Para aceder a tabelas no projetos do serviço em nuvem, terá de incluir os seguintes itens para c# ficheiros de origem que acedem ao table storage do Azure.

1. Certifique-se de que as declarações de espaço de nomes na parte superior do ficheiro c# incluem estes **utilizando** instruções.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Utilize todo o código acima à frente o código de nos exemplos seguintes.
   > 
   > 
3. Obter um **CloudTableClient** objeto referenciar os objetos de tabela na sua conta de armazenamento.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Obter um **CloudTable** o objecto de referência para fazer referência a uma tabela específica e entidades.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Criar uma tabela no código
Para criar a tabela do Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync** para o depois um **CloudTable** objeto conforme descrito na secção "Acedam a tabelas no código".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código seguinte define uma classe de entidade chamada **CustomerEntity** que utiliza o nome próprio do cliente como a chave de linha e o apelido como a chave de partição.

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

Operações da tabela que envolvem entidades são efetuadas utilizando o **CloudTable** objeto que criou anteriormente no "Tabelas de acesso no código." O **TableOperation** objeto representa a operação a ser realizado. Exemplo de código seguinte mostra como criar um **CloudTable** objeto e um **CustomerEntity** objeto. Para preparar a operação, uma **TableOperation** é criada para inserir a entidade de cliente na tabela. Por fim, a operação é executada chamando **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir várias entidades numa tabela numa operação de escrita única. Exemplo de código seguinte cria dois objetos de entidade ("Jorge Santos" e "Bernardo Santos"), adiciona-lhes uma **TableBatchOperation** utilizando o método de inserção de objeto e, em seguida, inicia a operação chamando **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para consultar uma tabela para todas as entidades numa partição, utilize um **TableQuery** objeto. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza um **TableOperation** objeto para especificar um cliente com o nome "Bernardo Santos". Este método devolve apenas uma entidade, em vez de uma coleção e o valor devolvido em **Tableresult** é um **CustomerEntity** objeto. A especificação de chaves de partição e da fila numa consulta é a forma mais rápida para obter uma única entidade a partir de **tabela** serviço.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode eliminar uma entidade depois de encontrá-lo. O seguinte código procura uma entidade de cliente "Bernardo Santos" com o nome, e se encontrá-lo, elimina-a.

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

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

