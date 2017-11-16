---
title: "A ligação do Apache Spark a Azure Cosmos DB | Microsoft Docs"
description: "Utilize este tutorial para saber mais sobre o conector do Azure Cosmos DB Spark que permite-lhe ligar Apache Spark BD do Cosmos do Azure para efetuar distribuídas sciences agregações e esses dados no multi-inquilino globalmente distribuídas sistema da base de dados da Microsoft que foi concebido para a nuvem."
keywords: o Apache spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: denlee
ms.openlocfilehash: ba824ed1bad49c71f8de9f2da8249945d9430222
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Acelerar a análise de macrodados em tempo real com o Spark para o conector Azure Cosmos DB

O Spark para o conector de BD do Cosmos Azure permite a BD do Azure Cosmos agir como uma origem de entrada ou o sink de saída para as tarefas do Apache Spark. Ligar [Spark](http://spark.apache.org/) para [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) acelera a sua capacidade para resolver problemas de ciência de dados a mover fast onde pode utilizar o Azure Cosmos DB para rápida persistirem e consultar dados. O Spark para o conector Azure Cosmos DB eficientemente utiliza os índices de base de dados do Cosmos Azure geridos nativos. Os índices ativar colunas atualizáveis quando efetuar análises de push pendente predicado filtragem fast alteração globalmente distribuídos dados, que o intervalo da Internet das coisas (IoT) para cenários de ciência e análise de dados.

Para trabalhar com o Spark GraphX e o gráfico de Gremlin BD do Cosmos APIs do Azure, consulte [efetuar análises de gráfico com o Spark e o Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Transferência

Para começar, transfira o Spark para o conector Azure Cosmos BD a partir do [cosmosdb-azure-spark](https://github.com/Azure/azure-cosmosdb-spark/) repositório no GitHub.

## <a name="connector-components"></a>Componentes de conector

O conector utiliza os seguintes componentes:

* [BD do Azure do Cosmos](http://documentdb.com) permite que os clientes aprovisionar e dimensionar débito e armazenamento em qualquer número de regiões geográficas. As ofertas de serviço:
   * Ativar a chave [distribuição global](distribute-data-globally.md) e dimensionamento horizontal
   * Garantida latências único dígito, o percentil 99th
   * [Vários modelos de consistência bem definidos](consistency-levels.md)
   * Garantida elevada disponibilidade com capacidades multi homing
   * Todas as funcionalidades são apoiadas por da indústria líder, abrangente [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLAs).

* [Apache Spark](http://spark.apache.org/) é um motor de processamento de open source para poderosas que está construído em torno de velocidade, facilidade de utilização e efetuar análises sofisticadas.

* [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) que pode implementar o Apache Spark na nuvem para implementações fundamentais utilizando [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Oficialmente as versões suportadas:

| Componente | Versão |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| SDK Java do DocumentDB do Azure | 1.10.0 |

Este artigo ajuda-o a executar alguns exemplos simples utilizando o Python (através de pyDocumentDB) e as interfaces de Scala.

Existem duas abordagens para ligar o Apache Spark e o Azure Cosmos DB:
- Utilize o pyDocumentDB através de [SDK Python do Azure DocumentDB](https://github.com/Azure/azure-documentdb-python).
- Criar um Spark baseada em Java para o conector de BD do Cosmos Azure utilizando o [SDK Java do Azure DocumentDB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>implementação de pyDocumentDB
Atual [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) permite-lhe ligar Spark BD do Cosmos do Azure, conforme mostrado no diagrama seguinte:

![Spark para o fluxo de dados de base de dados do Azure Cosmos através de pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Fluxo de dados da implementação pyDocumentDB

O fluxo de dados é o seguinte:

1. Liga o nó mestre do Spark para o nó de gateway do Azure Cosmos DB através de pyDocumentDB. Um utilizador especifica apenas as ligações de Spark e base de dados do Azure Cosmos. As ligações para os respetivos nós de gateway e o mestre estão transparentes para o utilizador.
2. O nó de gateway efetua a consulta de base de dados do Cosmos do Azure onde a consulta, subsequentemente, é executada relativamente a partições da coleção em nós de dados. A resposta para estas consultas é enviada para o nó de gateway e esse conjunto de resultados é devolvido para o nó mestre do Spark.
3. Subsequentes consultas (por exemplo, um DataFrame Spark) são enviadas para nós de trabalho de Spark para processamento.

Comunicação entre o Spark e base de dados do Azure Cosmos está limitada ao nó principal do Spark e nós de gateway do Azure Cosmos DB.  As consultas aceda tão rápido permite que a camada de transporte entre estes dois nós.

### <a name="install-pydocumentdb"></a>Instalar o pyDocumentDB
Pode instalar o pyDocumentDB no seu nó controladores utilizando **pip**, por exemplo:

```
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Ligar o Spark BD do Cosmos do Azure através do pyDocumentDB
Simplicidade o transporte de comunicações faz com que a execução de uma consulta do Spark BD do Cosmos do Azure utilizando o pyDocumentDB relativamente simple.

O fragmento de código seguinte mostra como utilizar o pyDocumentDB num contexto de Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Conforme indicado no fragmento de código:

* O SDK do Python Cosmos BD do Azure (`pyDocumentDB`) contém os todos os ligação necessárias parâmetros. Por exemplo, o parâmetro de localizações preferencial escolhe a ordem de réplica e a prioridade de leitura.
*  Importar as bibliotecas necessárias e configurar o **masterKey** e **anfitrião** para criar a base de dados do Azure Cosmos *cliente* (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Executar consultas do Spark através de pyDocumentDB
Os exemplos seguintes utilizam a instância de base de dados do Azure Cosmos que foi criada no fragmento anterior utilizando as chaves especificadas de só de leitura. O seguinte fragmento de código liga-se ao **airports.codes** coleção na conta DoctorWho como especificado anteriormente e executa uma consulta para extrair as cidades airport no estado de Washington.

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Depois da consulta foi executada através de **consulta**, o resultado é um **query_iterable. QueryIterable** que é convertido para uma lista de Python. Uma lista de Python pode ser facilmente convertida para um DataFrame Spark utilizando o seguinte código:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Porquê utilizar o pyDocumentDB ligar Spark BD do Cosmos do Azure?
Ligar o Spark BD do Cosmos do Azure utilizando o pyDocumentDB é normalmente para cenários em que:

* Pretende utilizar o Python.
* Está a devolver um relativamente pequeno conjunto de resultados do BD do Cosmos Azure spark. Tenha em atenção que o conjunto de dados subjacente do BD Azure Cosmos pode ser bastante grande. São aplicar os filtros, ou seja, executar predicados filtros, contra a origem de BD do Cosmos do Azure.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Spark para o conector Azure Cosmos DB

O Spark para o conector de BD do Cosmos Azure utiliza o [SDK Java do Azure DocumentDB](https://github.com/Azure/azure-documentdb-java) e move dados entre os nós de trabalho do Spark e o Azure Cosmos DB, conforme mostrado no diagrama seguinte:

![Fluxo de dados no Spark para o conector Azure Cosmos DB](./media/spark-connector/spark-connector.png)

O fluxo de dados é o seguinte:

1. Liga o nó mestre do Spark para o nó de gateway da BD do Cosmos do Azure para obter o mapa de partições. Um utilizador especifica apenas as ligações de Spark e base de dados do Azure Cosmos. As ligações para os respetivos nós de gateway e o mestre estão transparentes para o utilizador.
2. Esta informação é fornecida para o nó mestre do Spark.  Neste momento, deverá conseguir analisar a consulta para determinar as partições e as respetivas localizações na base de dados do Azure Cosmos que precisem de aceder.
3. Estas informações são transmitidas para nós de trabalho de Spark.
4. Ligar os nós de trabalho do Spark para as partições de base de dados do Azure Cosmos diretamente para extrair os dados e devolver os dados para as partições de Spark em nós de trabalho de Spark.

Comunicação entre o Spark e base de dados do Azure Cosmos é significativamente mais rápido porque o movimento de dados está entre os nós de trabalho do Spark e os nós de dados de base de dados do Azure Cosmos (partições).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Criar o Spark para o conector Azure Cosmos DB
Atualmente, o projeto de conector utiliza maven. Para criar o conector sem dependências, pode executar:
```
mvn clean package
```
Também pode transferir as versões mais recentes do JAR do *versões* pasta.

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Incluir o Spark de BD do Azure Cosmos JAR
Antes de executar qualquer código, terá de incluir o JAR do Spark de BD do Cosmos de Azure.  Se estiver a utilizar o **spark shell**, em seguida, pode incluir o JAR utilizando o **– v7** opção.  

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3-jar-with-dependencies.jar
```

Se pretender executar JAR sem dependências, utilize o seguinte código:

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3.jar,/$location/azure-documentdb-1.10.0.jar
```

Se estiver a utilizar um serviço de bloco de notas, como o serviço de bloco de notas do Jupyter do Azure HDInsight, pode utilizar o **spark magic** comandos:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.10.0.jar","wasb:///example/jars/azure-cosmosdb-spark-0.0.3.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

O **v7** comando permite-lhe incluir o dois v7 que é necessários para **cosmosdb-azure-spark** (próprio e o SDK de Java DocumentDB do Azure) e excluir **scala-refletir** para que não interfere com as chamadas de Livy (bloco de notas do Jupyter > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Ligar o Spark BD do Cosmos do Azure através do conector
Embora o transporte de comunicação seja um pouco mais complicado, executar uma consulta do Spark BD do Cosmos do Azure utilizando o conector é significativamente mais rápido.

O fragmento de código seguinte mostra como utilizar o conector num contexto de Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Conforme indicado no fragmento de código:

- **Azure-cosmosdb-spark** contém os todos os ligação necessários parâmetros, que inclui as localizações preferenciais. Por exemplo, pode escolher a ordem de réplica e a prioridade de leitura.
- Acabou de importar as bibliotecas necessárias e configurar o masterKey e o anfitrião para criar o cliente de base de dados do Azure Cosmos.

### <a name="execute-spark-queries-via-the-connector"></a>Executar consultas do Spark através do conector

O exemplo seguinte utiliza a instância de base de dados do Azure Cosmos que foi criada no fragmento anterior utilizando as chaves especificadas de só de leitura. O seguinte fragmento de código estabelece ligação à coleção DepartureDelays.flights_pcoll (na conta DoctorWho conforme especificado anteriormente) e executa uma consulta para extrair as informações de atraso de trânsito de flights são departing de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Porquê utilizar o Spark para implementação do conector de BD do Cosmos do Azure?

Ligar o Spark BD do Cosmos do Azure utilizando o conector é normalmente para cenários em que:

* Pretende utilizar Scala e atualizá-la para incluir um wrapper de Python, conforme indicado no [problema 3: wrapper adicionar Python e exemplos](https://github.com/Azure/azure-cosmosdb-spark/issues/3).
* Tiver uma grande quantidade de dados para transferir entre Apache Spark e base de dados do Azure Cosmos.

Para lhe fornecer uma ideia da diferença de desempenho de consulta, consulte o [execuções do teste de consulta wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Exemplo de agregação distribuída
Esta secção fornece alguns exemplos de como pode fazer agregações distribuídas e análise utilizando o Apache Spark e base de dados do Azure Cosmos em conjunto. BD do Azure do Cosmos já suporta agregações, de que é abordada no [agregados de escala Planet com blogue de base de dados do Azure Cosmos](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Eis como pode tirá-lo para o próximo nível com Apache Spark.

Tenha em atenção que estas agregações reference para o [Spark para o bloco de notas do conector de BD do Azure Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Ligar aos dados de exemplo flights
Estes exemplos de agregação acederem a alguns dados de desempenho de trânsito são armazenados no nosso **DoctorWho** base de dados do Azure Cosmos DB. Para ligar ao mesmo, terá de utilizar o seguinte fragmento de código:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Com este fragmento, também iremos executar uma consulta de base que transfere o conjunto filtrado de dados da base de dados do Azure Cosmos spark onde esta última opção pode efetuar as agregações distribuídas. Neste caso, pedimos para flights depart de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Os seguintes resultados foram gerados ao executar consultas do serviço de bloco de notas do Jupyter.  Tenha em atenção de que todos os fragmentos de código são genérico e não específicas para qualquer serviço.

### <a name="running-limit-and-count-queries"></a>LIMITE de execução e a CONTAGEM de consultas
Tal como que utilizou para, no SQL Server/Spark SQL, vamos começar com uma **limite** consulta:

![Consulta de limite de Spark](./media/spark-connector/spark-sql-query.png)

A consulta seguinte é uma simples e rápido **CONTAGEM** consulta:

![Consulta de CONTAGEM de Spark](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY consulta
Neste conjunto seguinte, pode facilmente executamos **GROUP BY** consultas em relação a nossa base de dados do Azure Cosmos DB:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![O Spark GROUP BY gráfico de consulta](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINTAS, consultas de ORDER BY
E aqui está uma **DISTINCT, ORDER BY** consulta:

![O Spark GROUP BY gráfico de consulta](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuar a análise de dados de eventos
Pode utilizar as seguintes consultas de exemplo para continuar a análise dos dados de eventos:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Parte superiores 5 atrasados destinos (cidades) departing de Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Gráfico de atrasos superior do Spark](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calcular atrasos mediano por cidades destino departing de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Gráfico de atrasos mediano do Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, transfira o Spark para o conector Azure Cosmos BD a partir do [cosmosdb-azure-spark](https://github.com/Azure/azure-cosmosdb-spark) repositório do GitHub e explorar os recursos adicionais no repositório:

* [Exemplos de agregações distribuída](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Scripts de exemplo e blocos de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Pode também querer rever o [Apache Spark SQL, DataFrames e conjuntos de dados guia](http://spark.apache.org/docs/latest/sql-programming-guide.html) e [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artigo.
