---
title: "Distribuir a tabelas no armazém de dados SQL | Microsoft Docs"
description: "Introdução à distribuição de tabelas do Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: d0e12bf821a81826a20b8db84e76c48fa60ad9b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribuir a tabelas no armazém de dados do SQL Server
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

O SQL Data Warehouse é um sistema de base de dados distribuído de processamento paralelo em massa (MPP).  Ao dividir os dados e a capacidade de processamento entre vários nós, o SQL Data Warehouse pode oferecer uma enorme escalabilidade, muito além de qualquer sistema único.  Decidir como distribuir os seus dados dentro do SQL Data Warehouse é um dos fatores importantes para alcançar um desempenho ideal.   A chave para um desempenho ideal é minimizar o movimento de dados e por sua vez a chave para minimizar o movimento de dados é selecionando a estratégia de distribuição à direita.

## <a name="understanding-data-movement"></a>Movimento de dados de conhecimento
Num sistema MPP, os dados de cada tabela estão divididos em várias bases de dados subjacentes.  As consultas mais otimizadas num sistema MPP simplesmente podem ser transmitidas para executar nas bases de dados distribuídas individuais sem interação entre as outras bases de dados.  Por exemplo, vamos supor que tem uma base de dados com dados de vendas que contém duas tabelas, vendas e clientes.  Se tiver uma consulta que precisa de associar a tabela de venda a sua tabela de cliente e dividir as suas vendas e tabelas de cliente cópias de segurança por número de cliente, colocando a cada cliente numa base de dados separada, todas as consultas que aderir ao cliente e de vendas possam ser resolvidas dentro de cada base de dados com sem qualquer conhecimento das outras bases de dados.  Em contrapartida, se dividir os dados de vendas por número de ordem e os dados de cliente por número de cliente, em seguida, qualquer base de dados indicado não terá a data correspondente para cada cliente e, consequentemente, se pretendesse associar os dados de vendas aos seus dados de cliente, terá de obter os dados de cada cliente, a partir de outras bases de dados.  Neste exemplo segundo, o movimento de dados tem de ocorrer mover os dados de clientes para os dados de vendas, para que duas tabelas podem ser associadas.  

Movimento de dados não se encontra sempre uma coisa danificada, por vezes, é necessário resolver uma consulta.  Mas, quando este passo adicional pode ser evitado, naturalmente sua consulta serão são executadas mais rápido.  Movimento de dados for mais frequentemente quando estão associadas a tabelas ou agregações são executadas.  Muitas vezes, precisa de fazer ambos, por isso enquanto poderá otimizar para um cenário, como uma associação, terá ainda de movimento de dados para ajudar a resolver para o cenário como uma agregação.  O truque é perceber que é menor trabalho.  Na maioria dos casos, distribuir as tabelas de factos grande numa coluna normalmente associada é o método mais eficaz para reduzir o movimento de dados mais.  Distribuição de dados em colunas de associação é um método muito mais comum para reduzir o movimento de dados de distribuição de dados em colunas envolvidos numa agregação.

## <a name="select-distribution-method"></a>Selecione o método de distribuição
Nos bastidores, o SQL Data Warehouse divide os dados em 60 bases de dados.  Cada base de dados individuais é referido como um **distribuição**.  Quando os dados são carregados para cada tabela, do armazém de dados do SQL Server tem de saber como dividir os dados entre estes 60 distribuições.  

O método de distribuição é definido ao nível da tabela e atualmente, existem duas opções:

1. **O round robin** que distribui dados uniformemente mas aleatoriamente.
2. **A função hash distribuídas** que distribui dados com base em hash valores de uma única coluna

Por predefinição, quando não definem um método de distribuição de dados, a tabela será distribuída utilizando o **round robin** método de distribuição.  No entanto, à ficam mais sofisticadas na sua implementação, convém considerar a utilização de **hash distribuída** tabelas para minimizar o movimento de dados que por sua vez irão otimizar o desempenho de consulta.

### <a name="round-robin-tables"></a>O Round Robin tabelas
Utilizando o método de Round Robin de distribuição de dados é muito como-SOA.  Como os dados são carregados, cada linha é simplesmente enviada para a distribuição seguinte.  Este método de distribuir os dados serão sempre aleatoriamente distribuir os dados muito uniformemente em todas as distribuições.  Ou seja, não há qualquer ordenação efetuada durante o processo de round robin que coloca os dados.  Uma distribuição round robin denomina-se um hash aleatório por este motivo.  Com uma tabela distribuída round robin, não é necessário compreender os dados.  Por este motivo, tabelas Round-Robin, muitas vezes, tornar carregamento boa destinos.

Por predefinição, não se for escolhido nenhum método de distribuição, o método de distribuição round robin será utilizado.  No entanto, enquanto o round robin tabelas são fáceis de utilizar, porque os dados aleatoriamente são distribuídos por sistema significa que o sistema não pode garantir qual distribuição cada linha é no.  Como resultado, o sistema, por vezes, tem de invocar uma operação de movimento de dados para organizar melhor os dados antes de pode resolver uma consulta.  Este passo adicional pode atrasar as suas consultas.

Considere utilizar o Round Robin distribuição para a sua tabela nos seguintes cenários:

* Quando introdução como um ponto de partida simple
* Se não houver nenhuma chave joining óbvios
* Se não houver coluna bom candidato para distribuir a tabela de hash
* Se a tabela não partilha uma chave de associação comuns com outras tabelas
* Se a associação é menos significativa que outros associações na consulta
* Quando a tabela é uma tabela de transição temporária

Ambos estes exemplos irão criar uma tabela de Round Robin:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> Enquanto o round robin é o tipo de tabela predefinido a ser explícita na sua DDL é considerado uma melhor prática, para que as intenções do seu esquema de tabela são limpar a outras pessoas.
>
>

### <a name="hash-distributed-tables"></a>Distribuída tabelas de hash
Utilizar um **Hash distribuída** algoritmo para distribuir as tabelas pode melhorar o desempenho de muitos cenários ao reduzir o movimento de dados no momento da consulta.  As tabelas hash distribuída são tabelas que são divididas entre as bases de dados distribuídas utilizando um algoritmo hash em apenas uma coluna que selecionar.  A coluna de distribuição é o que determina a forma como os dados são divididos entre as bases de dados distribuídas.  A função hash utiliza a coluna de distribuição para atribuir linhas para as distribuições.  O algoritmo hash e a distribuição resultante é determinística.  Que é o mesmo valor com o mesmo tipo de dados será tem sempre para a distribuição do mesma.    

Neste exemplo, irá criar uma tabela distribuída no id de:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Selecione a coluna de distribuição
Se decidir **hash distribuir** uma tabela, terá de selecionar uma coluna de distribuição único.  Quando selecionar uma coluna de distribuição, existem três principais fatores a considerar.  

Selecione uma coluna que irá:

1. Não é possível atualizar
2. Distribuir dados uniformemente, evitando dados dissimetrias
3. Minimizar o movimento de dados

### <a name="select-distribution-column-which-will-not-be-updated"></a>Selecione a coluna de distribuição que não será atualizada
Colunas de distribuição não são atualizáveis, por conseguinte, selecione uma coluna com valores estáticos.  Se uma coluna tem de ser atualizado, geralmente, não é um candidato a distribuição bom.  Se existir um caso onde deve atualizar uma coluna de distribuição, isto pode ser feito pelo primeiro a eliminação da linha e, em seguida, a inserir uma nova linha.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Selecione a coluna de distribuição que será a distribuir uniformemente dados
Uma vez que um sistema distribuído efetua apenas tão rápido como a distribuição da mesma mais lenta, é importante dividir o trabalho uniformemente entre as distribuições para alcançar a execução com balanceamento entre o sistema.  A forma como o trabalho está dividido num sistema distribuído baseia onde os dados para cada distribuição exista.  Isto torna muito importante selecionar a coluna da direita da distribuição para distribuir os dados para que cada distribuição tem trabalho igual e irá demorar o mesmo tempo a concluir a parte do trabalho.  Quando o trabalho também é dividido entre o sistema, os dados são equilibrados nas distribuições.  Quando os dados não é uniformemente balanceamento, chamamos a isto **desfasamento de dados**.  

Para dividir dados uniformemente e evitar dados dissimetrias, considere o seguinte quando selecionar a coluna de distribuição:

1. Selecione uma coluna que contém um número significativo de valores distintos.
2. Evite distribuição de dados em colunas com alguns valores distintos.
3. Evite distribuição de dados em colunas com uma frequência alta de valores NULL.
4. Evite distribuição de dados em colunas de data.

Uma vez que cada valor é protegido por hash para 1 de 60 distribuições, para conseguir o mesmo distribuição deverá selecionar uma coluna que é exclusiva de elevada disponibilidade e contém mais de 60 valores exclusivos.  Para ilustrar, considere o caso em que uma coluna tem 40 valores exclusivos apenas.  Se esta coluna foi selecionada como a chave de distribuição, os dados para essa tabela seriam apresentado no 40 distribuições no máximo, deixando 20 distribuições com nenhum dado não o processamento de e para fazer.  Por outro lado, as distribuições de 40 teria mais trabalho fazê-lo se os dados foi uniformemente distribuídos mais de 60 distribuições.  Este cenário é um exemplo de dados dissimetrias.

Num sistema MPP, cada passo de consulta aguarda para todas as distribuições de concluir as respetivas partilha do trabalho.  Se uma distribuição está a fazer mais tarefas que os outros, em seguida, o recurso das distribuições de outros são essencialmente desperdiçado apenas a aguardar a distribuição ocupado.  Quando o trabalho não é distribuído uniformemente por todas as distribuições, chamamos a isto **processamento dissimetrias**.  O desfasamento de processamento fará consultas para executar inferior a se a carga de trabalho pode ser distribuída uniformemente pelas distribuições.  Dados dissimetrias direciona dissimetrias do processamento.

Evite distribuição na coluna de elevada disponibilidade que pode ser nula, como os valores nulos existentes serão apresentado de distribuição da mesma. Distribuição de uma coluna de data também pode causar o desfasamento de processamento porque todos os dados para uma determinada data serão encaminhado para a distribuição do mesmo. Se vários utilizadores estão a executar consultas todas as filtragens na mesma data, em seguida, apenas 1 as 60 distribuições a irá fazer todo o trabalho desde uma data especificada só será uma distribuição. Neste cenário, as consultas, provavelmente, serão executado 60 vezes mais lentas do que o se os dados foram igualmente distribuídos por todas as distribuições.

Quando não existe nenhuma coluna bom candidato, em seguida, considere utilizar o round robin como método de distribuição.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Selecione a coluna de distribuição que irá minimizar o movimento de dados
Minimizar o movimento de dados selecionando a coluna da direita da distribuição é uma das estratégias mais importantes para otimizar o desempenho do seu SQL Data Warehouse.  Movimento de dados for mais frequentemente quando estão associadas a tabelas ou agregações são executadas.  As colunas utilizadas no `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING` cláusulas todas tornam para **boa** candidatos a distribuição de hash.

Por outro lado, as colunas no `WHERE` cláusula efetue **não** efetuar para candidatos de coluna de hash boa porque estes limitam as distribuições participam na consulta, fazendo com que o processamento dissimetrias.  Um bom exemplo de uma coluna que pode ser tempting para distribuir no, mas, muitas vezes, pode causar este processamento dissimetrias é uma coluna de data.

Um modo geral, se tiver duas tabelas de factos grande frequentemente envolvidas numa associação, irá obter o desempenho da maioria por distribuição de ambas as tabelas das colunas de associação.  Se tiver uma tabela que nunca está associada a outra tabela de factos grande, em seguida, procure às colunas que são frequentemente no `GROUP BY` cláusula.

Existem alguns chaves os critérios que têm de ser cumpridos para evitar o movimento de dados durante uma associação:

1. As tabelas envolvidos na União tem de ser hash distribuído **um** das colunas participar na União.
2. Os tipos de dados das colunas de associação tem de corresponder entre ambas as tabelas.
3. As colunas tem de ser associadas com um operador igual a.
4. O tipo de associação não pode ser um `CROSS JOIN`.

## <a name="troubleshooting-data-skew"></a>Dados dissimetrias de resolução de problemas
Quando os dados da tabela são distribuídos utilizando o método de distribuição hash há a possibilidade de que irão ser skewed algumas distribuições ter disproportionately mais dados que outras pessoas. Dados excessivo desfasamento pode afetar o desempenho de consulta porque o resultado final de uma consulta distribuída tem de aguardar a maior distribuição concluir. Consoante o grau de desfasamento de dados poderá ter de resolver.

### <a name="identifying-skew"></a>Identificar desfasamento
Uma forma simples para identificar uma tabela como skewed consiste em utilizar `DBCC PDW_SHOWSPACEUSED`.  Esta é uma forma muito rápida e simple para ver o número de linhas de tabela que estão armazenados em cada um das distribuições de 60 da base de dados.  Lembre-se de que para um desempenho mais equilibrado, as linhas na tabela distribuída devem ser distribuídas uniformemente por todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, se consultar as vistas de gestão dinâmica do Azure SQL Data Warehouse (DMV) pode executar uma análise mais detalhada.  Para iniciar, crie a vista [dbo.vTableSizes] [ dbo.vTableSizes] ver utilizando o SQL a partir do [descrição geral da tabela] [ Overview] artigo.  Depois da vista está a ser criada, execute esta consulta para identificar que tabelas tem mais do que o desfasamento de dados de 10%.

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Resolver dados dissimetrias
Não todos os dissimetrias é suficiente para o garantir uma correção.  Em alguns casos, o desempenho de uma tabela em algumas consultas pode prevalecem sobre a possibilidade de dados dissimetrias.  Para decidir se deve resolver dados dissimetrias numa tabela, deve compreender quanto possível sobre os volumes de dados e consultas na sua carga de trabalho.   Uma forma de consultar o impacto de desfasamento consiste em utilizar os passos a [consulta monitorização] [ Query Monitoring] artigo para monitorizar o impacto da dissimetrias no desempenho das consultas e especificamente o impacto para como tempo consulta tomar para ficar concluída nas distribuições individuais.

Distribuição de dados é um fim de encontrar o equilíbrio certo entre minimizando desfasamento de dados e minimizar o movimento de dados. Estes podem ser opposing objetivos e, por vezes, irá querer manter os dados dissimetrias para reduzir o movimento de dados. Por exemplo, quando a coluna de distribuição é frequentemente a coluna partilhada na associações e agregações, irá ser minimizando movimento de dados. A vantagem de ter o movimento de dados mínimo poderá compensar o impacto da eliminação de dados desfasamento.

A forma típica para resolver o desfasamento de dados é recriar a tabela com uma coluna de distribuição diferente. Uma vez que não é possível alterar a coluna de distribuição numa tabela existente, a forma como para alterar a distribuição de uma tabela para recriá-la com uma [[CTAS]].  Seguem-se dois exemplos de como resolver dados dissimetrias:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exemplo 1: Recrie a tabela com uma nova coluna de distribuição
Este exemplo utiliza [CTAS] [-] voltar a criar uma tabela com uma coluna de distribuição hash diferentes.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exemplo 2: Recrie a tabela utilizando o round robin distribuição
Este exemplo utiliza [CTAS] [-] voltar a criar uma tabela com o round robin, em vez de uma distribuição hash. Esta alteração produzirá distribuição de dados, mesmo ao custo de movimento de dados maior.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a estrutura da tabela, consulte o [distribuir][Distribute], [índice][Index], [partição][Partition], [tipos de dados][Data Types], [estatísticas] [ Statistics] e [tabelas temporárias] [ Temporary] artigos.

Para obter uma descrição geral de melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

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
[Query Monitoring]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
