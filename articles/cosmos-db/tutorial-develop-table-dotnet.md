---
title: 'Azure Cosmos DB: Desenvolver com a API de tabela no .NET | Microsoft Docs'
description: "Saiba como desenvolver com a API de tabela da BD do Cosmos do Azure através do .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: 0e77ecc591173ae29311c2a1508e5a8a907816ac
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: Desenvolver com a API de tabela no .NET

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB.

Este tutorial abrange as seguintes tarefas: 

> [!div class="checklist"] 
> * Criar uma conta do Azure Cosmos DB 
> * Ativar a funcionalidade no ficheiro App. config 
> * Criar uma tabela utilizando o [API de tabela](table-introduction.md)
> * Adicionar uma entidade a uma tabela 
> * Inserir um lote de entidades 
> * Obter uma única entidade 
> * Entidades de consulta utilizando índices secundários automáticos 
> * Substituir uma entidade 
> * Eliminar uma entidade 
> * Eliminar uma tabela
 
## <a name="tables-in-azure-cosmos-db"></a>Tabelas no Azure Cosmos DB 

BD do Azure do Cosmos fornece o [API de tabela](table-introduction.md) para aplicações que necessitam de um arquivo de chave-valor com um design sem esquema. Ambas as API de tabela de base de dados do Azure Cosmos e [Table storage do Azure](../storage/common/storage-introduction.md) agora suportam as mesmas SDKs e REST APIs. Pode utilizar o Azure Cosmos DB para criar tabelas com requisitos de débito elevado.

Este tutorial é para os programadores que estão familiarizado com o Table storage do Azure SDK e gostaria de utilizar as funcionalidades premium disponíveis com base de dados do Azure Cosmos. Se baseia no [introdução ao Table storage do Azure através do .NET](table-storage-how-to-use-dotnet.md) e mostra como tirar partido das funcionalidades adicionais, como índices secundários, débito aprovisionado e multi homing. Este tutorial descreve como utilizar o portal do Azure para criar uma conta de base de dados do Azure Cosmos e, em seguida, criar e implementar uma aplicação API de tabela. Podemos também guiá exemplos do .NET para criar e eliminar uma tabela e inserir, atualizar, eliminar e consultar os dados de tabela. 

Se utilizar atualmente o Table storage do Azure, obtém os seguintes benefícios com a API de tabela do Azure Cosmos DB:

- Por sua vez chave [distribuição global](distribute-data-globally.md) com multi homing e [as ativações pós-falha automáticas e manual](regional-failover.md)
- Suporte para automática desconhecidas do esquema de indexação contra todas as propriedades ("índices secundários") e consultas rápidas 
- Suporte para [dimensionamento independente de armazenamento e débito](partition-data.md), em qualquer número de regiões
- Suporte para [dedicado débito por tabela](request-units.md) que pode ser ampliada de centenas para milhões de pedidos por segundo
- Suporte para [cinco níveis de consistência ajustáveis pelo](consistency-levels.md) comprometido disponibilidade, a latência e consistência com base na sua aplicação requer o
- 99,99% de disponibilidade dentro de uma única região e capacidade de adicionar mais regiões para maior disponibilidade, e [SLAs abrangentes líder da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) na disponibilidade geral
- Trabalhar com o .NET SDK de armazenamento do Azure existente e não existem alterações de código a sua aplicação

Este tutorial abrange Cosmos DB tabela API do Azure utilizando o SDK .NET. Pode transferir o [SDK de pré-visualização de armazenamento do Azure](https://aka.ms/tableapinuget) do NuGet.

Para saber mais sobre as tarefas de armazenamento de tabelas do Azure complexas, consulte:

* [Introdução ao Azure Cosmos API de tabela de base de dados](table-introduction.md)
* A documentação de referência de serviço tabela para obter detalhes completos sobre as APIs disponíveis [Cosmos DB tabela API SDK do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial é para programadores que estão familiarizado com o Table storage do Azure SDK e gostaria de utilizar as funcionalidades premium disponíveis através de BD do Cosmos do Azure. Se baseia no [introdução ao Table storage do Azure através do .NET](table-storage-how-to-use-dotnet.md) e mostra como tirar partido das funcionalidades adicionais, como índices secundários, débito aprovisionado e multi homing. Iremos abranger como utilizar o portal do Azure para criar uma conta de base de dados do Azure Cosmos e, em seguida, criar e implementar uma aplicação de tabela. Podemos também guiá exemplos do .NET para criar e eliminar uma tabela e inserir, atualizar, eliminar e consultar os dados de tabela. 

Se ainda não tiver o Visual Studio 2017, instalado, pode transferir e utilizar o **livre** [edição de Comunidade de 2017 do Visual Studio](https://www.visualstudio.com/downloads/). Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Vamos começar por criar uma conta de base de dados do Azure Cosmos no portal do Azure.  

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de Tabela a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada. 

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    Utilize os botões de cópia no lado direito do ecrã para copiar a cadeia de ligação.

    ![Ver e copiar a cadeia de ligação no painel de cadeia de ligação](./media/create-table-dotnet/connection-string.png)

2. No Visual Studio, abra o ficheiro app.config. 

3. Cole o valor da cadeia de ligação no ficheiro App. config, como o valor de CosmosDBStorageConnectionString. 

    `<add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.net" />`    

    > [!NOTE]
    > Para utilizar esta aplicação com o Table storage do Azure, terá de alterar a cadeia de ligação no `app.config file`. Utilize o nome da conta como nome da conta de tabela e a chave como chave primária de armazenamento do Azure. <br>
    >`<add key="StandardStorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
    > 
    >

4. Guarde o ficheiro App. config.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="azure-cosmos-db-capabilities"></a>Capacidades do Cosmos BD do Azure
BD do Azure do Cosmos suporta um número de capacidades que não estão disponíveis no armazenamento de tabelas do Azure API. 

Algumas funcionalidades são acedidas através do novo sobrecargas para CreateCloudTableClient que permitem uma especificar o nível de política e consistência de ligação.

| Definições de ligação da tabela | Descrição |
| --- | --- |
| Modo de ligação  | BD do Cosmos do Azure suporta dois modos de conectividade. No `Gateway` modo, são efetuados os pedidos sempre para o gateway de BD do Cosmos do Azure, que reencaminha-lo para as partições de dados correspondente. No `Direct` modo de conectividade, o cliente obtém o mapeamento de tabelas de partições e pedidos são realizados diretamente em partições de dados. Recomendamos `Direct`, a predefinição.  |
| Protocolo de ligação | BD do Cosmos do Azure suporta dois protocolos de ligação - `Https` e `Tcp`. `Tcp`é o predefinido e recomendado porque é mais simples. |
| Localizações preferenciais | Lista separada por vírgulas das localizações (multi homing) preferenciais para leituras. Cada conta de base de dados do Azure Cosmos pode ser associada com 1-30 + regiões. Cada instância de cliente pode especificar um subconjunto destas regiões pela ordem preferencial para leituras de latência baixa. As regiões devem ter o nome a utilizar as respetivas [nomes a apresentar](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`. Consulte também [APIs multi homing](tutorial-global-distribution-table.md). |
| Nível de Consistência | Pode comprometido entre a latência de consistência, disponibilidade e ao escolher entre cinco níveis de consistência bem definidos: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix`, e `Eventual`. Predefinição é `Session`. A escolha de nível de consistência faz uma diferença significativa do desempenho no multirregião setups. Consulte [níveis de consistência](consistency-levels.md) para obter mais detalhes. |

Outras funcionalidades, podem ser ativada através do seguinte `appSettings` valores de configuração.

| Chave | Descrição |
| --- | --- |
| TableThroughput | Débito reservado para a tabela expressado em unidades de pedido (RU) por segundo. Tabelas único podem suportar 100s-milhões de RU/s. Consulte [unidades de pedido](request-units.md). Predefinição é`400` |
| TableIndexingPolicy | Cadeia JSON cumprindo a especificação de política de indexação. Consulte [política de indexação](indexing-policies.md) para ver como pode alterar a política de indexação para incluir/excluir colunas específicas. |
| TableQueryMaxItemCount | Configure o número máximo de itens devolvidos por uma consulta de tabela numa único ida e volta. Predefinição é `-1`, que permite a BD do Cosmos Azure determinar dinamicamente o valor no tempo de execução. |
| TableQueryEnableScan | Se a consulta não é possível utilizar o índice para qualquer filtro, em seguida, execute este mesmo assim através de uma análise. Predefinição é `false`.|
| TableQueryMaxDegreeOfParallelism | O grau de paralelismo para execução de uma consulta de partição cruzada. `0`é a série com sem obter previamente, `1` é a série com valores de pré-obter e superiores aumentar a taxa de paralelismo. Predefinição é `-1`, que permite a BD do Cosmos Azure determinar dinamicamente o valor no tempo de execução. |

Para alterar o valor predefinido, abra o `app.config` ficheiro a partir do Explorador de soluções no Visual Studio. Adicione o conteúdo do elemento `<appSettings>` mostrado abaixo. Substitua `account-name` com o nome da sua conta do storage e `account-key` com a chave de acesso da conta. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o `Program.cs` de ficheiros e de irá encontrar que estas linhas de código criar os recursos de tabela. 

## <a name="create-the-table-client"></a>Criar o cliente de tabela
A inicializar um `CloudTableClient` para ligar à conta de tabela.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Este cliente é iniciado utilizando a `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel`, e `TablePreferredLocations` valores de configuração se especificado nas definições da aplicação.

## <a name="create-a-table"></a>Criar uma tabela
Em seguida, crie uma tabela utilizando `CloudTable`. As tabelas na base de dados do Azure Cosmos podem dimensionar de forma independente em termos de armazenamento e débito e criação de partições é processada pelo serviço automaticamente. BD do Azure do Cosmos suporta tamanho fixo e tabelas ilimitadas. Consulte [criação de partições do BD Azure Cosmos](partition-data.md) para obter mais detalhes. 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

Há uma diferença importante na forma como são criadas tabelas. BD do Azure do Cosmos reserva-se o débito, ao contrário do modelo de armazenamento do Azure baseada no consumo para transações. O débito é dedicado/reservado, para que nunca obter limitadas se a taxa de pedidos é igual ou abaixo o débito aprovisionado.

Pode configurar o débito de predefinido ao configurar a definição de `TableThroughput` em termos de RU (unidades de pedido) por segundo. 

Uma leitura de uma entidade de 1 KB é normalizada como 1 RU e outras operações são normalizados para um valor de RU fixo com base no respetivo consumo de CPU, memória e IOPS. Saiba mais sobre [unidades do BD Azure Cosmos de pedido](request-units.md) e especificamente para [arquivos de valor de chave](key-value-store-cost.md).

Em seguida, iremos orientá-durante a leitura simple operações e escrita (CRUD) utilizando o Table storage do Azure SDK. Este tutorial demonstra latências previsíveis milissegundo dígito baixa e consultas rápidas fornecidas pela base de dados do Azure Cosmos.

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Expandem entidades no Table storage do Azure a partir de `TableEntity` classe e tem de ter `PartitionKey` e `RowKey` propriedades. Segue-se uma definição de exemplo para uma entidade de cliente.

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

O fragmento seguinte mostra como inserir uma entidade com o SDK de armazenamento do Azure. BD do Azure do Cosmos foi concebido para garantida baixa latência em qualquer escala, por todo o mundo.

Escritas concluir < 15 ms p99 e ms ~ 6 em p50 para aplicações em execução na mesma região que a conta de base de dados do Azure Cosmos. E esta duração de contas para o facto de que as escritas são confirmadas novamente para o cliente apenas depois de estes estão em sincronia replicados, consolidada de forma durável, e todo o conteúdo está indexado.


```csharp
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
Suporte de armazenamento do Azure tabela uma operação em lote API, que lhe permite combinar as atualizações, eliminações e inserções na mesma operação batch.

```csharp
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
## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
Obtém (obtém) no Azure Cosmos BD concluída < 10 ms p99 e ~ 1 ms em p50 na mesma região do Azure. Pode adicionar tantos regiões à sua conta para leituras de latência baixa e implementar aplicações para ler a partir do respetivo região local ("multihomed") definindo `TablePreferredLocations`. 

Pode obter uma única entidade através do seguinte fragmento:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Saiba mais sobre APIs multi homing em [desenvolver com várias regiões](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Entidades de consulta utilizando índices secundários automáticos
As tabelas podem ser consultadas utilizando o `TableQuery` classe. BD do Azure do Cosmos tem um motor de base de dados com otimização de escrita que indexa automaticamente todas as colunas na sua tabela. A indexação do BD Azure Cosmos é agnóstico em termos de esquema. Por conseguinte, mesmo se o esquema for diferente entre as linhas, ou se o esquema medida que evolui ao longo do tempo, é automaticamente indexado. Uma vez que a base de dados do Azure Cosmos suporta índices secundários automáticos, consultas em relação a qualquer propriedade podem utilizar o índice e ser servidas de forma eficiente.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

BD do Cosmos do Azure suporta a mesma funcionalidade de consulta que o Table storage do Azure para a API de tabela. BD do Azure do Cosmos também suporta a ordenação, os agregados, geoespacial consulta, hierarquia e uma vasta gama de funções incorporadas. As funcionalidades adicionais serão fornecidas na API tabela numa atualização futura do serviço. Consulte [consulta de base de dados do Azure Cosmos](documentdb-sql-query.md) para uma descrição geral destas capacidades. 

## <a name="replace-an-entity"></a>Substituir uma entidade
Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, guarde as alterações novamente no serviço Tabela. O código seguinte altera o número de telefone de um cliente existente. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Da mesma forma, pode realizar `InsertOrMerge` ou `Merge` operações.  

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Eliminar uma tabela
Finalmente, o seguinte exemplo de código elimina uma tabela a partir de uma conta do Storage. Pode eliminar e recriar a tabela imediatamente com a base de dados do Azure Cosmos.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Limpar recursos 

Se não pretender continuar a utilizar esta aplicação, siga os passos abaixo para eliminar todos os recursos criados por este tutorial no portal do Azure.   

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou.  
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, iremos abordadas como começar a utilizar a base de dados do Azure Cosmos com a API de tabela e fez o seguinte: 

> [!div class="checklist"] 
> * Criar uma conta de base de dados do Azure Cosmos 
> * Ativada a funcionalidade no ficheiro App. config 
> * Criar uma tabela 
> * Adicionar uma entidade a uma tabela 
> * Inserir um lote de entidades 
> * Obter uma única entidade 
> * Entidades consultadas com índices secundários automáticos 
> * Substituir uma entidade 
> * Eliminar uma entidade 
> * Eliminar uma tabela  

Agora pode avançar para o próximo tutorial e saber mais sobre a consultar os dados de tabela. 

> [!div class="nextstepaction"]
> [Consulta com a tabela de API](tutorial-query-table.md)
