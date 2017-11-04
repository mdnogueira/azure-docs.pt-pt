---
title: "Melhorar o desempenho do índice columnstore - Azure SQL Data Warehouse | Microsoft Docs"
description: "Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que comprime um índice columnstore em cada rowgroup."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: barbkess
ms.openlocfilehash: 0476afb875616ed0758835aa52fb2334b63959a9
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximizar a forma de qualidade de rowgroup para columnstore

Qualidade de Rowgroup é determinada pelo número de linhas num rowgroup. Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que comprime um índice columnstore em cada rowgroup.  Utilize estes métodos para melhorar as taxas de compressão e consultar o desempenho para índices columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Por que motivo o tamanho de rowgroup é importante
Uma vez que um índice columnstore analisa uma tabela através da análise de segmentos de coluna de rowgroups individuais, maximizar a forma do número de linhas em cada rowgroup melhora o desempenho de consulta. Quando rowgroups tem um elevado número de linhas, compressão de dados melhora o que significa que há menos dados para ler a partir do disco.

Para mais informações sobre rowgroups, consulte [guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamanho de destino para rowgroups
Para melhor desempenho das consultas, o objetivo é para maximizar o número de linhas por rowgroup num índice columnstore. Um rowgroup pode ter um máximo de 1.048.576 linhas. É pode não ter o número máximo de linhas por rowgroup. Os índices Columnstore alcançarem o bom desempenho quando rowgroups tem, pelo menos, 100 000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Pode obter cortada Rowgroups durante a compressão

Durante uma em massa de carga ou columnstore índice reconstrução, por vezes, não existe não existe memória suficiente disponível para compressão todas as linhas designadas para cada rowgroup. Quando existe pressão de memória, os índices columnstore compactar os tamanhos de rowgroup para que compressão para o columnstore pode ter êxito. 

Quando não existe memória suficiente para comprimir a, pelo menos, 10 000 linhas para cada rowgroup, o SQL Data Warehouse gera um erro.

Para obter mais informações sobre o carregamento em massa, consulte [carregamento em massa para um índice columnstore em cluster](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorizar rowgroup qualidade

Não há DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats) que expõe informações úteis, tais como o número de linhas na rowgroups e o motivo para corte se foi corte não existe. Pode criar a vista seguinte como uma forma útil para consultar este DMV para obter informações sobre rowgroup corte.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

O trim_reason_desc indica se o rowgroup foi cortada (trim_reason_desc = NO_TRIM implica não ocorreu nenhuma corte e grupo de linhas de qualidade ideal). Pelas seguintes razões compactação indicam prematuro corte do rowgroup:
- BULKLOAD: Esta compactação motivo é utilizado quando o lote de entrada de linhas para a carga tinha inferior a 1 milhões de linhas. O motor irá criar grupos de linhas comprimido se existirem superior a 100.000 linhas que está a ser inseridas (por oposição a inserir no arquivo de delta) mas define o motivo de corte como BULKLOAD. Neste cenário, é aconselhável aumentar a janela de carga de batch para acumule mais linhas. Além disso, reevaluate a esquema de partições para garantir que não está demasiado granular como grupos de linhas não podem abranger limites de partição.
- MEMORY_LIMITATION: Para criar grupos de linhas com 1 milhões de linhas, uma determinada quantidade de memória de trabalho é necessário pelo motor. Quando a sessão de carregamento de memória disponível é inferior a memória do trabalho necessário, grupos de linhas prematuramente obterem cortados. As secções seguintes explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: Esta compactação razão indica que corte rowgroup ocorreu porque ocorreu pelo menos uma coluna de cadeia com cadeias de cardinalidade abrangente e/ou elevado. O tamanho de dicionário está limitado a 16 MB na memória e é comprimido assim que este limite for atingido o grupo de linhas. Caso se depare com esta situação, considere isolar problemática coluna numa tabela separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

O máximo de memória necessário para comprimir um rowgroup é aproximadamente

- 72 MB +
- \#linhas \* \#colunas \* 8 bytes +
- \#linhas \* \#colunas de cadeia curta \* 32 bytes +
- \#colunas de cadeia longa \* 16 MB de dicionário de compressão

em colunas de cadeia curta utilizam tipos de dados de cadeia de < = 32 bytes e tipos de dados de cadeia de utilização de colunas de cadeia longa de > 32 bytes.

Cadeias de longas são comprimidas com um método de compressão concebido para a compressão de texto. Este método de compressão utiliza um *dicionário* para armazenar os padrões de texto. O tamanho máximo de um dicionário é 16 MB. Não há apenas um dicionário para cada coluna de cadeia longo no rowgroup.

Para um debate aprofundado sobre os requisitos de memória columnstore, veja o vídeo [Azure SQL Data Warehouse dimensionamento: configuração e as orientações](https://myignite.microsoft.com/videos/14822).

## <a name="ways-to-reduce-memory-requirements"></a>Formas de reduzir os requisitos de memória

Utilize as seguintes técnicas para reduzir os requisitos de memória para a compressão rowgroups em índices columnstore.

### <a name="use-fewer-columns"></a>Utilizar menos colunas
Se for possível, estruture tabela com menos colunas. Quando um rowgroup é comprimido para o columnstore, o índice columnstore comprime cada segmento de coluna em separado. Por conseguinte, os requisitos de memória para comprimir um rowgroup aumentam como o número de aumentos de colunas.


### <a name="use-fewer-string-columns"></a>Utilizar menos colunas de cadeia
Colunas de tipos de dados de cadeia requerem mais memória do que um valor numérico e tipos de dados de data. Para reduzir os requisitos de memória, considere remover colunas de cadeia de tabelas de factos e colocando-as nas tabelas de dimensão mais pequena.

Requisitos de memória adicional para compressão de cadeia:

- Tipos de dados de cadeia até 32 carateres podem exigir a 32 bytes adicionais por valor.
- Tipos de dados de cadeia com mais do que 32 carateres são comprimidos através de métodos de dicionário.  Cada coluna no rowgroup pode necessitar até um 16 MB adicional para criar o dicionário.

### <a name="avoid-over-partitioning"></a>Evitar a criação de partições excessiva

Os índices Columnstore criar um ou mais rowgroups por partição. No SQL Data Warehouse, o número de partições cresce rapidamente, uma vez que são distribuídos os dados e cada distribuição está particionada. Se a tabela tem partições demasiados, poderão não existir suficiente linhas para preencher o rowgroups. A falta de linhas não cria pressão de memória durante a compressão, mas leva a rowgroups não alcance o melhor desempenho das consultas columnstore.

Outra razão para evitar a criação de partições excessiva está existir uma memória sobrecarga de carregamento linhas para um índice columnstore numa tabela particionada. Durante uma carga várias partições foi receber as linhas de entrada, o que são guardadas na memória até cada partição tem suficiente linhas ser comprimido. Ter partições demasiados cria pressão de memória adicional.

### <a name="simplify-the-load-query"></a>Simplifique a consulta de carga

A base de dados partilha a concessão de memória para uma consulta entre todos os operadores da consulta. Quando uma consulta de carga tem tipos complexos e associações, a memória disponível para compressão é reduzida.

Estruture a consulta de carga para se focarem apenas em carregar a consulta. Se precisar de executar transformações nos dados, executá-los separada da consulta de carga. Por exemplo, testar os dados numa tabela Área dinâmica para dados, execute as transformações e, em seguida, carregue a tabela de testes para o índice columnstore. Pode também primeiro a carregar os dados e, em seguida, utilizar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar MAXDOP

Cada distribuição comprime rowgroups no columnstore em paralelo quando existe mais do que um núcleo de CPU disponíveis por distribuição. O paralelismo necessita de recursos de memória adicional, o que podem levar a pressão de memória e rowgroup corte.

Para reduzir a pressão de memória, pode utilizar a sugestão de consulta MAXDOP para forçar a operação de carregamento para ser executada no modo de série dentro de cada distribuição.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Formas de alocar mais memória

Tamanho DWU e a classe de recursos de utilizador em conjunto determinam a quantidade de memória está disponível para uma consulta do utilizador. Para aumentar a concessão de memória para uma consulta de carga, pode aumentar o número de DWUs ou aumentar a classe de recursos.

- Para aumentar as DWUs, consulte [como aumentar o desempenho?](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- Para alterar a classe de recursos para uma consulta, consulte [alterar um exemplo de classe de recursos de utilizador](sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

Por exemplo, no DWU 100 um utilizador na classe de recurso smallrc pode utilizar a 100 MB de memória para cada distribuição. Para obter mais detalhes, consulte [simultaneidade no SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md).

Suponha que determine que tem de 700 MB de memória para obter os tamanhos de rowgroup de alta qualidade. Estes exemplos mostram como pode executar a consulta de carga com memória suficiente.

- DWU 1000 e mediumrc, a concessão de memória é utilizar 800 MB
- A utilização de DWU 600 e largerc, a concessão de memória é 800 MB.


## <a name="next-steps"></a>Passos seguintes

Para ver mais formas de melhorar o desempenho do armazém de dados do SQL Server, consulte o [descrição geral do desempenho](sql-data-warehouse-overview-manage-user-queries.md).

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->
