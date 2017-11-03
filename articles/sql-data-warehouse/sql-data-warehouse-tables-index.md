---
title: "A indexação de tabelas no armazém de dados SQL | Microsoft Azure"
description: "Introdução à tabela de indexação no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: 3e617674-7b62-43ab-9ca2-3f40c41d5a88
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2016
ms.author: shigu;barbkess
ms.openlocfilehash: b205ed47833f675286539705e2754d2ea3821b8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>A indexação de tabelas no armazém de dados do SQL Server
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Tipos de dados][Data Types]
> * [Distribuir][Distribute]
> * [Índice][Index]
> * [Partição][Partition]
> * [Estatísticas][Statistics]
> * [Temporário][Temporary]
> 
> 

O SQL Data Warehouse oferece várias opções de indexação, incluindo [em cluster os índices columnstore][clustered columnstore indexes], [agrupado índices e índices não em cluster] [ clustered indexes and nonclustered indexes].  Além disso, também oferece uma opção de índice não existem também conhecido como [área dinâmica para dados][heap].  Este artigo aborda as vantagens de cada tipo de índice, bem como sugestões para obter o desempenho maioria dos fora do seu índices. Consulte [criar a sintaxe de tabela] [ create table syntax] para obter mais detalhes sobre como criar uma tabela no SQL Data Warehouse.

## <a name="clustered-columnstore-indexes"></a>Índices columnstore em cluster
Por predefinição, o SQL Data Warehouse cria um índice columnstore em cluster quando não existem opções de índice são especificadas numa tabela. Tabelas columnstore em cluster oferecem tanto o nível mais elevado de compressão de dados, bem como o melhor desempenho de consulta global.  Tabelas columnstore em cluster irão geralmente superam o desempenho dos tabelas de índice ou a área dinâmica para dados em cluster e, normalmente, a melhor opção para tabelas grandes.  Por esta razão, columnstore em cluster é o melhor local para iniciar quando não souber como a tabela de índice.  

Para criar uma tabela de columnstore em cluster, basta especificar o índice COLUMNSTORE em cluster na cláusula WITH ou deixe a cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existem alguns cenários onde columnstore em cluster não pode ser uma boa opção:

* Tabelas Columnstore não suportam varchar, nvarchar (Max) e varbinary (Max).  Considere área dinâmica para dados ou o índice em cluster.
* Tabelas Columnstore poderão ser menos eficientes para dados transitórios.  Considere área dinâmica para dados e tabelas temporárias talvez até.
* Tabelas pequenas com menos de 100 milhões de linhas.  Considere as tabelas de área dinâmica para dados.

## <a name="heap-tables"></a>Tabelas de área dinâmica para dados
Quando está temporariamente a colocar dados no SQL Data Warehouse, utilizar uma tabela de área dinâmica para dados irá tornar o processo geral mais rápido.  Isto acontece porque a pilhas são mais rápida do que a tabelas de índice e, em alguns casos, que pode ser feita a leitura subsequente da cache.  Se estiver a carregar dados apenas para testá-los antes de executar mais transformações, será muito mais rápido carregar a tabela para a tabela de área dinâmica para dados, do que carregar os dados para uma tabela columnstore em cluster. Além disso, carregar dados para um [tabela temporária] [ Temporary] também carregará muito mais rapidamente do que o carregamento de uma tabela no armazenamento permanente.  

Para as tabelas de pesquisa pequeno, menos de 100 milhões de linhas, muitas vezes, as tabelas de área dinâmica para dados fazem sentido.  Cluster columnstore tabelas começam a alcançar a compressão ideal assim que existir mais de 100 milhões de linhas.

Para criar uma tabela de área dinâmica para dados, basta especificar área dinâmica para dados na cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices em cluster e não em cluster
Os índices em cluster podem superam o desempenho dos tabelas columnstore em cluster quando uma linha única tem de ser recuperadas de forma rápida.  Para onde é necessário para desempenho com velocidade Alpine um único ou muito poucos pesquisa de linha de consultas, considere um índice de cluster ou o índice secundário não em cluster.  A desvantagem à utilização de um índice em cluster é que beneficia apenas consultas que utilizam um filtro altamente seletivo na coluna de índice em cluster.  Para melhorar o filtro de outras colunas de um índice não em cluster pode ser adicionado para outras colunas.  No entanto, cada índice que é adicionado a uma tabela adicionará espaço e o tempo de processamento para cargas.

Para criar uma tabela de índice em cluster, basta especificar índice em cluster na cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não em cluster numa tabela, basta utilize a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Otimização de índices columnstore em cluster
Tabelas columnstore em cluster são organizadas em dados em segmentos.  Boa qualidade de segmento elevada é fundamental para alcançar o desempenho de consultas em tabelas columnstore.  Qualidade de segmento pode ser medida pelo número de linhas de um grupo de linha comprimido.  Qualidade de segmento é ideal em que existem, pelo menos, 100K linhas por linha comprimida grupo e obterem no desempenho, como o número de linhas por abordagem de grupo de linha 1.048.576 linhas, que é as maioria das linhas que pode conter um grupo de linhas.

O abaixo vista pode ser criada e utilizada no seu sistema para as linhas média por linha agrupar e identificam os índices columnstore inferiores às ideais de cluster de computação.  A última coluna nesta vista irá gerar como instrução de SQL que pode ser utilizada para reconstruir os índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Agora que criou a vista, executar esta consulta para identificar a tabelas com grupos de linhas com menos de mil 100 linhas.  Obviamente, pode querer aumentar o limiar de K de 100, se estiver à procura de qualidade de segmento ideal mais. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Depois de ter de executar a consulta que pode começar a observar os dados e analisar os resultados. Esta tabela explica o que a procurar na sua análise de grupo de linha.

| Coluna | Como utilizar estes dados |
| --- | --- |
| [table_partition_count] |Se a tabela está particionada, que pode esperar ver a conta do grupo de linhas aberto superior. Cada partição na distribuição na teoria ter um grupo de linhas aberto associado. Fator esta na sua análise. Uma tabela pequena que tenha sido particionada foi otimizada, removendo a criação de partições completamente como isto iria melhorar a compressão. |
| [row_count_total] |Número total de linhas da tabela. Por exemplo, pode utilizar este valor para calcular a percentagem de linhas no estado comprimido. |
| [row_count_per_distribution_MAX] |Se a todas as linhas são distribuídas uniformemente este valor será o número de linhas por distribuição de destino. Compare este valor com o compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de linhas no formato columnstore para a tabela. |
| [COMPRESSED_rowgroup_rows_AVG] |Se o número médio de linhas significativamente menor do que o n. º máximo de linhas para um grupo de linhas, em seguida, considere a utilização CTAS ou ALTER INDEX REBUILD para recompress os dados |
| [COMPRESSED_rowgroup_count] |Número de grupos de linhas no formato columnstore. Se este número for bastante elevado em relação a tabela é um indicador de que a densidade columnstore é baixa. |
| [COMPRESSED_rowgroup_rows_DELETED] |Linhas logicamente são eliminadas no formato columnstore. Se o número for elevado relativamente ao tamanho da tabela, considere recriar a partição ou reconstrua o índice como esta ação remove-los fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Utilize esta opção em conjunto com as colunas médio e máximo para compreender o intervalo de valores para os grupos de linhas na sua columnstore. Um número superior ao limiar de carga (102,400 por partição alinhada distribuição) baixo sugere que otimizações estão disponíveis no carregamento de dados |
| [COMPRESSED_rowgroup_rows_MAX] |Tal como acima |
| [OPEN_rowgroup_count] |Grupos de linhas aberto são normais. Um razoavelmente seria de esperar um grupo de linhas aberto por distribuição de tabela (60). Números excessivos sugerimos entre as partições do carregamento de dados. Verifique novamente a estratégia de criação de partições para se certificar de é som |
| [OPEN_rowgroup_rows] |Cada grupo de linhas pode ter 1.048.576 linhas-lo como um máximo. Utilize este valor como completa os grupos de linhas aberto estão atualmente |
| [OPEN_rowgroup_rows_MIN] |Abrir grupos indicam que os dados são o trickle carregado na tabela ou que a carga anterior spilled dos restantes linhas para este grupo de linhas. Utilizar o mín, Máx, grupos de linhas de colunas de média para ver a quantidade de dados é DOM no OPEN. Para pequenas tabelas poderia ser 100% de todos os dados! Nesse caso ALTER INDEX REBUILD para forçar os dados para columnstore. |
| [OPEN_rowgroup_rows_MAX] |Tal como acima |
| [OPEN_rowgroup_rows_AVG] |Tal como acima |
| [CLOSED_rowgroup_rows] |Observe as linhas de grupo de linha fechado como uma verificação de sanity. |
| [CLOSED_rowgroup_count] |O número de grupos de linhas fechado deve ser baixa se qualquer são visualizadas de todo. Grupos de linhas fechado podem ser convertidos para roups rowg comprimido utilizando ALTER INDEX... REORGANISE comando. No entanto, isto não é normalmente necessário. Grupos fechados automaticamente são convertidos em grupos de linhas columnstore pelo processo de "cadeia de identificação Movimentador" em segundo plano. |
| [CLOSED_rowgroup_rows_MIN] |Grupos de linhas fechado devem ter uma taxa muito elevada de preenchimento. Se a taxa de preenchimento para um grupo de linhas fechado for baixa, são necessárias mais análises do columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Tal como acima |
| [CLOSED_rowgroup_rows_AVG] |Tal como acima |
| [Rebuild_Index_SQL] |SQL Server para reconstruir o índice columnstore em tabelas de |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de qualidade de índice columnstore fraco
Se identificou tabelas com qualidade de segmento fraco, é melhor identificar a causa raiz.  Seguem-se algumas outras causas comuns de segmento fraco quaility:

1. Pressão de memória quando o índice foi criado
2. Elevado volume de operações DML
3. Pequeno ou trickle operações de carga
4. Demasiados partições

Estes fatores podem causar um índice columnstore ter significativamente inferior ao ideal milhões de 1 linhas por grupo de linhas.  Também pode provocar a linhas Ir para o grupo de linhas de diferenças em vez de um grupo de linhas comprimido. 

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando o índice foi criado
O número de linhas por grupo de linhas comprimido está diretamente relacionadas com a largura da linha e a quantidade de memória disponível para processar o grupo de linhas.  Quando as linhas são escritas em tabelas columnstore sob pressão de memória, a qualidade de segmento de columnstore poderá sofrer consequências.  Por conseguinte, a melhor prática é atribuir a sessão que está a escrever para o acesso de tabelas de índice columnstore ao, tanta memória como possíveis.  Uma vez que existe um compromisso entre a memória e simultaneidade, a documentação de orientação na alocação de memória direita depende dos dados em cada linha da sua tabela, a quantidade de DWU alocou ao seu sistema e a quantidade de ranhuras de concorrência que deu à sessão que é escrever os dados da tabela.  Como melhor prática, recomendamos que comece com xlargerc se estiver a utilizar DW300 ou menos largerc se estiver a utilizar DW400 DW600 e mediumrc se estiver a utilizar DW1000 e acima.

### <a name="high-volume-of-dml-operations"></a>Elevado volume de operações DML
Um grande volume de operações DML que atualizar e eliminar linhas pode introduzir ineficácia no columnstore. Isto é particularmente verdadeiro quando a maioria das linhas de um grupo de linha são modificadas.

* Eliminar uma linha de um grupo de linhas comprimido apenas logicamente marca a linha como eliminado. A linha permanece no grupo de linha comprimido até que a partição ou tabela for reconstruída.
* A inserir uma linha adiciona a linha a uma tabela interna rowstore chamada um grupo de linhas de diferenças. A linha inserida não é convertida columnstore até que o grupo de linhas delta está cheio e é marcado como fechados. Grupos de linhas são fechados depois de atingirem a capacidade máxima de 1.048.576 linhas. 
* Atualizar uma linha no columnstore formato é processado como um eliminar lógica e, em seguida, uma insert. A linha inserida pode ser armazenada no arquivo de diferenças.

Em lotes atualização e operações excedem o limiar em massa de 102,400 linhas por partição alinhada distribuição será escrita diretamente para o formato de columnstore de inserção. No entanto, partindo do princípio de uma distribuição mesmo, teria de ser modificar mais do que 6.144 milhões de linhas numa única operação para esta situação ocorrer. Se o número de linhas de uma determinada partição alinhada distribuição é inferior a 102,400 as linhas que serão enviadas para o arquivo de diferenças e irão ficar lá tanto tempo até linhas suficientes foram inseridas ou alterou para fechar o grupo de linhas ou o índice foi reconstruído.

### <a name="small-or-trickle-load-operations"></a>Pequeno ou trickle operações de carga
Breve carrega que fluxo para o SQL Data Warehouse também por vezes, são conhecidos como trickle cargas. Estes normalmente representam uma transmissão em fluxo constante near dos dados que está a ser ingeridos pelo sistema. No entanto, dado que esta sequência está quase contínua o volume de linhas não é particularmente grande. Mais frequentemente do que não os dados significativamente estão abaixo do limiar necessário para uma carga direta para o formato de columnstore.

Nestas situações, muitas vezes, é melhor encaminhado para os dados pela primeira vez no blob storage do Azure e permitir que o mesmo acumular antes de carregar. Esta técnica, muitas vezes, é conhecida como *criação de batches de micro*.

### <a name="too-many-partitions"></a>Demasiados partições
Outro aspeto a ter em consideração é o impacto da criação de partições no seu tabelas columnstore em cluster.  Antes de criação de partições, o SQL Data Warehouse divide já os dados em 60 bases de dados.  Ainda mais a criação de partições divide os dados.  Se os dados de partição, em seguida, deverá considerar que **cada** partição tem de ter, pelo menos, 1 milhões de linhas para tirar partido de um índice columnstore em cluster.  Se a sua tabela de partição em 100 partições, em seguida, a tabela tem de ter, pelo menos, 6 mil milhões de linhas para tirar partido de um índice columnstore em cluster (60 distribuições * 100 partições * 1 milhões de linhas). Se a tabela de 100 partições não tem linhas de mil milhões de 6, reduza o número de partições ou considere a utilização de uma tabela de área dinâmica para dados em vez disso.

Depois das tabelas foram carregadas com alguns dados, siga os passos abaixo para identificar e reconstrua a tabelas com índices columnstore de cluster inferiores às ideais.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstruir índices para melhorar a qualidade de segmento
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passo 1: Identificar ou criar o utilizador que utiliza a classe de recursos à direita
É uma forma rápida de imediato melhorar a qualidade do segmento para reconstruir o índice.  O SQL devolvido pela vista acima irá devolver uma instrução ALTER INDEX REBUILD, que pode ser utilizada para reconstruir os índices.  Ao reconstruir os índices, lembre-se de que atribuir memória suficiente para a sessão que irá reconstruir o índice.  Para tal, aumente a classe de recursos de um utilizador que tem permissões para reconstruir o índice nesta tabela para o mínimo recomendado.  Não é possível alterar a classe de recursos do utilizador de proprietário da base de dados, por isso se não tiver criado um utilizador no sistema, terá de fazê-lo primeiro.  O mínimo que recomendamos é xlargerc se estiver a utilizar DW300 ou menos largerc se estiver a utilizar DW400 DW600 e mediumrc se estiver a utilizar DW1000 e acima.

Abaixo está um exemplo de como alocar mais memória para um utilizador através do aumento da respetiva classe de recursos.  Para obter mais informações sobre recursos classes e como criar um novo utilizador podem ser encontrados no [simultaneidade e carga de trabalho de gestão] [ Concurrency] artigo.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passo 2: Reconstruir índices columnstore em cluster com o utilizador de classe de recurso superior
Início de sessão como utilizador do passo 1 (por exemplo, LoadUser), que está agora a utilizar uma classe de recurso superior e executar as instruções ALTER INDEX.  Lembre-se de que este utilizador tem permissão ALTER para tabelas em que o índice está a ser reconstruído.  Estes exemplos mostram como reconstruir o índice columnstore completa ou como reconstruir uma única partição. Em tabelas grandes, é mais práticos para reconstruir índices uma única partição cada vez.

Em alternativa, em vez de reconstruir o índice, pode copiar a tabela para uma nova tabela utilizando [CTAS][CTAS].  Que forma é melhor? Para grandes volumes de dados, [CTAS] [ CTAS] é normalmente mais rápida do que [ALTER INDEX][ALTER INDEX]. Para mais pequenos volumes de dados, [ALTER INDEX] [ ALTER INDEX] é mais fáceis de utilizar e não requerem a trocar a tabela.  Consulte **reconstruir índices com CTAS e mudança de partições** abaixo para obter mais detalhes sobre como reconstruir índices com CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Reconstruir um índice no SQL Data Warehouse é uma operação offline.  Para obter mais informações sobre como reconstruir índices, consulte a secção ALTER INDEX REBUILD [desfragmentação de índices Columnstore] [ Columnstore Indexes Defragmentation] e o tópico de sintaxe [ALTER INDEX] [ ALTER INDEX].

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passo 3: Verificar a qualidade de segmento de columnstore em cluster foi melhorado
Execute novamente a consulta que tabela identificada com fraco segmentar qualidade e certifique-se de qualidade de segmento foi melhorado.  Se não melhorar a qualidade de segmento, é possível que as linhas na tabela são muito grande.  Considere a utilização de uma classe de recursos mais elevado ou DWU ao reconstruir os índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Reconstruir índices com CTAS e mudança de partições
Este exemplo utiliza [CTAS] [ CTAS] e mudança para reconstruir uma partição de tabela de partições. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para obter mais detalhes sobre voltar a criar partições utilizando `CTAS`, consulte o [partição] [ Partition] artigo.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os artigos no [descrição geral da tabela][Overview], [tipos de dados de tabela][Data Types], [distribuir uma tabela] [ Distribute], [Uma tabela de criação de partições][Partition], [manter as estatísticas da tabela] [ Statistics] e [Tabelas temporárias][Temporary].  Para saber mais sobre as melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[clustered indexes and nonclustered indexes]: https://msdn.microsoft.com/library/ms190457.aspx
[create table syntax]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[clustered columnstore indexes]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
