---
title: "Atribuir variáveis no SQL Data Warehouse | Microsoft Docs"
description: "Sugestões para atribuição de variáveis de Transact-SQL no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 045d5148cd3f12dac63c961ccf7c953d355ed725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-variables-in-sql-data-warehouse"></a>Atribuir variáveis no SQL Data Warehouse
As variáveis no armazém de dados do SQL Server são definidas utilizando o `DECLARE` instrução ou a `SET` instrução.

Todos os seguintes são formas válidas perfeitamente para definir um valor de variável:

## <a name="setting-variables-with-declare"></a>Definir variáveis com DECLARE
A inicializar variáveis com DECLARE é uma das formas mais flexíveis para definir um valor da variável no armazém de dados do SQL Server.

```sql
DECLARE @v  int = 0
;
```

Também pode utilizar DECLARE para definir mais de uma variável de cada vez. Não é possível utilizar `SELECT` ou `UPDATE` para efetuar este procedimento:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Não é possível inicializar o e utilizar uma variável na mesma instrução DECLARE. Para ilustrar o ponto de exemplo abaixo é **não** permitido como @p1 é inicializado tanto utilizado na mesma instrução DECLARE. Este procedimento resultará num erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Valores de definição com conjunto
Conjunto é um método muito comum para definir uma única variável.

Todos os exemplos abaixo são formas válidas de definir uma variável com o conjunto de:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Só é possível definir uma variável num momento com conjunto. No entanto, como podem ser vistos acima operadores compostos são permissable.

## <a name="limitations"></a>Limitações
Não é possível utilizar SELECIONE ou a ATUALIZAÇÃO para atribuição de variáveis.

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
