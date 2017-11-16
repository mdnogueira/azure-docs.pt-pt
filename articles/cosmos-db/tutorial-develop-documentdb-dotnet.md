---
title: 'Azure Cosmos DB: Desenvolver com o DocumentDB API no .NET | Microsoft Docs'
description: "Saiba como desenvolver com a API do Azure Cosmos base de dados DocumentDB através do .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 106eaa1eb64dffd6c8362b13b4edb6452d536965
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmosdb-develop-with-the-documentdb-api-in-net"></a>Azure CosmosDB: Desenvolver com o DocumentDB API no .NET

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta de base de dados do Azure Cosmos no portal do Azure e, em seguida, criar uma base de dados de documento e a coleção com um [chave de partição](documentdb-partition-data.md#partition-keys) utilizando o [API .NET do DocumentDB](documentdb-introduction.md). Definir uma chave de partição quando cria uma coleção, a aplicação está preparada para dimensionamento effortlessly à medida que aumenta os dados. 

Este tutorial abrange as seguintes tarefas utilizando o [API .NET do DocumentDB](documentdb-sdk-dotnet.md):

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Criar uma base de dados e coleção com uma chave de partição
> * Criar documentos JSON
> * Atualizar um documento
> * Consultar as coleções particionadas
> * Execução de procedimentos armazenados
> * Eliminar um documento
> * Eliminar uma base de dados

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Vamos começar por criar uma conta de base de dados do Azure Cosmos no portal do Azure.

> [!TIP]
> * Já tem uma conta de base de dados do Azure Cosmos? Se assim for, avançar diretamente para [configurar a sua solução Visual Studio](#SetupVS)
> * Se estiver a utilizar o emulador de BD do Cosmos do Azure, siga os passos indicados em [emulador de BD do Azure Cosmos](local-emulator.md) para configurar o emulador e avançar diretamente para [configurar a sua solução do Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurar a sua solução Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
3. No **novo projeto** caixa de diálogo, selecione **modelos** / **Visual c#** / **aplicação de consola (.NET Framework)**, nomeie o projeto e, em seguida, clique em **OK**.
   ![Captura de ecrã da janela Novo Projeto](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. No **Explorador de Soluções**, clique com o botão direito do rato na sua nova aplicação de consola, que está sob a sua solução Visual Studio e, em seguida, clique em **Gerir Pacotes NuGet...**
    
    ![Captura de ecrã do Menu com Botão do Lado Direito para o Projeto](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. No **NuGet** separador, clique em **procurar**e escreva **documentdb** na caixa de pesquisa.
<!---stopped here--->
6. Nos resultados, localize **Microsoft.Azure.DocumentDB** e clique em **Instalar**.
   O ID de pacote para a biblioteca de clientes de base de dados do Azure Cosmos é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Captura de ecrã do NuGet Menu para encontrar o cliente SDK do Azure Cosmos DB](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Se obtiver uma mensagem sobre a revisão das alterações para a solução, clique em **OK**. Se obtiver uma mensagem sobre a aceitação de licença, clique em **Aceito**.

## <a id="Connect"></a>Adicione referências ao seu projeto
Os passos restantes neste tutorial fornecem os fragmentos de código da API do DocumentDB necessários para criar e atualizar a base de dados do Azure Cosmos recursos no seu projeto.

Primeiro, adicione estas referências à sua aplicação.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Ligar a aplicação

Em seguida, adicione estas duas constantes e a sua *cliente* variável na sua aplicação.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Em seguida, head novamente para o [portal do Azure](https://portal.azure.com) para obter o URL de ponto final e a chave primária. O URL de ponto final e a chave primária são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação.

No portal do Azure, navegue até à sua conta de base de dados do Azure Cosmos, clique em **chaves**e, em seguida, clique em **chaves de leitura e escrita**.

Copie o URI do portal e cole-o ao longo do `<your endpoint URL>` no ficheiro program.cs. Em seguida, copie a chave primária do portal e cole-o ao longo do `<your primary key>`. Não se esqueça de remover o `<` e `>` partir dos seus valores.

![Captura de ecrã do portal do Azure utilizado pelo NoSQL tutorial para criar uma aplicação de consola c#. Mostra uma conta de base de dados do Azure Cosmos, com as chaves realçadas no painel de conta de base de dados do Azure Cosmos e os valores URI e a chave primária realçados no painel de chaves](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Instanciar o DocumentClient

Agora, crie uma nova instância do **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Criar uma base de dados

Em seguida, crie uma base de dados do Azure Cosmos [base de dados](documentdb-resources.md#databases) utilizando o [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) método ou [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) método do **DocumentClient** classe do [SDK do .NET DocumentDB](documentdb-sdk-dotnet.md). Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Opte por utilizar uma chave de partição 

As coleções são contentores para armazenar documentos. São recursos lógicos e pode [abranger uma ou mais partições físicas](partition-data.md). A [chave de partição](documentdb-partition-data.md) é uma propriedade (ou caminho) na sua documentos que é utilizado para distribuir os dados entre os servidores ou partições. Todos os documentos com a mesma chave de partição são armazenados na mesma partição. 

A determinação de uma chave de partição é uma decisão importante antes de criar uma coleção. As chaves de partição são uma propriedade (ou caminho) na sua documentos que podem ser utilizados por base de dados do Azure Cosmos para distribuir os dados entre vários servidores ou partições. BD do cosmos codifica o valor da chave de partição e utiliza o resultado com hash para determinar a partição no qual pretende armazenar o documento. Todos os documentos com a mesma chave de partição são armazenados na mesma partição e chaves de partição não podem ser alteradas depois de uma coleção é criada. 

Para este tutorial, vamos definir a chave de partição para `/deviceId` para que a todos os dados para um único dispositivo é armazenado numa única partição. Pretende escolher uma chave de partição tem um grande número de valores, cada um dos quais são utilizados em sobre a mesma frequência para garantir a que BD do Cosmos pode o balanceamento de carga como os dados que vai crescendo e alcançarem o débito total da coleção. 

Para obter mais informações sobre a criação de partições, consulte [como partição e escala do BD Azure Cosmos?](partition-data.md) 

## <a id="CreateColl"></a>Criar uma coleção 

Agora que Sabemos que o nosso chave de partição, `/deviceId`, permite criar um [coleção](documentdb-resources.md#collections) utilizando o [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) método ou [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) método o **DocumentClient** classe. Uma coleção é um contentor de documentos JSON e qualquer lógica da aplicação associada JavaScript. 

> [!WARNING]
> Criar uma coleção tem implicações, de preços são reservar o débito da aplicação para comunicar com a base de dados do Azure Cosmos. Para obter mais detalhes, visite o nosso [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Este método permite uma API de REST a chamada para a base de dados do Azure Cosmos e aprovisiona o serviço um número de partições com base no débito pedido. Pode alterar o débito de uma coleção, o desempenho precisa de evoluir utilizando o SDK ou [portal do Azure](set-throughput.md).

## <a id="CreateDoc"></a>Criar documentos JSON
Vamos inserir alguns documentos JSON na base de dados do Azure Cosmos. Pode criar um [documento](documentdb-resources.md#documents) utilizando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Esta classe de exemplo contém um dispositivo ao ler e chamar CreateDocumentAsync para inserir um novo dispositivo de leitura para uma coleção.

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

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
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
## <a name="read-data"></a>Ler dados

Vamos ler o documento pela respetiva chave de partição e o Id utilizando o método ReadDocumentAsync. Tenha em atenção que as leituras incluem um valor de PartitionKey (correspondente para o `x-ms-documentdb-partitionkey` cabeçalho do pedido na REST API).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Atualizar dados

Agora vamos atualizar alguns dados utilizando o método ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Eliminar dados

Agora permite eliminar um documento pela chave de partição e o id utilizando o método DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Consultar as coleções particionadas

Quando consulta os dados em coleções particionadas, base de dados do Azure Cosmos encaminha automaticamente a consulta para as partições correspondente para os valores de chave de partição especificados no filtro (se existir alguma). Por exemplo, esta consulta é encaminhada para a partição que contém a chave de partição "XMS-0001 e".

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

## <a name="parallel-query-execution"></a>Execução paralela da consulta
Os SDKs do Azure Cosmos DB DocumentDB 1.9.0 e acima opções de execução paralela da consulta de suporte, que permitem-lhe executar consultas de latência baixa contra coleções particionadas, mesmo quando precisar de touch um grande número de partições. Por exemplo, a seguinte consulta está configurada para ser executado em paralelo em partições.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Pode gerir a execução paralela da consulta por otimização os seguintes parâmetros:

* Por definição `MaxDegreeOfParallelism`, pode controlar o grau de paralelismo ou seja, o número máximo de ligações de rede em simultâneo para partições da coleção. Se definir esta opção como -1, o grau de paralelismo é gerido pelo SDK. Se o `MaxDegreeOfParallelism` não está especificado ou foi definido para 0, o qual é o valor predefinido, existirá uma ligação de rede único para partições da coleção.
* Por definição `MaxBufferedItemCount`, pode comprometido a utilização de memória de latência e do lado do cliente de consulta. Se omitir este parâmetro ou defina esta opção como -1, o número de itens em memória intermédia durante a execução paralela da consulta é gerido pelo SDK.

Tendo em conta o mesmo Estado da coleção, uma consulta paralela irá devolver resultados pela mesma ordem como em execução série. Quando executar uma consulta de partição cruzada que inclui a ordenação (ORDER BY e/ou superior), o SDK DocumentDB emite a consulta em paralelo em partições e intercala parcialmente ordenados resultados no lado do cliente para produzir resultados globalmente ordenados.

## <a name="execute-stored-procedures"></a>Executar procedimentos armazenados
Por último, pode executar transações atómicas contra documentos com o mesmo ID de dispositivo, por exemplo, se estiver a manter os agregados ou o estado mais recente de um dispositivo de um único documento adicionando o seguinte código ao projeto.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

E já está! Trata-se os componentes principais de uma aplicação de base de dados do Azure Cosmos que utiliza uma chave de partição de forma eficiente dimensionamento distribuição de dados na partições.  

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, elimine todos os recursos criados por este tutorial no portal do Azure com os seguintes passos:

1. No menu da esquerda no portal do Azure, clique em **grupos de recursos** e, em seguida, clique no nome exclusivo do recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte: 

> [!div class="checklist"]
> * Criar uma conta de base de dados do Azure Cosmos
> * Criar uma base de dados e coleção com uma chave de partição
> * Criar documentos JSON
> * Atualizar um documento
> * Consultar as coleções particionadas
> * Foi executado um procedimento armazenado
> * Eliminou um documento
> * Eliminar uma base de dados

Agora pode avançar para o próximo tutorial e importar dados adicionais à sua conta de base de dados do Cosmos. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
