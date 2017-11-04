---
title: Criar chaves substituto utilizando identidade | Microsoft Docs
description: "Saiba como utilizar a identidade para criar chaves de substituição no seu tabelas."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Criar chaves substituto através da utilização de identidade
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Tipos de dados][Data Types]
> * [Distribuir][Distribute]
> * [Índice][Index]
> * [Partição][Partition]
> * [Estatísticas][Statistics]
> * [Temporário][Temporary]
> * [Identidade][Identity]
> 
> 

Como muitas modelers de dados criar chaves de substituição nas respetivas tabelas quando estes modelos de armazém de dados de design. Pode utilizar a propriedade de identidade para atingir este objetivo, basta e eficaz, sem afetar o desempenho da carga. 

## <a name="get-started-with-identity"></a>Começar com uma identidade
Pode definir uma tabela como ter a propriedade de identidade ao primeiro criar a tabela utilizando a sintaxe que é semelhante à instrução seguinte:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Em seguida, pode utilizar `INSERT..SELECT` para preencher a tabela.

## <a name="behavior"></a>Comportamento
A propriedade de identidade foi concebida para aumentar horizontalmente em todas as distribuições no armazém de dados sem afetar o desempenho da carga. Por conseguinte, a implementação de identidade é dedicada a alcançar estes objetivos. Esta secção realça as nuances da implementação para o ajudar a compreendê-los mais detalhadamente.  

### <a name="allocation-of-values"></a>Alocação de valores
A propriedade de identidade não garante a ordem na qual os valores de substituição são alocados e que reflete o comportamento do SQL Server e SQL Database do Azure. No entanto, no Azure SQL Data Warehouse, mais pronunciada de ausência de uma garantia. 

O exemplo seguinte é uma ilustração:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

No exemplo anterior, duas linhas landed distribuição 1. A primeira linha com o valor de substituição de 1 coluna `C1`, e a segunda linha com o valor de substituição de 61. Ambos estes valores foram gerados pela propriedade de identidade. No entanto, a atribuição dos valores não é contígua. Este comportamento é propositado.

### <a name="skewed-data"></a>Dados distorcidos 
O intervalo de valores para o tipo de dados são distribuídos uniformemente pelas distribuições. Se sofrerá de uma tabela distribuída a partir dos dados distorcidos, em seguida, o intervalo de valores disponíveis para o tipo de dados pode ser esgotado prematuramente. Por exemplo, se todos os dados termina uma distribuição único, em seguida, efetivamente a tabela tem acesso a apenas um sixtieth dos valores do tipo de dados. Por este motivo, a propriedade de identidade está limitada a `INT` e `BIGINT` apenas tipos de dados.

### <a name="selectinto"></a>SELECIONAR... PARA
Quando uma coluna de identidade existente é selecionada para uma nova tabela, a nova coluna herda a propriedade de identidade, a menos que uma das seguintes condições for verdadeira:
- A instrução SELECT contém uma associação.
- Múltiplas instruções SELECIONADAS são associadas através da utilização de União.
- A coluna de identidade é listada mais do que uma vez na lista de SELEÇÃO.
- A coluna de identidade faz parte de uma expressão.
    
Se qualquer um dos seguintes condições for VERDADEIRO, a coluna é criada NOT NULL em vez de herdar a propriedade de identidade.

### <a name="create-table-as-select"></a>CRIAR TABLE AS SELECT
Criar tabela AS SELECIONE (CTAS) segue o mesmo comportamento de SQL Server que é descrito selecione... PARA. No entanto, não é possível especificar uma propriedade de identidade na definição de coluna a `CREATE TABLE` fazem parte da instrução. Também não é possível utilizar a função de identidade no `SELECT` fazem parte do CTAS. Para preencher uma tabela, tem de utilizar `CREATE TABLE` para definir a tabela seguida `INSERT..SELECT` para preenchê-lo.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Inserir explicitamente valores numa coluna de identidade 
Suporta o SQL Data Warehouse `SET IDENTITY_INSERT <your table> ON|OFF` sintaxe. Pode utilizar esta sintaxe de inserir explicitamente valores na coluna de identidade.

Como muitas modelers de dados a utilizar valores negativos predefinidos para algumas linhas no respetivas dimensões. Um exemplo é a -1 ou linha "membro desconhecido". 

O script seguinte mostra como adicionar explicitamente esta linha utilizando IDENTITY_INSERT definir:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Carregar dados para uma tabela com uma identidade

A presença da propriedade de identidade tem algumas implicações ao código de carregamento de dados. Esta secção realça alguns padrões básicos para carregar dados para tabelas através da utilização de identidade. 

### <a name="load-data-with-polybase"></a>Carregar dados com o PolyBase
Para carregar dados para uma tabela e gerar uma chave de substituição, utilizando a identidade, criar a tabela e, em seguida, utilizar INSERT... SELECIONE ou INSIRA... VALORES para executar a carga.

O exemplo seguinte realça o padrão básico:
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Não é possível utilizar `CREATE TABLE AS SELECT` atualmente ao carregar dados para uma tabela com uma coluna de identidade.
> 

Para mais informações sobre como carregar dados utilizando a ferramenta de programa (BCP) de cópia em massa, consulte os artigos seguintes:

- [Carregar com o PolyBase][]
- [O PolyBase melhores práticas][]

### <a name="load-data-with-bcp"></a>Carregar dados com o BCP
BCP é uma ferramenta da linha de comandos que pode utilizar para carregar dados para o SQL Data Warehouse. Um dos respetivos parâmetros (-I) controla o comportamento do BCP quando carregar dados para uma tabela com uma coluna de identidade. 

Quando é especificado -I, os valores contidos no ficheiro de entrada para a coluna com a identidade são retidos. Se for -I *não* especificado, em seguida, os valores desta coluna serão ignorados. Se a coluna de identidade não estiver incluída, os dados são carregados como normal. Os valores são gerados, de acordo com a política de incremento e seed da propriedade.

Para mais informações sobre como carregar dados utilizando o BCP, consulte os artigos seguintes:

- [Carregar com o BCP][]
- [BCP no MSDN][]

## <a name="catalog-views"></a>Vistas de catálogo
Armazém de dados do SQL Server suporta o `sys.identity_columns` vista de catálogo. Esta vista pode ser utilizada para identificar uma coluna que tem a propriedade de identidade.

Para ajudar a compreender melhor o esquema de base de dados, este exemplo mostra como integrar `sys.identity_columns` com outras vistas de catálogo de sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitações
A propriedade de identidade não pode ser utilizada nos seguintes cenários:
- Em que o tipo de dados da coluna não é INT ou BIGINT
- Em que a coluna também é a chave de distribuição
- Em que a tabela é uma tabela externa 

As seguintes funções relacionadas não são suportadas no armazém de dados do SQL Server:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Tarefas

Esta secção fornece algum código de exemplo, que pode utilizar para executar tarefas comuns quando trabalha com colunas de identidade.

> [!NOTE] 
> C1 de coluna é a identidade em todas as tarefas seguintes.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Localizar o valor mais alto alocado para uma tabela
Utilize o `MAX()` função para determinar o valor mais alto atribuído a uma tabela distribuída:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Localizar o seed e incrementação maiores para a propriedade de identidade
Pode utilizar as vistas de catálogo para detetar os valores de configuração de identidade incremento e seed, para uma tabela utilizando a seguinte consulta: 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como desenvolver tabelas, consulte [descrição geral da tabela][Overview], [tipos de dados de tabela][Data Types], [distribuir uma tabela] [ Distribute], [Uma tabela de índice][Index], [uma tabela de partição][Partition], e [ Tabelas temporárias][Temporary]. 
* Para obter mais informações sobre as melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Carregar com o bcp]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Carregar com o PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[O PolyBase melhores práticas]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[BCP no MSDN]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
