---
title: "Recursos para desenvolver um armazém de dados no Azure | Microsoft Docs"
description: "Conceitos de desenvolvimento, as decisões de conceção, recomendações e técnicas de programação para o SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: b85a4f09e561e429aa5bf46ec680014487fb40c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>As decisões de conceção e técnicas de programação para o SQL Data Warehouse
Veja através destes artigos de desenvolvimento para compreender melhor as decisões de conceção importantes, recomendações e técnicas de programação para o SQL Data Warehouse.

## <a name="key-design-decisions"></a>Decisões de conceção de chave
Os artigos seguintes realçam alguns dos conceitos chave e as decisões de conceção, terá de compreender para o desenvolvimento do seu armazém de dados distribuídos através do armazém de dados do SQL Server:

* [ligações][connections]
* [simultaneidade][concurrency]
* [transações][transactions]
* [esquemas definido pelo utilizador][user-defined schemas]
* [distribuição de tabela][table distribution]
* [índices da tabela][table indexes]
* [partições da tabela][table partitions]
* [CTAS][CTAS]
* [estatísticas][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações de desenvolvimento e técnicas de programação
Estes artigos realce técnicas de programação específicas, sugestões e as recomendações para desenvolver o seu armazém de dados do SQL Server:

* [procedimentos armazenados][stored procedures]
* [etiquetas][labels]
* [vistas][views]
* [tabelas temporárias][temporary tables]
* [SQL dinâmico][dynamic SQL]
* [ciclo][looping]
* [Agrupar por opções][group by options]
* [atribuição de variáveis][variable assignment]

## <a name="next-steps"></a>Passos seguintes
Depois de ter sido através dos artigos de desenvolvimento veja através de [referência de Transact-SQL] [ Transact-SQL reference] página para obter mais detalhes sobre a sintaxe suportado para o SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[concurrency]: ./sql-data-warehouse-develop-concurrency.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md
[Transact-SQL reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
