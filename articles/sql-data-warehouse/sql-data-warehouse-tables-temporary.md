---
title: "Tabelas temporárias no SQL Data Warehouse | Microsoft Docs"
description: "Introdução ao tabelas temporárias no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 9b1119eb-7f54-46d0-ad74-19c85a2a555a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: fd8c31a727dae3b011aa8294a81f005bad72a278
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelas temporárias no SQL Data Warehouse
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

Tabelas temporárias são muito úteis durante o processamento de dados - especialmente durante a transformação onde os resultados intermédios estão transitórios. No SQL Data Warehouse tabelas temporárias existirem ao nível da sessão.  Apenas são visíveis para a sessão em que foram criadas e são automaticamente removidas quando termina a sessão.  Tabelas temporárias oferecem uma vantagem de desempenho, porque os respetivos resultados são escritos no local em vez de armazenamento remoto.  Tabelas temporárias são ligeiramente diferentes no armazém de dados SQL do Azure SQL Database do Azure que possam ser acedidos de qualquer local dentro da sessão, incluindo dentro e fora de um procedimento armazenado.

Este artigo contém documentação de orientação essencial para utilizar tabelas temporárias e realça os princípios de tabelas temporárias ao nível de sessão. Utilizar as informações deste artigo pode ajudá-lo modularize código, melhorando reusability e facilidade de manutenção do seu código.

## <a name="create-a-temporary-table"></a>Criar uma tabela temporária
São criadas tabelas temporárias atribuindo simplesmente lhe o prefixo do nome de tabela com um `#`.  Por exemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Também é possível criar tabelas temporárias com um `CTAS` utilizando exatamente a mesma abordagem:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

> [!NOTE]
> `CTAS`é um comando muito poderoso e tem a vantagem adicional de que está a ser muito eficientes na sua utilização do espaço de registo de transações. 
> 
> 

## <a name="dropping-temporary-tables"></a>Remover tabelas temporárias
Quando é criada uma nova sessão, não existem tabelas temporárias devem existir.  No entanto, se estiver a chamar o procedimento armazenado mesmo, que cria um temporário com o mesmo nome, certifique-se de que o `CREATE TABLE` instruções são com êxito uma verificação de pré-existência simple com um `DROP` podem ser utilizados como no exemplo abaixo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para consistência de codificação, é uma boa prática para utilizar este padrão de tabelas e tabelas temporárias.  Também é uma boa ideia utilizar `DROP TABLE` para remover a tabelas temporárias quando tiver terminado com os mesmos no seu código.  No procedimento armazenado desenvolvimento é bastante comum para ver os comandos de largar agrupadas no final de um procedimento para garantir que estes objetos são limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Código modularizing
Uma vez que a tabelas temporárias podem ser vistas em qualquer lugar numa sessão de utilizador, este pode ser explorada para ajudar a modularize código da aplicação.  Por exemplo, o procedimento armazenado abaixo reúne as práticas recomendadas de acima para gerar DDL que irá atualizar todas as estatísticas na base de dados pelo nome da estatística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Nesta fase, a única ação que tenha ocorrido é a criação de um procedimento armazenado que simplesmente será gerada uma tabela temporária, stats_ddl #, com as instruções DDL.  Este procedimento armazenado irá remover #stats_ddl se já existir Certifique-se de que não falha se mais do que uma vez executado dentro de uma sessão.  No entanto, uma vez que não existe nenhum `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, que irá deixar a tabela criada para que possa ser lido fora do procedimento armazenado.  No SQL Data Warehouse, ao contrário de outras bases de dados do SQL Server, é possível utilizar a tabela temporária fora o procedimento que o criou.  Podem ser utilizadas a tabelas temporárias do SQL Data Warehouse **em qualquer lugar** dentro da sessão. Isto pode levar a mais modular e gerível código como o exemplo abaixo:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitações de tabela temporária
O SQL Data Warehouse impõe algumas limitações ao efetuar a tabelas temporárias.  Atualmente, apenas a sessão de tabelas temporárias âmbito são suportadas.  As tabelas temporárias globais não são suportadas.  Além disso, não não possível criar vistas de tabelas temporárias.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os artigos no [descrição geral da tabela][Overview], [tipos de dados de tabela][Data Types], [distribuir uma tabela] [ Distribute], [Uma tabela de indexação][Index], [uma tabela de criação de partições] [ Partition] e [Manter as estatísticas da tabela][Statistics].  Para obter mais informações sobre as melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
