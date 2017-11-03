---
title: "Monitorização e a depuração com métricas do BD Azure Cosmos | Microsoft Docs"
description: "Utilize métricas do BD Azure Cosmos para depurar problemas comuns e monitorizar a base de dados."
keywords: metrics
services: cosmos-db
author: gnot
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: e6399831fe7c6cc727e92b13719df3b69e9981bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Monitorização e a depuração com métricas do BD Azure Cosmos

BD do Azure do Cosmos fornece métricas de débito, armazenamento, consistência, disponibilidade e latência. O [portal do Azure](https://portal.azure.com) fornece uma vista de agregados destas métricas; mais granulares com base nas métricas, o SDK de cliente e o [os registos de diagnóstico](./logging.md) estão disponíveis.

Para obter uma descrição geral das métricas novo e saiba localizar partições frequente na base de dados, veja o seguinte vídeo de sexta-feira do Azure:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Este artigo explica os casos de utilização comuns e como métricas de BD do Cosmos Azure podem ser utilizadas para analisar e estes problemas de depuração. As métricas são recolhidas a cada cinco minutos e são mantidas durante sete dias.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Noções sobre o número de pedidos está a ter êxito ou a causar erros

Para começar, dirija-se ao [portal do Azure](https://portal.azure.com) e navegue para o **métricas** painel. No painel, localizar o **excedido o número de pedidos de capacidade por 1 minuto** gráfico. Este gráfico mostra um minuto por minuto total de pedidos segmentados pelo código de estado. Para obter mais informações sobre códigos de estado HTTP, consulte [códigos de estado de HTTP para a base de dados do Azure Cosmos](https://docs.microsoft.com/rest/api/documentdb/http-status-codes-for-documentdb).

O código de estado de erro mais comuns é 429 (otimização), o que significa que os pedidos à base de dados do Azure Cosmos estão a exceder o débito aprovisionado. A solução mais comuns para isto é [aumentar verticalmente os RUs](./set-throughput.md) para a coleção especificada.

![Número de pedidos por minuto](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Determinar a distribuição de débito em partições

Ter uma boa cardinalidade das suas chaves de partição é essencial para qualquer aplicação escalável. Para determinar a distribuição de débito de qualquer coleção particionada dividida por partição, navegue para o **painel métricas** no [portal do Azure](https://portal.azure.com). No **débito** separador, é apresentada a repartição de armazenamento na **máximo consumido RU por segundo por cada partição física** gráfico. O gráfico seguinte ilustra um exemplo de uma distribuição fraco de dados como evidenced para a partição distorcida na extremidade esquerda. 

![Ver utilização intensa às 15:05 de partições únicas](media/use-metrics/metrics-17.png)

Pode provocar uma distribuição desigual débito *frequente* partições, que podem resultar em pedidos otimizados e podem necessitar de repartição. Para obter mais informações sobre a criação de partições do BD Azure Cosmos, consulte [partição e o dimensionamento do BD Azure Cosmos](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Determinar a distribuição de armazenamento em partições

Ter uma boa cardinalidade da sua partição é essencial para qualquer aplicação escalável. Para determinar a distribuição de débito de qualquer coleção particionada dividida por partição, aceda ao painel de métricas no [portal do Azure](https://portal.azure.com). No separador de débito, a repartição de armazenamento é apresentada no máximo consumido RU por segundo gráfico cada partição física. O gráfico seguinte ilustra uma distribuição fraca de dados como evidenced para a partição distorcida na extremidade esquerda. 

![Exemplo de distribuição de dados fraco](media/use-metrics/metrics-07.png)

Pode raiz causa que a chave de partição é skewing a distribuição clicando na partição no gráfico. 

![Chave de partição é skewing de distribuição](media/use-metrics/metrics-05.png)

Depois de identificar que a chave de partição está a causar o desfasamento de distribuição, poderá ter de reparticionar a coleção com uma chave de partição mais distribuída. Para obter mais informações sobre a criação de partições do BD Azure Cosmos, consulte [partição e o dimensionamento do BD Azure Cosmos](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Comparar o tamanho dos dados contra o tamanho de índice

Na base de dados do Azure Cosmos, o armazenamento consumido total é a combinação do tamanho de dados e o tamanho do índice. Normalmente, o tamanho do índice é fração do tamanho dos dados. No painel do métricas no [portal do Azure](https://portal.azure.com), no separador de armazenamento showcases a repartição de consumo de armazenamento com base nos dados e índice. Imagem (talvez) em alternativa, do SDK, pode encontrar a utilização de armazenamento atual através de uma coleção de leitura.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Se quiser poupar espaço de índice, pode ajustar o [política de indexação](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Depurar a razão pela qual as consultas em execução lenta

Os SDKs do DocumentDB API, base de dados do Azure Cosmos fornece estatísticas de execução da consulta. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* fornece detalhes sobre cada componente da consulta de quanto tempo demorou a execução. A causa mais comum para execução longa consultas são análises (a consulta não foi possível tirar partido dos índices), que podem ser resolvidos com uma condição de filtro melhor.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como monitorizar e problemas de depuração utilizando as métricas fornecidas no portal do Azure, pode pretender obter mais informações sobre como melhorar o desempenho da base de dados ao ler os artigos seguintes:

* [Desempenho e dimensionamento de teste com base de dados do Azure Cosmos](performance-testing.md)
* [Sugestões de desempenho para a base de dados do Azure Cosmos](performance-tips.md)
