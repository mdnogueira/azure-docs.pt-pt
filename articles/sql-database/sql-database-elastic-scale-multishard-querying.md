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
ms.date: 04/12/2016
ms.author: torsteng
ms.openlocfilehash: b4827fafdfd2f8b094c4f541a7511d6233dd4e6f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="multi-shard-querying"></a>Consulta de várias partições horizontais
## <a name="overview"></a>Descrição geral
Com o [ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md), pode criar soluções de base de dados em partição horizontal. **Consulta de partições horizontais Multi** é utilizada para tarefas, tais como recolha/relatórios de dados que requerem a execução de uma consulta que é alongada entre várias partições horizontais. (Contraste esta opção para [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md), que efetua todo trabalho um ID de partição horizontal único.) 

1. Obter um [ **RangeShardMap** ](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [ **ListShardMap** ](https://msdn.microsoft.com/library/azure/dn807370.aspx) utilizando o [ **TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), a [ **TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx), ou o [ **GetShardMap** ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) método. Consulte [ **construir um ShardMapManager** ](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [ **obter um RangeShardMap ou ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Criar um  **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**  objeto.
3. Criar um  **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
4. Definir o  **[propriedade CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)**  para um comando T-SQL.
5. Execute o comando ao chamar o  **[método ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
6. Ver os resultados utilizando o  **[MultiShardDataReader classe](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exemplo
O código seguinte ilustra a utilização de várias partições horizontais consulta utilizando um determinado **ShardMap** denominado *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
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


Uma diferença chave é a construção de ligações de várias partições horizontais. Onde **SqlConnection** funciona numa base de dados único, o **MultiShardConnection** demora um ***coleção de partições horizontais*** como entrada. Povoar a colecção de partições horizontais de um mapa de partições horizontais. A consulta, em seguida, é executada na coleção de partições horizontais utilizando **UNION ALL** semântica para Monte um único resultado geral. Opcionalmente, o nome de partições horizontais em que a linha tem origem pode ser adicionado à utilização de saída a **ExecutionOptions** propriedade no comando. 

Tenha em atenção a chamada para **myShardMap.GetShards()**. Este método obtém todas as partições horizontais do mapa de partições horizontais e fornece uma forma fácil de executar uma consulta em todas as bases de dados relevantes. A coleção de partições horizontais para uma consulta de partições horizontais multi pode ser avançada mais efetuando uma consulta LINQ sobre a recolha devolvido da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parcial, a capacidade atual na consulta de partições horizontais multi tem foi concebida para funcionar bem para dezenas até centenas de partições horizontais.

Uma limitação com várias partições horizontais consulta está atualmente a falta de validação para shards e shardlets são consultadas. Enquanto o encaminhamento de dados dependentes verifica que um ID de partição horizontal especificado faz parte do mapa de partições horizontais no momento da consulta, consultas de partições horizontais multi não executar esta verificação. Isto pode levar a consultas de várias partições horizontais em execução em bases de dados que foram removidas do mapa de partições horizontais.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Operações de divisão de intercalação e consultas de várias partições horizontais
Consultas de partições horizontais multi não verificar se shardlets na base de dados consultado participam em operações de divisão de intercalação em curso. (Consulte [dimensionamento utilizando a ferramenta de intercalação de divisão de base de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md).) Isto pode levar a inconsistências em que as linhas da mesma shardlet mostram para várias bases de dados da mesma consulta de várias partições horizontais. Tenha em conta estas limitações e considere draining operações de divisão de intercalação em curso e as alterações para o mapa de partições horizontais ao efetuar consultas de várias partições horizontais.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Consultar também
**[SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**  classes e métodos.

Gerir shards utilizando o [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md). Inclui um espaço de nomes chamado [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) que fornece a capacidade de várias partições horizontais utilizando uma única consulta e o resultado de consulta. Fornece uma abstração de consulta através de uma coleção de partições horizontais. Também fornece as políticas de execução alternativo, resultados parciais em particular, para lidar com falhas ao consultar através de várias partições horizontais.  

