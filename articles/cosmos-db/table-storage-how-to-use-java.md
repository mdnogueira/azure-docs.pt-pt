---
title: Como utilizar o Table storage do Azure de Java | Microsoft Docs
description: "Armazene dados estruturados na nuvem através do Table Storage do Azure, um arquivo de dados NoSQL."
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: f47e5dfb174a0c7b99111485cee74eb01df665c7
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-use-azure-table-storage-from-java"></a>Como utilizar o Table storage do Azure de Java
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Descrição geral
Este guia irá mostrar como efetuar cenários comuns utilizando o serviço de armazenamento de tabelas do Azure. Os exemplos são escritos em Java e utilize o [SDK de armazenamento do Azure para Java][Azure Storage SDK for Java]. Os cenários abrangidos incluem **criar**, **listagem**, e **eliminar** tabelas, bem como **a inserir**, **consultar**, **modificar**, e **eliminar** entidades numa tabela. Para obter mais informações sobre as tabelas, consulte o [passos](#Next-Steps) secção.

> [!NOTE]
> Um SDK está disponível para programadores que estiver a utilizar o armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte o [SDK de armazenamento do Azure para Android][Azure Storage SDK for Android].
>

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Neste guia, irá utilizar as funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação Java localmente ou numa código em execução numa função da web ou função de trabalho no Azure.

Para tal, terá de instalar o Kit de desenvolvimento Java (JDK) e criar uma conta de armazenamento do Azure na sua subscrição do Azure. Assim que tiver feito, terá de verificar se o sistema de desenvolvimento cumpre os requisitos mínimos e as dependências que são apresentadas no [SDK de armazenamento do Azure para Java] [ Azure Storage SDK for Java] repositório no GitHub. Se o seu sistema cumpre os requisitos, pode seguir as instruções para transferir e instalar as bibliotecas de armazenamento do Azure para Java no seu sistema desse repositório. Depois de concluir essas tarefas, poderá criar uma aplicação de Java que utiliza os exemplos neste artigo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de tabela
Adicione as seguintes declarações de importação na parte superior do ficheiro de Java em que pretende utilizar APIs de armazenamento do Microsoft Azure para aceder a tabelas:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Um cliente de armazenamento do Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e credenciais para aceder aos serviços de gestão de dados. Quando em execução numa aplicação de cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, com o nome da sua conta de armazenamento e a chave de acesso primária para a conta de armazenamento indicados no [portal do Azure](https://portal.azure.com) para o *AccountName* e *AccountKey* valores. Este exemplo mostra como podem declarar um campo estático para conter a cadeia de ligação:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Na execução aplicação dentro de uma função no Microsoft Azure, esta cadeia pode ser armazenada no ficheiro de configuração do serviço, *serviceconfiguration. Cscfg*e pode ser acedido através de uma chamada para o **RoleEnvironment.getConfigurationSettings** método. Eis um exemplo de obter a cadeia de ligação de um **definição** elemento com o nome *StorageConnectionString* no ficheiro de configuração do serviço:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="how-to-create-a-table"></a>Como: criar uma tabela
A **CloudTableClient** objeto permite-lhe obter objetos de referência para as tabelas e entidades. O código seguinte cria um **CloudTableClient** de objeto e utiliza-o para criar um novo **CloudTable** objecto que representa uma tabela com o nome "as pessoas". 

> [!NOTE]
> Existem formas adicionais para criar **CloudStorageAccount** objetos; para obter mais informações, consulte **CloudStorageAccount** no [referência do SDK de cliente de armazenamento de Azure].)
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-tables"></a>Como: listar as tabelas
Para obter uma lista de tabelas, chame o **CloudTableClient.listTables()** método para obter uma lista iterable de nomes de tabela.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-an-entity-to-a-table"></a>Como: adicionar uma entidade a uma tabela
As entidades mapeiam para objetos de Java utilizando uma classe personalizada implementar **TableEntity**. Para sua comodidade, a **TableServiceEntity** classe implementa **TableEntity** e utiliza reflexão para mapear propriedades para os métodos getter e setter com o nome para as propriedades. Para adicionar uma entidade a uma tabela, crie primeiro uma classe que define as propriedades de entidade. O código seguinte define uma classe de entidade que utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. Entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as chaves de partição diferentes.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

As operações de tabela que envolvem entidades requerem um **TableOperation** objeto. Este objeto define a operação ser efetuada numa entidade, que pode ser executada com um **CloudTable** objeto. O código seguinte cria uma nova instância do **CustomerEntity** classe com alguns dados de cliente para ser armazenado. O código seguinte chama **TableOperation.insertOrReplace** para criar um **TableOperation** de objeto a inserir uma entidade uma tabela e associa o novo **CustomerEntity** com o mesmo. Por fim, o código chama o **executar** método no **CloudTable** objeto, especificando a tabela "pessoas" e o novo **TableOperation**, que, em seguida, envia um pedido para o serviço de armazenamento para inserir a entidade de cliente novo na tabela "pessoas" ou substituir a entidade se já existir.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-a-batch-of-entities"></a>Como: Inserir um lote de entidades
Pode inserir um lote de entidades no serviço tabela numa operação de escrita. O código seguinte cria um **TableBatchOperation** objeto, em seguida, adiciona três operações ao mesmo de inserção. Cada operação de inserção é adicionada ao criar um novo objeto de entidade, os respetivos valores a definição e, em seguida, ao chamar o **inserir** método no **TableBatchOperation** objeto para associar a entidade com uma operação de inserção de novo. Em seguida, as chamadas de código **executar** no **CloudTable** objeto, especificar a tabela "pessoas" e o **TableBatchOperation** objeto, o que envia o lote de operações de tabela para o serviço de armazenamento num único pedido.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Algumas coisas a salientar nas operações de lote:

* Pode efetuar até 100 insert, eliminar, intercalar, substituir, inserir ou intercalar e inserir ou substituir as operações em qualquer combinação de um único lote.
* Uma operação em lote pode ter uma operação de obter, se for a única operação em lote.
* Todas as entidades numa única operação em lote têm de ter a mesma chave de partição.
* Uma operação em lote está limitada a um payload de dados de 4MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Como: obter todas as entidades numa partição
Para consultar uma tabela para entidades numa partição, pode utilizar um **TableQuery**. Chamar **TableQuery.from** para criar uma consulta numa determinada tabela que devolve um tipo de resultado especificado. O código seguinte especifica um filtro para entidades em que "Santos" é a chave de partição. **TableQuery.generateFilterCondition** é um método de programa auxiliar para criar filtros para consultas. Chamar **onde** na referência devolvida pelo **TableQuery.from** método para aplicar o filtro à consulta. Quando a consulta é executada com uma chamada para **executar** no **CloudTable** objeto devolve um **Iterator** com o **CustomerEntity** especificado do tipo de resultado. Em seguida, pode utilizar o **Iterator** devolvida uma para cada ciclo consumir os resultados. Este código imprime os campos de cada entidade nos resultados da consulta para a consola.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Como: obter um intervalo de entidades numa partição
Se não pretender consultar todas as entidades numa partição, pode especificar um intervalo através da utilização de operadores de comparação num filtro. O seguinte código combina dois filtros para obter todas as entidades numa partição "Santos", em que a chave de linha (nome próprio) começa com uma letra até 'E' do alfabeto. Em seguida, imprime os resultados da consulta. Se utilizar as entidades adicionadas à tabela no lote de inserir a secção deste guia, apenas duas entidades são devolvidas desta vez (Bernardo e Denise Santos); Jorge Santos não está incluído.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-single-entity"></a>Como: obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código chamadas **TableOperation.retrieve** com partição de chave e linha de chave de parâmetros para especificar o cliente "Jorge Santos", em vez de criar um **TableQuery** e utilizar filtros para fazer a mesma coisa. Quando executar a operação de obtenção devolve apenas uma entidade em vez de uma coleção. O **getResultAsType** método casts o resultado para o tipo de destino de atribuição, um **CustomerEntity** objeto. Se este tipo não é compatível com o tipo especificado para a consulta, será emitida uma exceção. Se nenhuma entidade tem uma partição exata e a chave de linha corresponder, é devolvido um valor nulo. Especificar as chaves de partição e da fila numa consulta é a forma mais rápida de obter uma única entidade a partir do serviço Tabela.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-modify-an-entity"></a>Como: modificar uma entidade
Para modificar uma entidade, obtê-lo a partir do serviço tabela, efetuar alterações ao objeto de entidade e guardar as alterações novamente para o serviço de tabela com uma operação de substituição ou intercalação. O código seguinte altera o número de telefone de um cliente existente. Em vez de chamar **TableOperation.insert** , tal como foi feito para inserir, este código chama **TableOperation.replace**. O **Cloudtable** método chama o serviço de tabela e a entidade é substituída, a menos que outra aplicação alterado-dentro do tempo, uma vez que esta aplicação obtido. Quando isso acontece, é emitida uma exceção e a entidade tem de ter obtida, modificar e guardar novamente. Neste padrão de repetição de simultaneidade otimista é comum num sistema de armazenamento distribuído.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-query-a-subset-of-entity-properties"></a>Como: consultar um subconjunto de propriedades de entidade
Uma consulta a uma tabela pode obter apenas algumas propriedades de uma entidade. Esta técnica, denominada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades grandes. A consulta no seguinte código utiliza o **selecione** método para devolver apenas os endereços de e-mail de entidades na tabela. Os resultados são projetados para uma coleção de **cadeia** com a ajuda de um **EntityResolver**, que tem a conversão do tipo entidades devolvido do servidor. Pode saber mais sobre a projeção na [tabelas do Azure: Upsert e da projeção da consulta][Azure Tables: Introducing Upsert and Query Projection]. Tenha em atenção que projeção não é suportada o emulador de armazenamento local, pelo que este código é executado apenas quando utilizar uma conta do serviço tabela.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-or-replace-an-entity"></a>Como: inserir ou substituir uma entidade
Muitas vezes, que pretende adicionar uma entidade a uma tabela sem saberem se já existir na tabela. Uma operação de inserção ou substituir permite-lhe efetuar um pedido único, que irá inserir a entidade se não existir ou substituir a existente se existir. O seguinte código compilar nos exemplos anteriores, insere ou substitui a entidade para "Walter Harp". Depois de criar uma nova entidade, este código chama o **TableOperation.insertOrReplace** método. Este código, em seguida, chama **executar** no **CloudTable** objeto com a tabela e a inserção ou operação de tabela como os parâmetros de substituição. Para atualizar apenas uma parte de uma entidade a **TableOperation.insertOrMerge** método pode ser utilizado em vez disso. Tenha em atenção que inserir ou substituir não é suportada no emulador de armazenamento local, pelo que este código é executado apenas quando utilizar uma conta do serviço tabela. Pode saber mais sobre inserir ou substituir e a inserção ou intercalação deste [tabelas do Azure: Upsert e da projeção da consulta][Azure Tables: Introducing Upsert and Query Projection].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-an-entity"></a>Como: eliminar uma entidade
Pode facilmente eliminar uma entidade depois de a ter obtido. Assim que a entidade é obtida, chamar **TableOperation.delete** com a entidade para eliminar. Em seguida, chame **executar** no **CloudTable** objeto. O código seguinte obtém e elimina uma entidade de cliente.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-table"></a>Como: eliminar uma tabela
Por fim, o seguinte código elimina uma tabela a partir de uma conta de armazenamento. Uma tabela que foi eliminada estará indisponível para ser recriada durante um período de tempo após a eliminação, normalmente, menos de forty segundos.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```
[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Passos seguintes

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* [Armazenamento do Azure SDK para Java][Azure Storage SDK for Java]
* [Referência do SDK de cliente de armazenamento do Azure][referência do SDK de cliente de armazenamento de Azure]
* [API REST do Storage do Azure][Azure Storage REST API]
* [Blogue da equipa do Storage do Azure][Azure Storage Team Blog]

Para obter mais informações, visite [Azure para programadores Java](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[referência do SDK de cliente de armazenamento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
