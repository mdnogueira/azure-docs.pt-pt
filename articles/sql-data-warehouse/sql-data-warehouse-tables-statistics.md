---
title: "Gerir as estatísticas em tabelas no armazém de dados SQL | Microsoft Docs"
description: "Introdução ao estatísticas de tabelas do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: 1d5ded69e394643ddfc3de0c6d30dbd30c8e848f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gerir as estatísticas em tabelas no armazém de dados do SQL Server
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

Mais SQL Data Warehouse conhece os dados, quanto mais depressa pode executar consultas contra os dados.  A forma como o que dizer do armazém de dados do SQL Server sobre os dados, é através da recolha de estatísticas sobre os dados.  Estatísticas nos seus dados é ter uma das ações mais importantes, pode fazê-lo a otimizar as suas consultas.  Estatísticas de ajudam a criar o plano ideal para consultas do SQL Data Warehouse.  Isto acontece porque o otimizador de consultas do SQL Data Warehouse é um otimizador de custo com base.  Ou seja, compara o custo de vários planos de consulta e, em seguida, escolhe o plano com o menor custo, também deve ser o plano que irão executar o mais rápido.

Podem ser criadas estatísticas no única coluna, várias colunas ou um índice de uma tabela.  As estatísticas são armazenadas num histograma que capture o intervalo e selectivity dos valores.  Trata-se do especial interesse dos quando o otimizador tem de avaliar associações, GROUP BY, HAVING e cláusulas WHERE numa consulta.  Por exemplo, se o otimizador estimativas de que a data está a filtrar na sua consulta devolverá 1 linha, pode escolher a muito diferentes planear que o se se estimativas que data selecionou irá devolver 1 milhões de linhas.  Enquanto é extremamente importante criar estatísticas, é igualmente importante que estatísticas *com precisão* refletem o estado atual da tabela.  Ter estatísticas atualizadas garante que um bom plano está selecionado pelo otimizador.  Os planos criados pelo otimizador são apenas como boas como as estatísticas nos seus dados.

O processo de criação e a atualizar as estatísticas é atualmente um processo manual, mas é muito simple para o fazer.  Tal não acontecia no SQL Server, que cria e atualiza as estatísticas de índices e colunas único automaticamente.  Ao utilizar as informações abaixo, pode automatizar bastante a gestão das estatísticas nos seus dados. 

## <a name="getting-started-with-statistics"></a>Introdução ao estatísticas
 Criar estatísticas amostras em cada coluna é uma forma fácil de começar com as estatísticas.  Uma vez que é igualmente importante manter as estatísticas de atualizados, pode ser uma abordagem conservador atualizar as estatísticas diária ou após cada carga. Há sempre uma relação entre o desempenho e o custo para criar e atualizar as estatísticas.  Se achar que está a demorar demasiado tempo a manter todas as estatísticas, deve tentar ser mais seletivo sobre as colunas que têm estatísticas ou as colunas que precisam de atualização frequente.  Por exemplo, pode querer atualizar colunas de data diariamente, como podem ser adicionados novos valores em vez de após cada carga. Novamente, irá obter mais benefício, fazendo com que as estatísticas em colunas envolvidas em associações, GROUP BY, HAVING e cláusulas WHERE.  Se tiver uma tabela com um grande número de colunas que são utilizadas apenas na cláusula SELECT, estatísticas destas colunas não podem ajudar e gastos um pouco maior esforço para identificar apenas as colunas onde irão ajudar a estatísticas, pode reduzir o tempo para manter as estatísticas.

## <a name="multi-column-statistics"></a>Estatísticas de várias colunas
Para além de criar estatísticas em colunas único, pode considerar que as suas consultas irão beneficiar de estatísticas de várias colunas.  Estatísticas de várias colunas são criadas numa lista de colunas de estatísticas.  Incluem estatísticas de coluna única na primeira coluna na lista, mais algumas informações de correlação entre coluna chamado densities.  Por exemplo, se tiver uma tabela que é associado a outro em duas colunas, pode considerar que o SQL Data Warehouse melhor pode otimizar o plano de se compreender a relação entre duas colunas.   Estatísticas de várias colunas podem melhorar o desempenho de consulta para algumas operações, tais como associações compostas e agrupar por.

## <a name="updating-statistics"></a>A atualizar as estatísticas
A atualizar as estatísticas é uma parte importante da sua rotina de gestão de base de dados.  Quando altera a distribuição de dados na base de dados, necessitam de atualizar as estatísticas.  Estatísticas Desatualizadas resultará desempenho das consultas inferiores às ideais.

Um procedimento recomendado é atualizar estatísticas de colunas de data por dia datas novas são adicionadas.  Cada novas linhas de tempo são carregados para o armazém de dados, datas de carga novo ou datas de transação são adicionadas. Estes alterar a distribuição de dados e tome as estatísticas desatualizados. Por outro lado, as estatísticas de uma coluna de país numa tabela cliente nunca poderão ter de ser atualizado, como a distribuição dos valores, geralmente, não altere. Partindo do princípio que de distribuição é constante entre os clientes, adicionar novas linhas para a variação de tabela não se encontra vai alterar a distribuição de dados. No entanto, se o armazém de dados contém apenas um país e colocar dados de um novo país, resultando em dados a partir de vários países que está a ser armazenados, em seguida, sem dúvida terá de atualizar a estatística na coluna de país.

Uma das primeiras questões colocadas pedir quando uma consulta de resolução de problemas, "São as estatísticas atualizadas?"

Esta questão não é um que pode ser respondida pela idade dos dados. Um objeto de estatísticas atualizados pode ser muito antigo não se tiver ocorrido nenhuma alteração essenciais para os dados subjacentes. Quando o número de linhas mudou substancialmente ou existe uma alteração essenciais da distribuição dos valores para uma determinada coluna *, em seguida,* está na altura de atualizar as estatísticas.  

Para referência, **do SQL Server** (não o SQL Data Warehouse) atualiza automaticamente as estatísticas para estas situações:

* Se tiver zero linhas na tabela, quando adiciona linhas, obterá uma atualização automática de estatísticas
* Ao adicionar mais do que 500 linhas para uma tabela a partir com menos de 500 linhas (por exemplo, no início tiver 499 e, em seguida, adicionar 500 linhas para um total de 999 linhas), obterá uma atualização automática 
* Assim que tiver mais de 500 linhas, terá de adicionar 500 linhas adicionais + 20% do tamanho da tabela antes de irá ver uma atualização automática nas estatísticas

Uma vez que não existe nenhum DMV para determinar se os dados na tabela foi alterada desde que foram atualizadas as última estatísticas de tempo, saber a idade dos seus estatísticas pode fornecer com parte da imagem.  Pode utilizar a seguinte consulta para determinar a última vez que as estatísticas onde atualizadas em cada tabela.  

> [!NOTE]
> Lembre-se de que o se houver uma alteração essenciais da distribuição dos valores para uma determinada coluna, deve atualizar estatísticas, independentemente da última vez que foram atualizados.  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Colunas de data no armazém de dados, por exemplo, normalmente, tem de induzirem frequentes estatísticas atualizações. Cada novas linhas de tempo são carregados para o armazém de dados, datas de carga novo ou datas de transação são adicionadas. Estes alterar a distribuição de dados e tome as estatísticas desatualizados.  Por outro lado, estatísticas de uma coluna de sexo numa tabela cliente nunca poderão ter de ser atualizados. Partindo do princípio que de distribuição é constante entre os clientes, adicionar novas linhas para a variação de tabela não se encontra vai alterar a distribuição de dados. No entanto, se o seu armazém de dados contém apenas um sexo e um novo resultados de requisito em vários genders sem dúvida terá de atualizar as estatísticas na coluna sexo.

Para uma explicação mais, consulte [estatísticas] [ Statistics] no MSDN.

## <a name="implementing-statistics-management"></a>Implementar a gestão de estatísticas
Muitas vezes, é uma boa ideia para expandir os processo para garantir que as estatísticas são atualizadas no fim de carga do carregamento de dados. O carregamento de dados é quando tabelas com mais frequência a alterar o tamanho e/ou a respetiva distribuição dos valores. Por conseguinte, este é um local lógico ao implementar alguns processos de gestão.

Alguns princípios de orientação para são fornecidos abaixo para atualizar as estatísticas durante o processo de carga:

* Certifique-se de que cada tabela carregada tem, pelo menos, um objeto de estatísticas atualizado. Isto atualiza as informações de tamanho (contagem de linhas e contagem de páginas) de tabelas como parte da atualização de estatísticas.
* Concentre-se em colunas que participam na associação, GROUP BY, ORDER BY e DISTINCT cláusulas
* Considere a atualização de colunas "chave ascendente" como transação datas mais frequentemente estes valores não serão incluídos em histograma de estatísticas.
* Considere atualizar colunas de distribuição estático menos frequência.
* Lembre-se de que cada objeto de estatística é atualizado na série. Basta implementar `UPDATE STATISTICS <TABLE_NAME>` não pode ser o ideal - especialmente para tabelas wide com muitos objetos de estatísticas.

> [!NOTE]
> Para obter mais detalhes sobre [chave ascendente]. consulte o documento de técnico de modelo de estimativa de cardinalidade do SQL Server 2014.
> 
> 

Para uma explicação mais, consulte [estimativa de cardinalidade] [ Cardinality Estimation] no MSDN.

## <a name="examples-create-statistics"></a>Exemplos: Criar estatísticas
Estes exemplos mostram como utilizar várias opções para criar estatísticas. As opções que utilizar para cada coluna dependem as características de dados e a forma como a coluna será utilizada nas consultas.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. Criar estatísticas de coluna única com opções predefinidas
Para criar estatísticas numa coluna, só tem de fornecer um nome para o objeto de estatísticas e o nome da coluna.

Esta sintaxe utiliza todas as opções predefinidas. Por predefinição, o SQL Data Warehouse amostras 20 por cento da tabela quando cria estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Criar estatísticas de coluna única, examinando cada linha
A frequência de amostragem da predefinição de 20 por cento é suficiente para a maioria das situações. No entanto, pode ajustar a frequência de amostragem.

Para a tabela completa de exemplo, utilize esta sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Criar estatísticas de coluna única, especificando o tamanho da amostra
Em alternativa, pode especificar o tamanho da amostra como uma percentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Criar estatísticas de coluna única na apenas algumas das linhas
Outra opção, pode criar estatísticas numa parte das linhas na tabela. Esta opção é denominada uma estatística filtrada.

Por exemplo, pode utilizar as estatísticas filtradas quando planeia uma partição específica de uma tabela particionada grande de consulta. Ao criar estatísticas em apenas os valores de partição, a precisão das estatísticas melhorar e, por conseguinte, melhorar o desempenho das consultas.

Este exemplo cria estatísticas num intervalo de valores. Os valores facilmente pode ser definidos para corresponder ao intervalo de valores numa partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para o otimizador de consultas ponderar a utilização de estatísticas filtradas quando escolhe o plano de consulta distribuída, a consulta deve caber a definição do objeto de estatísticas. Utilizando o exemplo anterior, a qual cláusula tem de especificar valores de col1 entre 2000101 e 20001231 da consulta.
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Criar estatísticas de coluna única com todas as opções
Obviamente, pode combinar as opções em conjunto. O exemplo abaixo cria um objeto de estatísticas filtradas com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para referência completa, consulte [CREATE STATISTICS] [ CREATE STATISTICS] no MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Criar estatísticas de várias colunas
Para criar uma estatísticas de várias colunas, basta utilizar exemplos anteriores, mas especificar mais colunas.

> [!NOTE]
> Histograma, que é utilizada para estimar o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição do objeto de estatísticas.
> 
> 

Neste exemplo, a histograma é no *produto\_categoria*. Estatísticas de coluna em vários locais são calculadas *produto\_categoria* e *produto\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Uma vez que existe uma correlação entre *produto\_categoria* e *produto\_sub\_categoria*, estado várias coluna pode ser úteis se estas colunas são acedidas ao mesmo tempo.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Criar estatísticas em todas as colunas existentes numa tabela
É uma forma de criar estatísticas para problemas comandos CREATE STATISTICS depois de criar a tabela.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Utilizar um procedimento armazenado para criar estatísticas em todas as colunas numa base de dados
Armazém de dados do SQL Server não tem um procedimento de sistema armazenados equivalente [sp_create_stats] [-] no SQL Server. Este procedimento armazenado cria um objeto de estatísticas de coluna única em cada coluna da base de dados que já não tem as estatísticas.

Isto irá ajudar a começar com o design da sua base de dados. Não hesite adaptá-lo para as suas necessidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas em todas as colunas na tabela com este procedimento, basta chame o procedimento.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Exemplos: estatísticas de atualização
Para atualizar as estatísticas, pode:

1. Atualize um objeto de estatísticas. Especifique o nome do objeto que pretende atualizar as estatísticas.
2. Atualize todos os objetos de estatísticas numa tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específico.

### <a name="a-update-one-specific-statistics-object"></a>A. Atualizar um objeto de estatísticas específico
Utilize a sintaxe seguinte para atualizar um objeto de estatísticas específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específico, pode minimizar o tempo e recursos necessários para gerir as estatísticas. Isto requer algum profundamente, no entanto, para escolher os objetos de estatísticas melhor a atualizar.

### <a name="b-update-all-statistics-on-a-table"></a>B. Atualizar todas as estatísticas numa tabela
Mostra um método simples para atualizar todos os objetos de estatísticas numa tabela.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

É fácil de utilizar esta instrução. Lembre-se apenas de todas as estatísticas na tabela de atualizações e, por isso, poderá efetuar trabalho mais do que é necessário. Se o desempenho não for um problema, esta é sem dúvida a forma mais fácil e mais completa para garantir que as estatísticas são atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas numa tabela, o SQL Data Warehouse efetua uma análise para a tabela para cada estatísticas de exemplo. Se a tabela for grande, tem demasiadas colunas e estatísticas de muitas, poderá ser mais eficiente para atualizar as estatísticas de individuais com base na necessidade.
> 
> 

Para uma implementação de um `UPDATE STATISTICS` procedimento, consulte o [tabelas temporárias] [ Temporary] artigo. O método de implementação é ligeiramente diferente para o `CREATE STATISTICS` procedimento acima, mas o resultado final é igual.

Para a sintaxe completa, consulte [Update Statistics] [ Update Statistics] no MSDN.

## <a name="statistics-metadata"></a>Metadados de estatísticas
Existem várias funções que pode utilizar para obter informações sobre as estatísticas e vista de sistema. Por exemplo, pode ver se um objeto de estatísticas pode estar desatualizado utilizando a função de data estatísticas para ver quando estatísticas foram criadas ou atualizadas pela última vez.

### <a name="catalog-views-for-statistics"></a>Vistas de catálogo para estatísticas
Estas vistas de sistema fornecem informações sobre as estatísticas:

| Vista de catálogo | Descrição |
|:--- |:--- |
| [sys.Columns][sys.columns] |Uma linha para cada coluna. |
| [sys.Objects][sys.objects] |Uma linha para cada objeto na base de dados. |
| [sys.schemas][sys.schemas] |Uma linha para cada esquema na base de dados. |
| [sys.Stats][sys.stats] |Uma linha para cada objeto de estatísticas. |
| [sys.stats_columns][sys.stats_columns] |Uma linha para cada coluna no objeto de estatísticas. Fornece hiperligações para sys.columns. |
| [sys. Tables][sys.tables] |Uma linha para cada tabela (inclui as tabelas externas). |
| [sys.table_types][sys.table_types] |Uma linha para cada tipo de dados. |

### <a name="system-functions-for-statistics"></a>Funções de sistema para estatísticas
Estas funções de sistema são úteis para trabalhar com as estatísticas:

| Função de sistema | Descrição |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Data que da última atualização o objeto de estatísticas. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Fornece resumidas nível e informações detalhadas sobre a distribuição dos valores como compreendido pelo objeto estatísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar as colunas de estatísticas e as funções para uma vista
Esta vista coloca colunas relacionados com as estatísticas e resulta na função de [] [STATS_DATE()] em conjunto.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() mostra os dados retidos dentro de um objeto de estatísticas. Estes dados é apresentada no três partes.

1. Cabeçalho
2. Vetor de densidade
3. Histograma

Os metadados de cabeçalho sobre as estatísticas. A histograma mostra a distribuição dos valores na primeira coluna chave do objeto de estatísticas. O vetor de densidade mede a correlação de coluna em vários locais. SQLDW calcula estimativas de cardinalidade com quaisquer dados no objeto de estatísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar o cabeçalho, densidade e histograma
Neste exemplo simples mostra todos os três partes de um objeto de estatísticas.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrar uma ou mais partes de DBCC SHOW_STATISTICS();
Se apenas estiver interessado em partes específicas de visualização, utilize o `WITH` cláusula e especifique as peças que pretende ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Diferenças de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() mais estritamente é implementado no armazém de dados SQL relativamente ao SQL Server.

1. Não são suportadas as funcionalidades não documentadas
2. Não é possível utilizar Stats_stream
3. Não é possível associar resultados para subconjuntos específicos de dados de estatísticas por exemplo, (STAT_HEADER associação DENSITY_VECTOR)
4. Não é possível definir NO_INFOMSGS para supressão de mensagem
5. Não é possível utilizar parênteses Retos à volta de nomes de estatísticas
6. Não é possível utilizar nomes de colunas para identificar objetos de estatísticas
7. Erro personalizado 2767 não é suportado

## <a name="next-steps"></a>Passos seguintes
Para obter mais detalhes, consulte [DBCC SHOW_STATISTICS] [ DBCC SHOW_STATISTICS] no MSDN.  Para obter mais informações, consulte os artigos no [descrição geral da tabela][Overview], [tipos de dados de tabela][Data Types], [distribuir uma tabela] [ Distribute], [Uma tabela de indexação][Index], [uma tabela de criação de partições] [ Partition] e [Tabelas temporárias][Temporary].  Para obter mais informações sobre as melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].  

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
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
