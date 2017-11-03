---
title: "Migrar o seu código SQL ao SQL Data Warehouse | Microsoft Docs"
description: "Sugestões para migrar o código do SQL Server para o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 19c252a3-0e41-4eec-9d3e-09a68c7e7add
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/23/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: c6e6b890f5e2d0e31b10bbb6803adad02bf60248
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrar o seu código SQL ao SQL Data Warehouse
Este artigo explica as alterações de código, provavelmente terá de tomar quando migrar o seu código de outra base de dados ao SQL Data Warehouse. Algumas funcionalidades do SQL Data Warehouse podem significativamente melhorar o desempenho, que foram concebidos para trabalhar de forma distribuída. No entanto, para manter o desempenho e dimensionamento, algumas funcionalidades também não estão disponíveis.

## <a name="common-t-sql-limitations"></a>Limitações de T-SQL comuns
A lista seguinte resume as funcionalidades mais comuns que não suporta o SQL Data Warehouse. As ligações remetem o utilizador soluções para as funcionalidades não suportadas:

* [Associações de ANSI nas atualizações][ANSI joins on updates]
* [Associações de ANSI em eliminações][ANSI joins on deletes]
* [instrução Merge][merge statement]
* associações entre bases de dados
* [cursores][cursors]
* [INSERT... EXEC][INSERT..EXEC]
* cláusula output
* funções inline de definidas pelo utilizador
* funções com múltiplas instruções
* [expressões de tabela comuns](#Common-table-expressions)
* [recursiva expressões de tabela comuns (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funções CLR e procedimentos
* função $partition
* variáveis de tabela
* parâmetros de valor de tabela
* transações distribuídas
* consolidação / reversão de trabalho
* guardar a transação
* contextos de execução (EXECUTE AS)
* [Agrupar por cláusula com rollup / cubo / opções de conjuntos de agrupamento][group by clause with rollup / cube / grouping sets options]
* [níveis de aninhamento além do 8][nesting levels beyond 8]
* [Atualizar nas vistas][updating through views]
* [utilização de Selecione para atribuição de variáveis][use of select for variable assignment]
* [sem dados máx. Escreva dinâmica cadeias de SQL][no MAX data type for dynamic SQL strings]

Felizmente a maioria destas limitações pode ser trabalhada à volta. Explicações são fornecidas nos artigos de desenvolvimento relevantes referenciados acima.

## <a name="supported-cte-features"></a>Funcionalidades CTE suportadas
As expressões de tabela comuns (ctes recursivos) parcialmente são suportadas no SQL Data Warehouse.  As seguintes funcionalidades CTE atualmente são suportadas:

* Pode ser especificado um CTE numa instrução SELECT.
* Pode ser especificado um CTE numa instrução CREATE VIEW.
* Pode ser especificado um CTE numa instrução criar tabela AS SELECIONE (CTAS).
* Pode ser especificado um CTE numa instrução criar remoto tabela AS SELECIONE (CRTAS).
* Pode ser especificado um CTE numa instrução criar externo tabela AS SELECIONE (CETAS).
* Uma tabela remota pode ser referenciada a partir de um CTE.
* Uma tabela externa pode ser referenciada a partir de um CTE.
* Várias definições de consulta CTE podem ser definidas num CTE.

## <a name="cte-limitations"></a>Limitações de CTE
Expressões de tabela comuns tem algumas limitações no SQL Data Warehouse, incluindo:

* Um CTE tem de ser seguido de uma única instrução SELECT. INSERT, UPDATE, DELETE, e instruções de intercalação não são suportadas.
* Não é suportada uma expressão de tabela comum, que inclui as referências a próprio (uma recursiva expressão de tabela comum) (consulte abaixo secção).
* Não é permitido especificar mais do que uma com a cláusula num CTE. Por exemplo, se um CTE_query_definition contiver uma subconsulta, esse subconsulta não pode conter um aninhada com uma cláusula que define CTE outro.
* Uma cláusula ORDER BY não é possível utilizar CTE_query_definition, exceto se for especificada uma cláusula superior.
* Quando um CTE é utilizado numa instrução que faz parte de um lote, a instrução antes de tem de ser seguida por ponto e vírgula.
* Quando utilizados em declarações preparadas sp_prepare, ctes recursivos irão comportar-se da mesma forma que outras instruções SELECT no PDW. No entanto, se ctes recursivos são utilizados como parte da CETAS preparada sp_prepare, o comportamento pode diferir do SQL Server e outras instruções PDW devido a forma de enlace é implementado para sp_prepare. Se SELECIONAR de que as referências CTE está a utilizar uma coluna errada, que não existe no CTE, o sp_prepare irá passar sem detetar o erro, mas o erro será emitido durante a sp_execute em vez disso.

## <a name="recursive-ctes"></a>Ctes recursivos de recursiva
Ctes recursivos de recursivas não são suportados no SQL Data Warehouse.  A migração de recursiva CTE pode ser um pouco complexa e é o processo de melhor para interromper vários passos. Normalmente, pode utilizar um ciclo e preencher uma tabela temporária como iterar as consultas provisória recursiva. Depois da tabela temporária é preenchida, em seguida, pode regressar os dados como um conjunto de resultado único. Foi utilizada uma abordagem semelhante para resolver `GROUP BY WITH CUBE` no [Agrupar por cláusula com rollup / cubo / opções de conjuntos de agrupamento] [ group by clause with rollup / cube / grouping sets options] artigo.

## <a name="unsupported-system-functions"></a>Funções de sistema não suportado
Existem também algumas funções de sistema que não são suportadas. Algumas das principais as que poderá achar normalmente utilizadas no armazém de dados são:

* NEWSEQUENTIALID)
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Alguns destes problemas podem ser trabalhado à volta.

## <a name="rowcount-workaround"></a>@@ROWCOUNT solução
Para contornar a falta de suporte para @@ROWCOUNT, crie um procedimento armazenado que irá obter o último número de linhas de sys.dm_pdw_request_steps e, em seguida, executar `EXEC LastRowCount` depois de uma instrução DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista completa de todas as declarações de T-SQL suportadas, consulte [tópicos de Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
