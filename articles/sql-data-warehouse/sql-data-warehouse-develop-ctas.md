---
title: Criar tabela como selecione (CTAS) no SQL Data Warehouse | Microsoft Docs
description: "Sugestões para programação com a tabela de criar como instrução de select (CTAS) no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 68ac9a94-09f9-424b-b536-06a125a653bd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 01/30/2017
ms.author: shigu;barbkess
ms.openlocfilehash: cb08313726e8135feaa9b413937c2197ea397f4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Create Table As Select (CTAS) no SQL Data Warehouse
Criar tabela como selecionar ou `CTAS` é uma das funcionalidades mais importantes do T-SQL disponíveis. É uma operação totalmente parallelized que cria uma nova tabela com base na saída de uma instrução SELECT. `CTAS`é a forma mais simples e mais rápida para criar uma cópia de uma tabela. Este documento fornece exemplos e melhores práticas para `CTAS`.

## <a name="selectinto-vs-ctas"></a>SELECIONAR... NO vs. CTAS
Pode considerar `CTAS` como uma versão Super-obrigatórios debitada do `SELECT..INTO`.

Segue-se um exemplo de uma simples `SELECT..INTO` instrução:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

No exemplo acima `[dbo].[FactInternetSales_new]` seria possível criar como tabela de distribuída ROUND_ROBIN com um índice de COLUMNSTORE em cluster no mesmo como estas são as predefinições de tabela no Azure SQL Data Warehouse.

`SELECT..INTO`No entanto não permitem-lhe alterar o método de distribuição ou o tipo de índice como parte da operação. Este é onde `CTAS` é apresentada no.

Converter o valor acima para `CTAS` é bastante simples:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

Com `CTAS` conseguir alterar ambas a distribuição dos dados da tabela, bem como o tipo de tabela. 

> [!NOTE]
> Se estiver a tentar apenas alterar o índice do seu `CTAS` operação e a tabela de origem é hash distribuída, em seguida, o `CTAS` operação executará melhor se mantém o mesmo tipo de coluna e os dados de distribuição. Evitará cruzada movimento de dados de distribuição durante a operação que é mais eficiente.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Utilizar CTAS para copiar uma tabela
Talvez uma mais comuns das utilizações de `CTAS` está a criar uma cópia de uma tabela para que o pode alterar o DDL. Se, por exemplo, que criou originalmente a tabela como `ROUND_ROBIN` e agora pretende alterá-la a uma tabela distribuída numa coluna, `CTAS` é a forma como iria alterar a coluna de distribuição. `CTAS`Também pode ser utilizado para alterar os tipos de criação de partições, indexar ou coluna.

Digamos que criou esta tabela utilizando o tipo de distribuição predefinido da `ROUND_ROBIN` distribuído, desde que não foi especificada nenhuma coluna de distribuição no `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Agora que pretende criar uma nova cópia desta tabela com um índice de Columnstore em cluster, de modo a que pode tirar partido do desempenho dos tabelas Columnstore em cluster. Também pretende distribuir esta tabela em ProductKey, uma vez que são prevendo associações nesta coluna e pretender evitar o movimento de dados durante as associações no ProductKey. Por último também pretende adicionar a criação de partições no OrderDateKey para que rapidamente pode eliminar dados antigos, arrastando partições antigas. Segue-se a instrução de CTAS que seria copie a tabela antiga para uma nova tabela.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Por fim, pode mudar o nome as tabelas para a troca na sua nova tabela e, em seguida, remover a tabela antiga.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> O Azure SQL Data Warehouse ainda não suporta a criação ou atualização automática de estatísticas.  Para obter o melhor desempenho das consultas, é importante que sejam criadas estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou após a ocorrência de quaisquer alterações substanciais nos dados.  Para obter uma explicação detalhada das estatísticas, veja o tópico [Statistics (Estatísticas)][Statistics] no grupo de tópicos Programar.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Utilizar CTAS para trabalhar em torno de funcionalidades não suportadas
`CTAS`Também pode ser utilizado para resolver um número de funcionalidades não suportadas listados abaixo. Muitas vezes, isto pode provar a ser uma situação de win/win como não apenas o código estarão em conformidade, mas são, muitas vezes, executados mais rapidamente no armazém de dados do SQL Server. Trata-se como resultado da respetiva estrutura parallelized completamente. Os cenários que podem ser trabalhados em torno com CTAS incluem:

* ASSOCIAÇÕES de ANSI nas atualizações
* Associações de ANSI em eliminações
* Declaração de intercalação

> [!NOTE]
> Tente pensar "CTAS primeiro". Se pensa que pode resolver um problema utilizando `CTAS` , em seguida, que é, geralmente, a melhor forma de abordar--, mesmo se estiver a escrever mais dados como resultado.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Substituição de associação de ANSI para instruções update
Pode considerar que tem uma atualização complexa, que associa mais de duas tabelas em conjunto com ANSI a associar a sintaxe para efetuar a ATUALIZAÇÃO ou eliminação.

Imagine que era necessário atualizar nesta tabela:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

A consulta original poderá ter de analisar algo semelhante ao seguinte:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Uma vez que não suporta o SQL Data Warehouse ANSI associações no `FROM` cláusula de um `UPDATE` declaração, não é possível copiar este código de ativação pós-falha sem ligeiramente a alteração.

Pode utilizar uma combinação de um `CTAS` e uma associação implícita para substituir este código:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Substituição de associação de ANSI para eliminar instruções
Por vezes, a melhor abordagem para eliminar dados consiste em utilizar `CTAS`. Em vez de eliminar os dados simplesmente selecione os dados que quer manter. Este particularmente verdadeiro para `DELETE` instruções que utilizar ansi sintaxe associar uma vez que o SQL Data Warehouse não suporta ANSI associações no `FROM` cláusula de um `DELETE` instrução.

Um exemplo de uma instrução DELETE convertida está disponível abaixo:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Substitua as instruções de intercalação
Instruções Merge podem ser substituídas, pelo menos, parte, utilizando `CTAS`. Pode consolidar o `INSERT` e `UPDATE` para uma instrução única. Quaisquer registos eliminados seriam necessária a ser fechado desativar numa instrução segundo.

Um exemplo de um `UPSERT` está disponível abaixo:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recomendação CTAS: Estado explicitamente o tipo de dados e nulidade da saída
Quando migrar código poderá achar que sejam executadas neste tipo de codificação padrão:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinctively se pensa deve migrar este código para um CTAS e será correto. No entanto, há um problema oculto aqui.

O seguinte código não produzir o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Tenha em atenção que a coluna "resultado" reencaminhar acarreta os valores dados do tipo e nulidade da expressão. Isto pode levar a subtis variações como valores se não tenha o cuidado.

Tente o seguinte como exemplo:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

O valor armazenado para o resultado é diferente. Como o valor persistente na coluna de resultado é utilizado em outras expressões o erro fica ainda mais significativo.

![][1]

Isto é particularmente importante para migrações de dados. Apesar da consulta segundo é possivelmente mais exata há um problema. Os dados seriam diferentes em comparação com o sistema de origem e que leva a questões de integridade na migração. Este é um dos casos raros, onde a resposta "errada" é realmente o direito!

É o motivo pelo qual que este disparidade entre os dois resultados, vemos para baixo para a conversão de tipo implícito. No exemplo primeiro a tabela define a definição da coluna. Quando a linha é inserida ocorre uma conversão de tipo implícito. No segundo exemplo não há nenhuma conversão implícita do tipo de expressão define o tipo de dados da coluna. Repare também que a coluna no segundo exemplo foi definida como uma coluna de tipo nulo enquanto o primeiro exemplo não tem. Quando a tabela foi criada no primeiro nulidade de coluna de exemplo foi explicitamente definida. No segundo exemplo-foi apenas à esquerda para a expressão e por predefinição este resultaria numa definição de um valor nulo.  

Para resolver estes problemas tem de definir explicitamente a conversão do tipo e a permissão de valores NULL no `SELECT` parte a `CTAS` instrução. Não é possível definir estas propriedades na parte da tabela de criar.

O exemplo abaixo demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenha em atenção o seguinte:

* CAST ou CONVERT foi foram utilizado
* ISNULL é utilizado para forçar a permissão de valores NULL não COALESCE
* ISNULL é a função mais exterior
* A segunda parte do ISNULL é uma constante ou seja, 0

> [!NOTE]
> Para a permissão de valores NULL ser corretamente defini-lo é vital para utilizar `ISNULL` e não `COALESCE`. `COALESCE`Não é uma função determinista e, por isso, o resultado da expressão será sempre NULLable. `ISNULL`é diferente. É determinística. Por conseguinte quando a segunda parte do `ISNULL` função é uma constante ou um literal, em seguida, o valor resultante será NOT NULL.
> 
> 

Não é apenas útil para assegurar a integridade dos seus cálculos desta sugestão. Também é importante para a mudança de partições de tabela. Imagine que tiver esta tabela definida como o facto:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

No entanto, o campo de valor é uma expressão calculada não faz parte dos dados de origem.

Para criar o conjunto de dados particionado poderá pretender fazê-lo:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

A consulta executaria perfeitamente adequada. Inclui o problema ao tentar efetuar a mudança de partições. As definições de tabela não correspondem. Para fazer corresponder o CTAS as definições de tabela tem de ser modificado.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Pode ver, por conseguinte, que tipo de consistência e a manutenção nulidade as propriedades de um CTAS é uma boa prática engenharia. Isto ajuda a manter a integridade nos seus cálculos e também garante que é possível comutar a partição.

Consulte a MSDN para obter mais informações sobre como utilizar [CTAS][CTAS]. É uma das instruções mais importantes no Azure SQL Data Warehouse. Certifique-se que compreender exaustivamente.

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
