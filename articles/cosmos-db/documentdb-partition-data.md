---
title: "Criação de partições e dimensionamento do BD Azure Cosmos | Microsoft Docs"
description: "Saiba mais sobre funciona como criação de partições de BD do Cosmos do Azure, como configurar a criação de partições e chaves de partição e como escolher a chave de partição adequado para a sua aplicação."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81010d91ac7fe8fa7149c52ed56af304cf4e83d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="partitioning-in-azure-cosmos-db-using-the-documentdb-api"></a>A criação de partições na base de dados do Cosmos do Azure utilizando a API do DocumentDB

[Base de dados do Microsoft Azure Cosmos](../cosmos-db/introduction.md) é um serviço de base de dados distribuída, com vários modelo global concebido para ajudar a alcançar o desempenho previsível, rápido e dimensionamento de forma totalmente integrada, juntamente com a aplicação à medida que o que aumenta. 

Este artigo fornece uma descrição geral de como trabalhar com a criação de partições de contentores de BD do Cosmos com a API do DocumentDB. Consulte [criação de partições e dimensionamento horizontal](../cosmos-db/partition-data.md) para uma descrição geral dos conceitos e procedimentos recomendados para a criação de partições com qualquer API de BD do Cosmos do Azure. 

Para começar a utilizar com o código, transfira o projeto de [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Depois de ler este artigo, poderá responder às seguintes questões:   

* Como funciona o trabalho de criação de partições do BD Azure Cosmos?
* Como configurar a criação de partições do BD Azure Cosmos
* Quais são as chaves de partição e como escolher a chave de partição adequado para a minha aplicação?

Para começar a utilizar com o código, transfira o projeto de [Azure Cosmos DB desempenho testar controlador Sample](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Chaves de partição

Na API do DocumentDB, especifique a definição de chave de partição sob a forma de um caminho JSON. A tabela seguinte mostra exemplos de definições de chave de partição e os valores correspondentes para cada um. A chave de partição está especificada como um caminho, por exemplo, `/department` representa o departamento de propriedade. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Chave de partição</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Corresponde ao valor de doc.department onde o documento é o item.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Propriedades/nome</p></td>
            <td valign="top"><p>Corresponde ao valor de doc.properties.name onde o documento é o item (propriedade aninhada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>formado</p></td>
            <td valign="top"><p>Corresponde ao valor de doc.id (chave de partição e id são a mesma propriedade).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "nome do departamento"</p></td>
            <td valign="top"><p>Corresponde ao valor do documento "[departamento" nome], onde o documento é o item.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> A sintaxe para a chave de partição é semelhante a especificação de caminho para os caminhos de política com a diferença de chave que o caminho corresponde à propriedade em vez do valor de indexação, ou seja, não existe nenhum caráter universal no fim. Por exemplo, tem de especificar/departamento /? para os valores no departamento de índice, mas especificar /department como a definição de chave de partição. A chave de partição está implicitamente indexada e não pode ser excluída da indexação utilizando indexação de política de substituições.
> 
> 

Vamos ver como a escolha de chave de partição tem impacto no desempenho da aplicação.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Trabalhar com os SDKs do Azure Cosmos DB
BD do Azure do Cosmos suporte adicionado para criação de partições automático com [API de REST versão 2015-12-16](/rest/api/documentdb/). Para criar contentores particionadas, tem de transferir versões do SDK 1.6.0 ou mais recente de uma das seguintes as plataformas suportadas do SDK (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Criar contentores
O exemplo seguinte mostra um fragmento de .NET para criar um contentor para armazenar dados de telemetria do dispositivo 20.000 de unidades de pedido por segundo de débito. O SDK define o valor de OfferThroughput (que por sua vez, define o `x-ms-offer-throughput` cabeçalho do pedido na REST API). Aqui iremos definir o `/deviceId` como a chave de partição. A escolha de chave de partição é guardada juntamente com o resto dos metadados do contentor como o nome e a política de indexação.

Para este exemplo, vamos selecionado `deviceId` uma vez que Sabemos que (a) uma vez que existe um grande número de dispositivos, escritas podem ser distribuídas uniformemente por partições e permitir-nos para dimensionar a base de dados para a ingestão de grandes volumes de dados e (b) muitos pedidos, como obter a leitura mais recente para um dispositivo estão confinados a uma única deviceId e podem ser obtidos a partir de uma única partição.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Este método permite uma API de REST a chamada para a base de dados do Cosmos e o serviço irá aprovisionar um número de partições com base no débito pedido. Pode alterar o débito de um contentor à medida que o desempenho das necessidades evoluem. 

### <a name="reading-and-writing-items"></a>Ler e escrever itens
Agora, vamos inserir dados na base de dados do Cosmos. Segue-se uma classe de exemplo que contém um dispositivo ao ler e chamar CreateDocumentAsync para inserir um novo dispositivo ler num contentor. Este é um exemplo tirar partido da API do DocumentDB:

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Vamos o item pela sua chave de partição e o id de leitura, atualizá-lo e como passo final, elimine-o através da chave de partição e o id. Tenha em atenção que as leituras incluem um valor de PartitionKey (correspondente para o `x-ms-documentdb-partitionkey` cabeçalho do pedido na REST API).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Consultar particionadas contentores
Quando consulta os dados nos contentores particionadas, base de dados do Cosmos encaminha automaticamente a consulta para as partições correspondente para os valores de chave de partição especificados no filtro (se existir alguma). Por exemplo, esta consulta é encaminhada para a partição que contém a chave de partição "XMS-0001 e".

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
A seguinte consulta não tem um filtro a chave de partição (DeviceId) e fanned para todas as partições onde é executada relativamente o índice a particionar. Tenha em atenção que tem de especificar o EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` na REST API) com o SDK para executar uma consulta em partições.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Suporte cosmos DB [as funções de agregação](documentdb-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` e `AVG` over particionada contentores com o SQL a partir de com SDKs 1.12.0 e acima. As consultas têm de incluir um operador de agregação único e tem de incluir um valor único na projeção.

### <a name="parallel-query-execution"></a>Execução paralela da consulta
Os SDKs de BD do Cosmos 1.9.0 e acima opções de execução paralela da consulta de suporte, que permitem-lhe executar consultas de latência baixa contra coleções particionadas, mesmo quando precisar de touch um grande número de partições. Por exemplo, a seguinte consulta está configurada para ser executado em paralelo em partições.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Pode gerir a execução paralela da consulta por otimização os seguintes parâmetros:

* Por definição `MaxDegreeOfParallelism`, pode controlar o grau de paralelismo ou seja, o número máximo de ligações de rede em simultâneo para partições do contentor. Se definir esta opção como -1, o grau de paralelismo é gerido pelo SDK. Se o `MaxDegreeOfParallelism` não está especificado ou foi definido para 0, o qual é o valor predefinido, existirá uma ligação de rede único para partições do contentor.
* Por definição `MaxBufferedItemCount`, pode comprometido a utilização de memória de latência e do lado do cliente de consulta. Se omitir este parâmetro ou defina esta opção como -1, o número de itens em memória intermédia durante a execução paralela da consulta é gerido pelo SDK.

Tendo em conta o mesmo Estado da coleção, uma consulta paralela irá devolver resultados pela mesma ordem como em execução série. Quando executar uma consulta de partição cruzada que inclui a ordenação (ORDER BY e/ou superior), o SDK de BD do Cosmos Azure emite a consulta em paralelo em partições e intercala parcialmente ordenados resultados no lado do cliente para produzir resultados globalmente ordenados.

### <a name="executing-stored-procedures"></a>Ao executar procedimentos armazenados
Também pode executar transações atómicas contra documentos com o mesmo ID de dispositivo, por exemplo, se estiver a manter o estado mais recente de um dispositivo de um único item ou agregados. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
Na secção seguinte, vamos ver como pode mover a contentores particionadas de contentores de partição única.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, fornecemos-lhe uma descrição geral de como trabalhar com a criação de partições de contentores de BD do Cosmos do Azure com a API do DocumentDB. Consulte também [criação de partições e dimensionamento horizontal](../cosmos-db/partition-data.md) para uma descrição geral dos conceitos e procedimentos recomendados para a criação de partições com qualquer API de BD do Cosmos do Azure. 

* Efetue o dimensionamento e desempenho de teste com base de dados do Azure Cosmos. Consulte [desempenho e dimensionamento de teste com base de dados do Azure Cosmos](performance-testing.md) para um exemplo.
* Introdução à programação com o [SDKs](documentdb-sdk-dotnet.md) ou [REST API](/rest/api/documentdb/)
* Saiba mais sobre [débito aprovisionado do BD Azure Cosmos](request-units.md)

