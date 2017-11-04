---
title: "Métricas de consulta SQL para a API do Azure Cosmos DB DocumentDB | Microsoft Docs"
description: "Saiba mais sobre como instrumentar e o desempenho de consulta SQL da base de dados do Azure Cosmos pedidos de depuração."
keywords: "sintaxe de SQL, consulta sql, as consultas sql, idioma de consulta json, conceitos de base de dados e as consultas de sql, as funções de agregação"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: b2fa8e8f-7291-45a3-9bd1-7284ed9077f8
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: arramac
ms.openlocfilehash: f057ee80e8a26595c17e6610a2aaaad08d0346b5
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Otimização de desempenho de consulta com base de dados do Azure Cosmos
BD do Cosmos do Azure fornece um [API do SQL Server para consultar os dados](documentdb-sql-query.md), sem necessidade de esquema ou índices secundários. Este artigo fornece as seguintes informações para programadores:

* Detalhes de alto nível sobre como funciona a execução de consulta SQL da BD do Cosmos do Azure
* Detalhes sobre as opções de SDK do cliente e cabeçalhos de pedido e resposta de consulta
* Melhores práticas para o desempenho das consultas e sugestões
* Exemplos de como utilizar as estatísticas de execução do SQL Server para depurar o desempenho das consultas

## <a name="about-sql-query-execution"></a>Sobre a execução da consulta SQL

Na base de dados do Azure Cosmos armazenar dados em contentores, que podem crescer para qualquer [débito de tamanho ou pedido de armazenamento](partition-data.md). BD do Azure do Cosmos dimensiona de forma totalmente integrada dados em partições físicas nos bastidores para processar o crescimento de dados ou aumentar o débito aprovisionado. Pode emitir consultas de SQL Server para qualquer contentor utilizando a API REST ou uma das suportado [SDKs do DocumentDB](documentdb-sdk-dotnet.md).

Uma breve descrição geral da criação de partições: definir uma chave de partição como "Cidade", que determina a forma como dados são divididos em partições físicas. Dados que pertencem a uma chave de partição única (por exemplo, "Cidade" = = "Seattle") são armazenadas dentro de uma partição física, mas normalmente uma única partição física tem várias chaves de partição. Quando uma partição atinge o tamanho de armazenamento, o serviço de forma totalmente integrada divide a partição em duas novas partições e divide a chave de partição uniformemente entre estas partições. Uma vez que as partições são transitórias, as APIs de utilizam uma abstração de um "chave intervalo de partição", que indica que os intervalos de hashes de chave de partição. 

Quando emite uma consulta à base de dados do Azure Cosmos, o SDK efetua estes passos lógicos:

* Analisar a consulta SQL para determinar o plano de execução da consulta. 
* Se a consulta inclui um filtro com a chave de partição, como `SELECT * FROM c WHERE c.city = "Seattle"`, é encaminhado para uma única partição. Se a consulta não tem um filtro na chave de partição, em seguida, que é executado em todas as partições e os resultados são intercalados do lado do cliente.
* A consulta é executada dentro de cada partição de série ou efetuada em paralelo, com base na configuração de cliente. Dentro de cada partição, a consulta poderá tornar-se um ou mais ida e volta, consoante a complexidade da consulta, configuraram o tamanho de página e aprovisionar débito da coleção. Cada execução devolve o número de [unidades de pedido](request-units.md) consumidas pela execução de consulta e, opcionalmente, estatísticas de execução da consulta. 
* O SDK efetua um resumo dos resultados da consulta em partições. Por exemplo, se a consulta envolve um ORDER BY em partições, em seguida, resultados de partições individuais são ordenados merge para devolver resultados globalmente ordenado por ordem. Se a consulta é uma agregação como `COUNT`, o número de partições individuais é somado para produzir o número global.

Os SDKs fornecem várias opções para a execução da consulta. Por exemplo, no .NET estas opções estão disponíveis no `FeedOptions` classe. A tabela seguinte descreve estas opções e forma afetam o tempo de execução de consulta. 

| Opção | Descrição |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Tem de ser definido como true para qualquer consulta que necessita para ser executada em mais de uma partição. Este é um sinalizador explícito para lhe permitir introduzir fala conscientes desempenho durante o período de desenvolvimento. |
| `EnableScanInQuery` | Tem de ser definida como verdadeira se optou por fora de indexação, mas pretende executar a consulta através de uma verificação mesmo assim. Apenas aplicável se a indexação para o caminho de filtro pedida está desativada. | 
| `MaxItemCount` | O número máximo de itens a devolver por ida e volta ao servidor. Por definição de -1, pode permitir que o servidor de gerir o número de itens. Em alternativa, pode reduzir este valor para obter apenas um pequeno número de itens por ida e volta. 
| `MaxBufferedItemCount` | Esta é uma opção do lado do cliente e utilizado para limitar o consumo de memória quando efetuar a partição cruzada ORDER BY. Um valor mais alto ajuda a reduzir a latência de ordenação de partição cruzada. |
| `MaxDegreeOfParallelism` | Obtém ou define o número de operações simultâneas executar lado do cliente durante a execução paralela da consulta no serviço de base de dados do Azure DocumentDB. Um valor de propriedade positivo limita o número de operações simultâneas para o valor de conjunto. Se estiver definido como inferior a 0, o sistema decide automaticamente o número de operações simultâneas para ser executada. |
| `PopulateQueryMetrics` | Tempo de carregamento permite registo detalhado de estatísticas de tempo gasto em várias fases de execução da consulta como tempo de compilação, o tempo de ciclo de índice e o documento. Pode partilhar um resultado de estatísticas de consulta com o suporte do Azure para diagnosticar problemas de desempenho de consulta. |
| `RequestContinuation` | Pode retomar a execução da consulta mediante a transmissão no token de continuação opaco devolvido por qualquer consulta. O token de continuação encapsula todos os Estados necessários para a execução da consulta. |
| `ResponseContinuationTokenLimitInKb` | Pode limitar o tamanho máximo do token de continuação devolvido pelo servidor. Poderá ter de definir esta opção se o anfitrião de aplicações tem limites de tamanho de cabeçalho de resposta. Definir este pode aumentar a duração e o RUs consumidos para a consulta global.  |

Por exemplo, vamos uma consulta de exemplo na chave de partição pedida numa coleção com `/city` como partição de chave e aprovisionada com 100.000 RU/s de débito. Solicitar esta consulta utilizando `CreateDocumentQuery<T>` no .NET semelhante ao seguinte:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

O fragmento SDK mostrado acima, corresponde ao pedido de REST API seguinte:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Cada página de execução da consulta corresponde a uma API REST `POST` com o `Accept: application/query+json` cabeçalho e a consulta SQL no corpo. Cada consulta efetua uma ou mais arredondar viagens para o servidor com o `x-ms-continuation` token echoed entre o cliente e servidor ao retomar a execução. As opções de configuração FeedOptions são transmitidas para o servidor sob a forma de cabeçalhos de pedido. Por exemplo, `MaxItemCount` corresponde à `x-ms-max-item-count`. 

O pedido devolve a resposta (truncado para legibilidade) seguintes:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Os cabeçalhos de resposta da chave devolvidos da consulta incluem o seguinte:

| Opção | Descrição |
| ------ | ----------- |
| `x-ms-item-count` | O número de itens devolvido na resposta. Isto é dependente de fornecido `x-ms-max-item-count`, o número de itens que podem ser ajustar o tamanho do payload máximo da resposta, o débito aprovisionado e tempo de execução da consulta. |  
| `x-ms-continuation:` | O token de continuação para retomar a execução da consulta, se os resultados adicionais estão disponíveis. | 
| `x-ms-documentdb-query-metrics` | As estatísticas de consulta para a execução. Esta é uma cadeia delimitada que contém as estatísticas de tempo gasto nas fases de execução da consulta. Devolvido se `x-ms-documentdb-populatequerymetrics` está definido como `True`. | 
| `x-ms-request-charge` | O número de [unidades de pedido](request-units.md) consumidas pela consulta. | 

Para obter detalhes sobre as opções e cabeçalhos de pedido de REST API, consulte [consultar recursos utilizando a API de REST do DocumentDB](https://docs.microsoft.com/rest/api/documentdb/querying-documentdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Melhores práticas para o desempenho das consultas
Seguem-se os fatores mais comuns que afetam o desempenho de consulta de base de dados do Azure Cosmos. Iremos aprofundar cada um dos seguintes tópicos neste artigo.

| Fator | Sugestão | 
| ------ | -----| 
| Débito aprovisionado | Medir RU por consulta e certifique-se de que tem o débito aprovisionado necessário para as suas consultas. | 
| Divisão em partições e chaves de partição | Favor consultas com o valor de chave de partição na cláusula de filtro de latência baixa. |
| Opções de consulta e SDK | Siga as melhores práticas SDK como ligação direta e otimizar as opções de execução de consulta do lado do cliente. |
| Latência de rede | Rede sobrecarga na medida em conta e utilizar APIs multi homing para ler a partir da região mais próximo. |
| Política de indexação | Certifique-se de que tem a indexação caminhos/política necessária para a consulta. |
| Métricas de execução da consulta | Analise as métricas de execução de consulta para identificar potenciais reescritas das formas de consulta e os dados.  |

### <a name="provisioned-throughput"></a>Débito aprovisionado
BD do Cosmos, vai criar contentores de dados, cada um com débito reservado expressado no pedido unidades (RU) por-segundo. Uma leitura de um documento de 1 KB é 1 RU e cada operação (incluindo consultas) é normalizados para um número fixo de RUs com base no respetiva complexidade. Por exemplo, se tiver 1000 RU/s aprovisionada para o contentor e tiver uma consulta como `SELECT * FROM c WHERE c.city = 'Seattle'` que consome 5 RUs, em seguida, pode realizar (1000 RU/s) / (5 RU/consulta) = 200 consulta/s estas consultas por segundo. 

Se submeter consultas mais de 200/seg, o serviço for iniciado a limitação de taxa de pedidos recebidos acima 200/s. Os SDKs processam automaticamente neste caso, efetuando uma repetição/término, por isso, poderá notar uma latência superior para estas consultas. Aumentar o débito aprovisionado para o valor necessário melhora a latência de consulta e débito. 

Para obter mais informações sobre unidades de pedido, consulte o artigo [unidades de pedido](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Divisão em partições e chaves de partição
Com a base de dados do Azure Cosmos, normalmente consultas efetuar pela seguinte ordem de mais rápido/mais eficiente para mais lento/menos eficiente. 

* OBTER uma chave de partição única e a chave de item
* Consultar com uma cláusula de filtro uma chave de partição única
* Consultar sem uma cláusula de filtro de igualdade ou um intervalo de uma propriedade
* Sem filtros de consulta

As consultas que precisam de Consulte todas as partições têm de latência superior e podem consumir RUs superiores. Uma vez que cada partição tem de indexação automática contra todas as propriedades, a consulta pode ser servida eficiente do índice neste caso. Pode efetuar consultas que abrangem partições mais rápida, utilizando as opções de paralelismo.

Para saber mais sobre a criação de partições e chaves de partição, consulte o artigo [criação de partições do BD Azure Cosmos](partition-data.md).

### <a name="sdk-and-query-options"></a>Opções de consulta e SDK
Consulte [sugestões de desempenho](performance-tips.md) e [teste de desempenho](performance-testing.md) para saber como obter o melhor desempenho do lado do cliente da base de dados do Azure Cosmos. Isto inclui utilizar os SDKs mais recentes, configurações de plataforma específica, como o número predefinido de ligações, frequência de recolha de lixo, a configurar e utilizar opções de conectividade simples como direta/TCP. 


#### <a name="max-item-count"></a>Número de itens de máx.
Para consultas, o valor de `MaxItemCount` pode ter um impacto significativo na hora de consulta de ponto a ponto. Cada ida e volta para o servidor irá devolver não mais do que o número de itens na `MaxItemCount` (predefinição de 100 itens). Definir este como um valor mais alto (a -1 é máximo e recomendada) irá melhorar a sua duração de consulta global ao limitar o número de ida e volta entre o servidor e cliente, especialmente para consultas com conjuntos de resultados grande.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Máx. grau de paralelismo
Para consultas, otimizar o `MaxDegreeOfParallelism` para identificar as configurações melhor para a sua aplicação, especialmente se executar consultas de partição cruzada (sem um filtro com o valor de chave de partição). `MaxDegreeOfParallelism`controla o número máximo de tarefas paralelas, ou seja, o número máximo de partições visitas em paralelo. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Vamos assumir que
* D = número predefinido máximo de tarefas paralelas (= número total de processador no computador cliente)
* P = o utilizador especificado o número máximo de tarefas paralelas
* N = número de partições que tem de ser visitado para responder a uma consulta

Seguem-se implicações da forma como as consultas paralelas comportar-se para valores diferentes de P.
* (P = = 0) = > modo de série
* (P = = 1) = > máximo de uma tarefa
* (P > 1) = > tarefas paralelas mín. (P, N) 
* (P < 1) = > tarefas paralelas mín. (N, D)

Notas de versão do SDK e detalhes sobre classes implementadas e métodos, consulte [SDKs do DocumentDB](documentdb-sdk-dotnet.md)

### <a name="network-latency"></a>Latência de rede
Consulte [distribuição global da base de dados do Azure Cosmos](tutorial-global-distribution-documentdb.md) como configurar a distribuição global e estabelecer ligação com a região mais próxima. Latência de rede tem um impacto significativo no desempenho das consultas quando precisar de efetuar várias ida e volta ou obter um grande conjunto de resultados da consulta. 

A secção sobre métricas de execução da consulta explica como obter o tempo de execução de consultas de servidor ( `totalExecutionTimeInMs`), para que pode diferenciar entre o tempo despendido na execução de consulta e o tempo despendido no tráfego de rede.

### <a name="indexing-policy"></a>Política de indexação
Consulte [configurar política de indexação](indexing-policies.md) para indexação caminhos, tipos e modos e forma afetam a execução da consulta. Por predefinição, a política de indexação utiliza indexação de Hash de cadeias, que é eficaz para consultas de igualdade, mas não para consultas de intervalo/ordem por consultas. Se precisar de consultas de intervalo de cadeias, é recomendável especificar o tipo de índice de intervalo para todas as cadeias. 

## <a name="query-execution-metrics"></a>Métricas de execução da consulta
Pode obter métricas detalhadas na execução da consulta mediante a transmissão de opcional no `x-ms-documentdb-populatequerymetrics` cabeçalho (`FeedOptions.PopulateQueryMetrics` no SDK do .NET). O valor devolvido em `x-ms-documentdb-query-metrics` tem os seguintes pares chave-valor destina a resolução de problemas de execução de consulta avançada. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Métrica | Unidade | Descrição | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milissegundos | Tempo de execução da consulta | 
| `queryCompileTimeInMs` | milissegundos | Tempo de compilação da consulta  | 
| `queryLogicalPlanBuildTimeInMs` | milissegundos | Tempo até ao criar o plano de consulta lógica | 
| `queryPhysicalPlanBuildTimeInMs` | milissegundos | Tempo até ao criar o plano de consulta físico | 
| `queryOptimizationTimeInMs` | milissegundos | Tempo gasto na consulta de otimização | 
| `VMExecutionTimeInMs` | milissegundos | Tempo despendido no tempo de execução da consulta | 
| `indexLookupTimeInMs` | milissegundos | Tempo gasto na camada física índice | 
| `documentLoadTimeInMs` | milissegundos | Tempo despendido no carregamento de documentos  | 
| `systemFunctionExecuteTimeInMs` | milissegundos | Total de tempo gasto em execução (incorporada) das funções de sistemas em milissegundos  | 
| `userFunctionExecuteTimeInMs` | milissegundos | Total de tempo gasto a executar as funções definidas pelo utilizador em milissegundos | 
| `retrievedDocumentCount` | Contagem | Número total de documentos obtidos  | 
| `retrievedDocumentSize` | Bytes | Tamanho total dos documentos obtidos em bytes  | 
| `outputDocumentCount` | Contagem | Número de documentos de saída | 
| `writeOutputTimeInMs` | milissegundos | Tempo de execução de consulta em milissegundos | 
| `indexUtilizationRatio` | rácio (< = 1) | Carregar o rácio do número de documentos correspondidos pelo filtro para o número de documentos  | 

Os SDKs do cliente internamente poderão fazer várias operações de consulta para servir a consulta dentro de cada partição. O cliente efetua a mais do que uma chamada por partição se os resultados totais excedem `x-ms-max-item-count`, se a consulta excede o débito aprovisionado para a partição, se o payload de consulta atinge o tamanho máximo por página, ou se a consulta atingir o sistema atribuído tempo limite. Cada execução de consultas parcial devolve um `x-ms-documentdb-query-metrics` para essa página. 

Seguem-se alguns exemplos de consultas e devolvido como interpretar algumas das métricas da execução de consulta: 

| Consulta | Métrica de exemplo | Descrição | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | O número de documentos obtido é 100 + 1 para corresponder a cláusula TOP. Hora de consulta é essencialmente despendida no `WriteOutputTime` e `DocumentLoadTime` porque se trata de uma análise. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount está agora superior (500 + 1 para corresponder a cláusula TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Prestes 0.9 ms é despendido no IndexLookupTime para uma pesquisa de chave, porque é uma pesquisa do índice `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Tempo ligeiramente mais (1.7 ms) despendido na IndexLookupTime através de uma análise de intervalo, porque é uma pesquisa do índice `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Mesmo tempo gasto em `DocumentLoadTime` como consultas anteriores, mas inferior `WriteOutputTime` porque iremos estiver a projetar apenas uma propriedade. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Prestes 213 ms é despendido no `UserDefinedFunctionExecutionTime` executando UDF em cada valor de `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Cerca de 0,6 ms é despendido no `IndexLookupTime` no `/Name/?`. A maioria de tempo de execução de consulta (~ 7 ms) em `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Consulta é executada como uma análise porque utiliza `LOWER`, e 500 fora 2491 documentos obtidos são devolvidos. |


## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os operadores de consulta SQL e palavras-chave suportados, consulte [consulta SQL](documentdb-sql-query.md). 
* Para mais informações sobre unidades de pedido, consulte o artigo [unidades de pedido](request-units.md).
* Para saber mais sobre a política de indexação, consulte [política de indexação](indexing-policies.md) 


