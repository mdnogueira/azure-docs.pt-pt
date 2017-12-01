---
title: Consultar a bases de dados SQL do Azure | Microsoft Docs
description: "Execute consultas em shards utilizando a biblioteca de cliente da base de dados elásticas."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: torsteng
ms.openlocfilehash: 33128357bd5b2bd744c5c1c3032f658ebe865d49
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="multi-shard-querying"></a>Consulta de várias partições horizontais
## <a name="overview"></a>Descrição geral
Com o [ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md), pode criar soluções de base de dados em partição horizontal. **Consulta de partições horizontais Multi** é utilizada para tarefas, tais como recolha/relatórios de dados que requerem a execução de uma consulta que é alongada entre várias partições horizontais. (Contraste esta opção para [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md), que efetua todo trabalho um ID de partição horizontal único.) 

1. Obter um **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) ou **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) utilizando o **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), o **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), ou o **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) método. Consulte  **[construir um ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)**  e  **[obter um RangeShardMap ou ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Criar um **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) objeto.
3. Criar um **MultiShardStatement ou MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Definir o **propriedade CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) para um comando T-SQL.
5. Execute o comando ao chamar o **ExecuteQueryAsync ou ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) método.
6. Ver os resultados utilizando o **MultiShardResultSet ou MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) classe. 

## <a name="example"></a>Exemplo
O código seguinte ilustra a utilização de várias partições horizontais consulta utilizando um determinado **ShardMap** denominado *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

Uma diferença chave é a construção de ligações de várias partições horizontais. Onde **SqlConnection** funciona numa base de dados único, o **MultiShardConnection** demora um ***coleção de partições horizontais*** como entrada. Povoar a colecção de partições horizontais de um mapa de partições horizontais. A consulta, em seguida, é executada na coleção de partições horizontais utilizando **UNION ALL** semântica para Monte um único resultado geral. Opcionalmente, o nome de partições horizontais em que a linha tem origem pode ser adicionado à utilização de saída a **ExecutionOptions** propriedade no comando. 

Tenha em atenção a chamada para **myShardMap.GetShards()**. Este método obtém todas as partições horizontais do mapa de partições horizontais e fornece uma forma fácil de executar uma consulta em todas as bases de dados relevantes. A coleção de partições horizontais para uma consulta de partições horizontais multi pode ser avançada mais efetuando uma consulta LINQ sobre a recolha devolvido da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parcial, a capacidade atual na consulta de partições horizontais multi tem foi concebida para funcionar bem para dezenas até centenas de partições horizontais.

Uma limitação com várias partições horizontais consulta está atualmente a falta de validação para shards e shardlets são consultadas. Enquanto o encaminhamento de dados dependentes verifica que um ID de partição horizontal especificado faz parte do mapa de partições horizontais no momento da consulta, consultas de partições horizontais multi não executar esta verificação. Isto pode levar a consultas de várias partições horizontais em execução em bases de dados que foram removidas do mapa de partições horizontais.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Operações de divisão de intercalação e consultas de várias partições horizontais
Consultas de partições horizontais multi não verificar se shardlets na base de dados consultado participam em operações de divisão de intercalação em curso. (Consulte [dimensionamento utilizando a ferramenta de intercalação de divisão de base de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md).) Isto pode levar a inconsistências em que as linhas da mesma shardlet mostram para várias bases de dados da mesma consulta de várias partições horizontais. Tenha em conta estas limitações e considere draining operações de divisão de intercalação em curso e as alterações para o mapa de partições horizontais ao efetuar consultas de várias partições horizontais.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


