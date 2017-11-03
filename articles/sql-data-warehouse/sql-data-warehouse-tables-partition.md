---
title: "A criação de partições de tabelas no armazém de dados SQL | Microsoft Docs"
description: "Introdução à criação de partições de tabela no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: 3edfd34d368228be32afef48688739639a3b03ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>A criação de partições de tabelas no armazém de dados do SQL Server
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

Criação de partições é suportada em todos os tipos de tabela do SQL Data Warehouse; incluindo columnstore em cluster, índice em cluster e área dinâmica para dados.  Criação de partições também é suportada em todos os tipos de distribuição, incluindo hash ou o round robin distribuído.  Permite a criação de partições para dividir os dados em grupos mais pequenos de dados e na maioria dos casos, a criação de partições é feita por uma coluna de data.

## <a name="benefits-of-partitioning"></a>Vantagens da criação de partições
Criação de partições pode beneficiar desempenho de consulta e de manutenção de dados.  Indica se beneficia ambos ou apenas um é depende da forma como os dados são carregados e se a mesma coluna pode ser utilizada para ambos os fins, desde que a criação de partições só pode ser efetuada numa coluna.

### <a name="benefits-to-loads"></a>Vantagens para cargas
A vantagem principal da criação de partições no SQL Data Warehouse é melhorar a eficiência e o desempenho de carregamento de dados pela utilização da eliminação de partição, mudar e intercalação.  Na maioria dos casos dados é divididos em partições uma coluna de data rigorosamente está associada à sequência de que os dados são carregados para a base de dados.  Uma das vantagens da utilização de partições para manter os dados mais compridas-evitação de ciclos de registo de transações.  Embora simplesmente a inserir, atualizar ou eliminar dados possam ser a abordagem mais simples, com um pequeno profundamente e esforço, a criação de partições durante o processo de carga a utilizar pode substancialmente melhorar o desempenho.

Mudar de partição pode ser utilizado para rapidamente remover ou substituir uma secção de uma tabela.  Por exemplo, uma tabela de factos vendas poderá conter dados apenas para os últimos meses 36.  No final de cada mês, o mês de dados de vendas mais antigo é eliminado da tabela.  Foi possível eliminar estes dados utilizando uma instrução de eliminação para eliminar os dados para o mês mais antigo.  No entanto, a eliminação de uma grande quantidade de dados linha por linha com a instrução delete pode demorar muito tempo, bem como criar o risco de grande transações que pode demorar muito tempo a reversão, se houver algum problema.  Uma abordagem mais ideal é simplesmente remova a partição mais antiga dos dados.  Em que a eliminação de linhas individuais pode demorar horas, eliminar uma partição completa pode demorar algum segundos.

### <a name="benefits-to-queries"></a>Vantagens para consultas
Criação de partições também pode ser utilizada para melhorar o desempenho de consulta.  Se uma consulta aplica-se um filtro numa coluna particionada, isto pode limitar a análise para apenas as partições nenhuns que pode ser um subconjunto dos dados, evitando uma análise completa tabela muito mais pequeno.  Com a introdução de índices columnstore em cluster, os benefícios de desempenho de eliminação de predicado são menos úteis, mas em alguns casos pode haver uma vantagem de consultas.  Por exemplo, se a tabela de factos de vendas está particionada em meses 36 utilizando o campo de data de vendas, em seguida, consulta esse filtro a data de venda pode ignorar a procurar em partições não correspondem ao filtro.

## <a name="partition-sizing-guidance"></a>Orientação de dimensionamento de partição
Durante a criação de partições pode ser utilizada para melhorar o desempenho de alguns cenários, criar uma tabela com **demasiados** partições pacote podem prejudicar o desempenho em algumas circunstâncias.  Estas questões são particularmente verdadeiras para tabelas columnstore em cluster.  Para a criação de partições para ser útil, é importante compreender quando deve utilizar a criação de partições e o número de partições para criar.  Há nenhuma regra rápida rígida relativamente a partições quantas for demasiado elevado, depende de dados e o número de partições são carregar para em simultâneo.  Mas como geral regra geral, considere adicionar 10s para 100s de partições, não 1000s.

Ao criar a criação de partições no **columnstore em cluster** tabelas, é importante considerar o número de linhas será encaminhado para cada partição.  Para compressão ideal e o desempenho das tabelas columnstore em cluster, é necessário um mínimo de 1 milhões de linhas por partição e de distribuição.  Antes da criação de partições, o SQL Data Warehouse divide já cada tabela em 60 bases de dados distribuídas.  É qualquer criar partições adicionado a uma tabela para além das distribuições criadas em segundo plano.  Com este exemplo, se a tabela de factos vendas contidos 36 partições mensais e uma vez que o SQL Data Warehouse tem 60 distribuições, em seguida, as tabelas de factos vendas devem conter 60 milhões de linhas por mês ou 2.1 milhões de linhas quando todos os meses são povoados.  Se uma tabela contém significativamente menos linhas que o número mínimo recomendado de linhas por partição, considere utilizar menos partições para efetuar a aumentar o número de linhas por partição.  Consulte também o [Indexing] [ Index] artigo que inclui as consultas que podem ser executadas no armazém de dados SQL para avaliar a qualidade dos índices columnstore de cluster.

## <a name="syntax-difference-from-sql-server"></a>Diferença de sintaxe do SQL Server
O SQL Data Warehouse introduz uma definição simplificada de partições que é ligeiramente diferente do SQL Server.  As funções de criação de partições e de esquemas não são utilizados no armazém de dados do SQL dado que estão no SQL Server.  Em vez disso, tudo o que precisa de fazer é identificar coluna particionada e os pontos de limites.  Enquanto a sintaxe de criação de partições poderão ser ligeiramente diferente do SQL Server, os conceitos básicos são iguais.  SQL Server e do armazém de dados do SQL Server suportam uma partição coluna por tabela, o que pode ser ranged partição.  Para saber mais sobre a criação de partições, consulte o artigo [índices e tabelas Particionadas][Partitioned Tables and Indexes].

O exemplo de um SQL Data Warehouse particionada abaixo [CREATE TABLE] [ CREATE TABLE] partições de declaração, a tabela FactInternetSales na coluna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrar a criação de partições do SQL Server
Para migrar as definições de partição do SQL Server para o SQL Data Warehouse simplesmente:

* Eliminar o SQL Server [esquema de partição][partition scheme].
* Adicionar o [função de partição] [ partition function] definição para a tabela de criar.

Se estiver a migrar uma tabela particionada necessitar de uma instância do SQL Server de abaixo SQL podem ajudar a interrogate o número de linhas que estão em cada partição.  Tenha em atenção que, se for utilizada a mesma granularidade de criação de partições no SQL Data Warehouse, o número de linhas por partição irá diminuir por um fator de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestão de cargas de trabalhos
É uma consideração de peça final para pesar para a decisão de partição da tabela [gestão da carga de trabalho][workload management].  Gestão de carga de trabalho do armazém de dados do SQL Server é principalmente a gestão de memória e concorrência.  No SQL Data Warehouse o máximo de memória alocado a cada distribuição durante a execução de consulta é regida recursos classes.  Idealmente, as partições serão dimensionadas in consideration of outros fatores, como as necessidades de memória da criação de índices columnstore em cluster.  Colocar em cluster benefício de índices columnstore significativamente quando estiverem alocados mais memória.  Por conseguinte, irá querer Certifique-se de que uma reconstrução do índice de partição não é starved de memória. Aumentar a quantidade de memória disponível para a consulta pode ser alcançado ao mudar da função predefinida, smallrc, para uma das outras funções, tais como largerc.

Informações sobre a alocação de memória por distribuição estão disponíveis consultando as vistas de gestão dinâmica do Governador de recursos. Na realidade a concessão de memória será inferior figuras abaixo. No entanto, esta opção fornece um nível de orientação que pode utilizar ao dimensionar a sua partições para operações de gestão de dados.  Tente evitar as partições para além de concessão de memória fornecido pela classe de recursos muito grande de dimensionamento. Se as partições de crescimento para além desta figura corre o risco de pressão de memória que por sua vez origina menos compressão ideal.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Mudar de partição
Armazém de dados SQL suporta partição dividir, intercalar e mudança. Cada uma destas funções é excuted utilizando o [ALTER TABLE] [ ALTER TABLE] instrução.

Para mudar partições entre duas tabelas, deve garantir que as partições alinhar os respetivos limites e que correspondem às definições de tabela. Como não estão disponíveis para o intervalo de valores numa tabela de impor restrições check da tabela de origem tem de conter os limites de partição mesmo como tabela de destino. Se não for este o caso, a mudança de partições irá falhar, não serão sincronizados os metadados da partição.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém os dados
O método mais eficaz para dividir uma partição que contém os dados já está a utilizar um `CTAS` instrução. Se a tabela particionada for um columnstore em cluster, em seguida, a partição de tabela deve estar vazia antes que pode ser dividido.

Segue-se uma tabela particionada columnstore de exemplo que contém uma linha em cada partição:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Ao criar o objeto de estatística, podemos assegurar que os metadados da tabela são mais exato. Se omitir iremos criar estatísticas, o SQL Data Warehouse utilizará os valores predefinidos. Para obter detalhes sobre as estatísticas reveja [estatísticas][statistics].
> 
> 

Vamos, em seguida, pode consultar a contagem de linhas utilizando o `sys.partitions` vista de catálogo:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se vamos tentar dividir esta tabela, iremos irá obter um erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tarifas de mensagens 35346, 15 nível, 1 de estado, linha 44 DIVIDIR cláusula da instrução ALTER PARTITION falhou porque a partição não está vazia.  Apenas as partições vazia podem ser divididas quando existe um índice columnstore na tabela. Considere desativar o índice columnstore antes de emitir a instrução ALTER PARTITION, em seguida, reconstrua o índice columnstore depois de ALTER PARTITION ter sido concluída.

No entanto, podemos utilizar `CTAS` para criar uma nova tabela para conter os nossos dados.

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

Como os limites de partição estão alinhados é permitido um comutador. Isto irá deixar a tabela de origem com uma partição vazia que iremos subsequentemente pode dividir.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o falta fazer é para alinhar com os nossos dados para os novos limites de partição utilizando `CTAS` e comutador nossos dados para a tabela principal novo

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Depois de concluir o movimento de dados é uma boa ideia para atualizar as estatísticas na tabela de destino para se certificar de que refletem com precisão a distribuição dos dados na respetiva respetivas partições novo:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabela de criação de partições de controlo de código fonte
Para evitar a definição da tabela de **rusting** no seu sistema de controlo de origem, convém considerar a seguinte abordagem:

1. Criar a tabela como uma tabela particionada mas não existem valores de partição

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT`a tabela como parte do processo de implementação:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Com esta abordagem o código de controlo de origem permanece estático e os valores de limite de criação de partições têm permissão para ser dinâmico; com o armazém a evolução ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os artigos no [descrição geral da tabela][Overview], [tipos de dados de tabela][Data Types], [distribuir uma tabela] [ Distribute], [Uma tabela de indexação][Index], [manter as estatísticas da tabela] [ Statistics] e [Tabelas temporárias][Temporary].  Para obter mais informações sobre as melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
