---
title: "Otimizar as transações do SQL Data Warehouse | Microsoft Docs"
description: "Melhores práticas obter orientações sobre a escrita de atualizações de transação eficiente no Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6f326f26-8a54-49df-a482-9c96a58db371
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: f9f19d75a37351b3562ce8c2f3629df14c5437c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Otimizar as transações do SQL Data Warehouse
Este artigo explica como otimizar o desempenho do seu código transacional, para minimizar o risco para reverte longo.

## <a name="transactions-and-logging"></a>Registo de transações e
As transações são um componente importante de um motor de base de dados relacional. O SQL Data Warehouse utiliza transações durante a modificação de dados. Podem ser estas transações explícitas ou implícitas. Único `INSERT`, `UPDATE` e `DELETE` instruções são todos os exemplos de transações implícitas. Transações explícitas são explicitamente escritas por um programador utilizando `BEGIN TRAN`, `COMMIT TRAN` ou `ROLLBACK TRAN` e são normalmente utilizadas quando várias instruções de modificação têm de ser vinculada em conjunto numa única unidade atomic. 

O Azure SQL Data Warehouse consolida as alterações à base de dados utilizando os registos de transações. Cada distribuição tem o suas próprias registo de transações. Escritas de registo de transações são automáticas. Não há qualquer configuração necessária. No entanto, embora este processo garante que a operação de escrita introduz uma sobrecarga no sistema. Pode minimizar este impacto ao escrever o código de uma forma eficiente. Código de uma forma eficiente amplamente se insere nas categorias de dois.

* Tire partido de construções de registo mínimo sempre que possível
* Processar dados com um âmbito lotes para evitar transações de execução longa único
* Adotar uma padrão para grandes modificações para uma determinada partição da mudança de partições

## <a name="minimal-vs-full-logging"></a>Mínimo vs. registo completo
Ao contrário das operações totalmente iniciadas, que utilizam o registo de transações para controlar todas as alterações de linha, operações minimamente iniciadas manter controlar dos alocações de extensão e apenas alterações de dados de metadados. Por conseguinte, o registo mínimo envolve registo apenas as informações que é necessárias reverter a transação na eventualidade de ocorrer uma falha ou um pedido de explícito (`ROLLBACK TRAN`). Como muito menos informações são registadas no registo de transações, uma operação registada minimamente efetua melhor do que uma operação totalmente registada tamanho da mesma forma. Além disso, como as escritas menos passam o registo de transações, é gerada uma menor quantidade de dados de registo veio e e/s mais eficiente.

Os limites de segurança da transação só se aplicam às operações totalmente iniciadas.

> [!NOTE]
> Operações minimamente com sessão iniciadas podem participar em transações explícitas. Como todas as alterações em estruturas de alocação são controladas, é possível reverter operações minimamente com sessão iniciadas. É importante compreender que a alteração é "minimamente" registada não é uma com sessão iniciada.
> 
> 

## <a name="minimally-logged-operations"></a>Operações minimamente com sessão iniciadas
As seguintes operações são capazes de minimamente a ser registado:

* CRIAR TABLE AS SELECT ([CTAS][CTAS])
* INSERT... SELECIONE
* CRIAR O ÍNDICE
* ALTER A RECONSTRUÇÃO DO ÍNDICE
* REMOVER O ÍNDICE
* TRUNCAR A TABELA
* LARGAR A TABELA
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> As operações de movimento de dados interna (tais como `BROADCAST` e `SHUFFLE`) não são afetadas pelo limite de segurança da transação.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Mínimo de registo com o carregamento em massa
`CTAS`e `INSERT...SELECT` estão ambos em massa operações de carga. No entanto, ambos são deve influenciadas pela definição da tabela de destino e dependem do cenário de carga. Segue-se uma tabela que explica se a operação em massa será totalmente ou minimamente registada:  

| Índice principal | Cenário de carga | Modo de registo |
| --- | --- | --- |
| Área dinâmica para dados |Qualquer |**Mínimo** |
| Índice em cluster |Tabela de destino vazio |**Mínimo** |
| Índice em cluster |Linhas carregadas não se sobrepõem páginas existentes no destino |**Mínimo** |
| Índice em cluster |Carregar linhas sobrepõem páginas existentes no destino |Completo |
| Índice Columnstore em cluster |Tamanho do lote > = 102,400 por partição alinhada distribuição |**Mínimo** |
| Índice Columnstore em cluster |Tamanho < 102,400 por distribuição de partição alinhada do batch |Completo |

É importante salientar que quaisquer operações de escrita para atualizar os índices secundários ou agrupado será sempre operações totalmente iniciadas.

> [!IMPORTANT]
> O SQL Data Warehouse tem 60 distribuições. Por conseguinte, partindo do princípio de todas as linhas são distribuídas uniformemente e de destino numa única partição, o batch terá de conter 6,144,000 linhas ou maior do que será registado minimamente ao escrever num índice de Columnstore em cluster. Se a tabela está particionada e as linhas a ser introduzidas span limites de partição, em seguida, terá de 6,144,000 linhas por limites de partição, partindo do princípio de distribuição de dados, mesmo. Cada partição cada distribuição independentemente deve exceder o limiar de 102,400 linha para a inserção minimamente seja registado para a distribuição.
> 
> 

Carregar dados para uma tabela não vazia com um índice em cluster, muitas vezes, pode conter uma mistura de linhas totalmente iniciadas e minimamente com sessão iniciadas. Um índice em cluster é uma árvore com balanceamento (árvore b) de páginas. Se a página a ser escritos já contém as linhas da outra transação, em seguida, estes escritas serão totalmente registadas. No entanto, se a página está vazia, em seguida, a operação de escrita para essa página será minimamente registada.

## <a name="optimizing-deletes"></a>Otimizar as eliminações
`DELETE`é uma operação registada completamente.  Se precisar de eliminar uma grande quantidade de dados de uma tabela ou de uma partição, muitas vezes, faz sentido mais `SELECT` os dados que pretende manter, que podem ser executados como uma operação minimamente registada.  Para tal, crie uma nova tabela com [CTAS][CTAS].  Depois de criado, utilize [mudar o nome] [ RENAME] ao trocar a sua tabela antiga com a tabela recentemente criada.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Otimizar as atualizações
`UPDATE`é uma operação registada completamente.  Se tem de atualizar um grande número de linhas numa tabela ou uma partição geralmente é muito mais eficiente para utilizar como uma operação registada minimamente [CTAS] [ CTAS] para fazê-lo.

No exemplo abaixo uma tabela completa atualização foi convertida para um `CTAS` para que o registo mínimo é possível.

Neste caso, iremos retrospectively adicionar um período de desconto a de vendas na tabela:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Voltar a criar tabelas grandes pode beneficiar da utilização de funcionalidades de gestão de carga de trabalho do armazém de dados do SQL Server. Para mais detalhes, consulte a secção de gestão de carga de trabalho no [simultaneidade] [ concurrency] artigo.
> 
> 

## <a name="optimizing-with-partition-switching"></a>Otimizar com a mudança de partições
Quando confrontados com modificações de grande escala no interior de um [tabela partição][table partition], em seguida, uma partição mudar padrão torna muito sentido. Se a modificação de dados é significativa e abrange várias partições, em seguida, basta iterating sobre as partições de alcance o mesmo resultado.

Os passos para efetuar uma mudança de partições são os seguintes:

1. Criar vazio partição
2. Efetuar a atualização como uma CTAS
3. Mudar os dados existentes para a tabela de saída
4. Mudar os novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as partições para mudar vamos primeiro terá de criar um procedimento do programa auxiliar tal como o abaixo. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Este procedimento maximiza a reutilização de código e mantém a exemplo mais compactação da mudança de partições.

O código abaixo demonstra os cinco passos mencionados acima para alcançar uma rotina da mudança de partições completa.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimizar o registo com lotes pequenos
Para operações de modificação de dados de grandes dimensões, poderá fazer sentido dividir a operação em segmentos ou lotes para definir o âmbito da unidade de trabalho.

Um exemplo é fornecido abaixo. O tamanho do lote já foi definido como um número trivial para realçar a técnica. Na realidade, o tamanho do lote seria significativamente maior. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Colocar em pausa e a documentação de orientação de dimensionamento
O Azure SQL Data Warehouse permite-lhe interromper, retomar e dimensionar o seu armazém de dados a pedido. Ao colocar em pausa ou dimensionar o seu armazém de dados do SQL Server é importante compreender que quaisquer transações em trânsito estão terminadas imediatamente; a causar quaisquer transações abertas ser revertidos. Se a carga de trabalho tinha emitido uma modificação de dados longa em execução e incompleta antes da operação de colocação em pausa ou escala, em seguida, este trabalho terá de ser anulada. Isto poderá afetar o tempo que demora a colocar em pausa ou dimensionar a base de dados do Azure SQL Data Warehouse. 

> [!IMPORTANT]
> Ambos `UPDATE` e `DELETE` operações totalmente iniciadas e para que estes anulação/refazer operações podem demorar significativamente mais do que o equivalente minimamente registados operações. 
> 
> 

É o cenário de melhor para permitir a entrada de transações de modificação de dados de voo concluídas antes de colocar em pausa ou dimensionamento do armazém de dados do SQL Server. No entanto, isto poderá nem sempre ser prático. Para mitigar o risco de uma reversão longo, considere uma das seguintes opções:

* Operações longa escrever novamente utilizando [CTAS][CTAS]
* Quebrar a operação segmentos; utilizar um subconjunto das linhas

## <a name="next-steps"></a>Passos seguintes
Consulte [transações no SQL Data Warehouse] [ Transactions in SQL Data Warehouse] para saber mais sobre os níveis de isolamento e limites transacionais.  Para obter uma descrição geral de outros melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Transactions in SQL Data Warehouse]: ./sql-data-warehouse-develop-transactions.md
[table partition]: ./sql-data-warehouse-tables-partition.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

