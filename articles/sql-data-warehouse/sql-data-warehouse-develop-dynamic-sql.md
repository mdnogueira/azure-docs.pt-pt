---
title: "Dinâmica SQL no SQL Data Warehouse | Microsoft Docs"
description: "Sugestões de utilização do SQL Server dinâmico no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dinâmica SQL no SQL Data Warehouse
Quando desenvolver código da aplicação para o SQL Data Warehouse poderá ter de utilizar o sql dinâmica para o ajudar a fornecer soluções flexíveis, modulares e genéricas. Armazém de dados do SQL Server não suporta tipos de dados de BLOBs neste momento. Isto pode limitar o tamanho do seu cadeias como tipos de BLOBs incluem tipos varchar e nvarchar (Max). Se tiver utilizado o estes tipos no código da aplicação durante a criação de cadeias muito grande, terá de quebrar o código de segmentos e utilizar a instrução EXEC em vez disso.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia é curta pode utilizar [sp_executesql] [ sp_executesql] como normal.

> [!NOTE]
> Continuarão a estar sujeitos a todas as regras de validação de TSQL instruções executadas como SQL dinâmico.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
