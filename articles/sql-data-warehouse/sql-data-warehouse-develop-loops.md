---
title: Tirar partido dos ciclos de T-SQL no Azure SQL Data Warehouse | Microsoft Docs
description: "Sugestões para ciclos de Transact-SQL e cursores substituindo no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 40a872ff310f48bfd543ac184fe7301b85b50258
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="loops-in-sql-data-warehouse"></a>Ciclos no SQL Data Warehouse
Armazém de dados do SQL Server suporta o [enquanto][enquanto] ciclo para executar repetidamente blocos de instrução. Isto irá continuar para, desde que as condições especificadas são verdadeiras ou até que o código termina especificamente o ciclo utilizando o `BREAK` palavra-chave. Ciclos são particularmente úteis para substituir os cursores definidos no código do SQL Server. Felizmente, quase todos os cursores que são escritos no código do SQL Server são de reencaminhamento rápido, de leitura apenas variedade. Por conseguinte [enquanto] ciclos são uma excelente alternativa caso se ter que substituir um.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Tirar partido dos ciclos e de substituir os cursores no SQL Data Warehouse
No entanto, antes de diving no cabeçalho pela primeira vez deve faça a mesmo à seguinte pergunta: "Foi deste cursor ser novamente escrito para utilizar operações de conjunto com base?". Em muitos casos, a resposta será Sim e é, muitas vezes, a abordagem das melhores. Muitas vezes, uma operação de definição com base efetua significativamente mais rapidamente do que uma abordagem iterativa, linha por linha.

Cursores só de leitura rápida reencaminhar podem ser facilmente substituídos com uma construção de ciclo. Segue-se um exemplo simples. Este exemplo de código atualiza as estatísticas de cada tabela na base de dados. Por iterating através de tabelas do ciclo, mas é possível executar cada comando na sequência.

Em primeiro lugar, crie uma tabela temporária contendo um número de linha exclusivo utilizado para identificar as declarações de individuais:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Segundo, inicializar as variáveis necessárias para executar o ciclo:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora cíclicas através de instruções executar um cada vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Por fim, remover a tabela temporária criada no primeiro passo

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[enquanto]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
