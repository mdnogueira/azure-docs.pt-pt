---
title: Utilize etiquetas para consultas de instrumento no SQL Data Warehouse | Microsoft Docs
description: "Sugestões para utilizar etiquetas para consultas de instrumento no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Utilize etiquetas para consultas de instrumento no SQL Data Warehouse
Armazém de dados do SQL Server suporta um conceito chamado etiquetas de consulta. Antes de avançar para qualquer profundidade vamos observe um exemplo de uma:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Esta última linha de etiquetas a cadeia 'Os meus de consulta de etiqueta' para a consulta. Isto é particularmente útil, dado que a etiqueta é consulta possível através de DMVs. Isto permite-nos com um mecanismo para identificar as consultas de problema e também para ajudar a identificar progresso através de uma execução ETL.

Uma convenção de nomenclatura boa realmente ajuda-o aqui. Por exemplo como ' PROJETO: procedimento: declaração: Comentário ' iria ajudar a identificar exclusivamente a consulta entre todos os o código de controlo de origem.

Para procurar por etiqueta pode utilizar a seguinte consulta que utiliza as vistas de gestão dinâmica:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial moldar Parênteses Retos ou aspas à volta a etiqueta do word resultante da consulta. Etiqueta é uma palavra reservada e causou um erro se não tiver sido delimitado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
