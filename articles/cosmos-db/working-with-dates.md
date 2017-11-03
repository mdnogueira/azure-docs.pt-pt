---
title: Trabalhar com as datas do BD Azure Cosmos | Microsoft Docs
description: Saiba mais sobre como trabalhar com as datas do BD Azure Cosmos.
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: arramac
ms.openlocfilehash: b6a77e33eea24000037ffb31d7aae3cb1d345ce9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhar com as datas no Azure Cosmos DB
BD do Cosmos do Azure oferece flexibilidade de esquema e indexação avançada através de um nativo [JSON](http://www.json.org) modelo de dados. Todos os recursos de BD do Cosmos do Azure, incluindo bases de dados, coleções, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como um requisito para a ser portátil, JSON (e a base de dados do Azure Cosmos) suportam apenas um pequeno conjunto de tipos básicos: cadeia, número, booleano, matriz, objeto e um valor nulo. No entanto, o JSON é flexível e permitir que os programadores e estruturas representar tipos mais complexos utilizando estes primitivos e de composição-los como objetos ou matrizes. 

Além dos tipos básicos, precisam de muitas aplicações de [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) tipo para representar as datas e carimbos. Este artigo descreve como os programadores podem armazenar, obter e consultar datas na base de dados do Cosmos do Azure utilizando o SDK .NET.

## <a name="storing-datetimes"></a>Armazenar DateTime
Por predefinição, o [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) serializa os valores DateTime como [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874) cadeias. A maioria das aplicações podem utilizar a representação de cadeia predefinido para DateTime pelos seguintes motivos:

* É possível comparar cadeias e a ordenação dos valores DateTime relativo é preservada quando estes são transformados para cadeias. 
* Esta abordagem não requer qualquer código personalizado ou atributos para a conversão de JSON.
* As datas, conforme armazenado no JSON são humanos legível.
* Esta abordagem pode tirar partido do índice da BD do Cosmos do Azure para um desempenho de consulta rápida.

Por exemplo, o seguinte fragmento armazena um `Order` objeto que contém duas propriedades de DateTime - `ShipDate` e `OrderDate` como um documento utilizando o SDK .NET:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Este documento é armazenado na base de dados do Azure Cosmos da seguinte forma:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Em alternativa, pode armazenar DateTime como carimbos de Unix, ou seja, como um número que representa o número de segundos decorridos desde 1 de Janeiro de 1970. Timestamp interno da BD Cosmos do Azure (`_ts`) propriedade segue esta abordagem. Pode utilizar o [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) classe DateTime como números, a serialização. 

## <a name="indexing-datetimes-for-range-queries"></a>A indexação de DateTime para consultas de intervalo
Consultas de intervalo são comuns com os valores DateTime. Por exemplo, se precisar de localizar todas as ordens criadas desde ontem, ou localizar todas as ordens fornecidas nos últimos cinco minutos, terá de executar consultas de intervalo. Para executar estas consultas de forma eficiente, tem de configurar a coleção para indexação de intervalo em cadeias.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Pode saber mais sobre como configurar as políticas de indexação a [políticas de indexação do Azure Cosmos DB](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Consultar a DateTime no LINQ
O SDK do .NET DocumentDB suporta automaticamente ao consultar os dados armazenados na base de dados do Azure Cosmos através de LINQ. Por exemplo, o fragmento seguinte mostra uma consulta LINQ que as ordens de filtros que foram enviadas nos últimos três dias.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Pode saber mais sobre a linguagem de consulta SQL da BD do Cosmos do Azure e o fornecedor LINQ [consultar base de dados do Cosmos](documentdb-sql-query.md).

Neste artigo, vamos analisar como armazenar, índice e consultar DATETIME do BD Azure Cosmos.

## <a name="next-steps"></a>Passos Seguintes
* Transfira e execute o [exemplos de código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consulta de API do DocumentDB](documentdb-sql-query.md)
* Saiba mais sobre [políticas de indexação do Azure Cosmos DB](indexing-policies.md)
