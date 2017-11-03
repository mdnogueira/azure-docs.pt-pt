---
title: Procedimentos armazenados no SQL Data Warehouse | Microsoft Docs
description: "Sugestões para implementar os procedimentos armazenados no Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Procedimentos armazenados no SQL Data Warehouse
Armazém de dados SQL suporta muitas das funcionalidades de Transact-SQL encontradas no SQL Server. Mais importante ainda existem ampliação funcionalidades específicas que queremos de tirar partido para maximizar o desempenho da sua solução.

No entanto, para manter o dimensionamento e desempenho do armazém de dados do SQL Server existe também são algumas funções e funcionalidades que tenham diferenças comportamentais e outras pessoas que não são suportadas.

Este artigo explica como implementar os procedimentos armazenados no SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Introdução ao procedimentos armazenados
Os procedimentos armazenados são uma excelente forma ao encapsular o código do SQL Server; armazenar próximo os dados no armazém de dados. Ao encapsular o código em unidades geríveis procedimentos armazenados ajudam os programadores modularize as soluções; facilitar o início re-usability maior de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-las ainda mais flexíveis.

Armazém de dados do SQL Server fornece uma implementação simplificada e simplificada procedimento armazenado. A diferença maior relativamente ao SQL Server é que o procedimento armazenado não está código previamente compilado. Os armazéns de dados mas é geralmente menos preocupadas com o tempo de compilação. É mais importante que o código de procedimento armazenado é corretamente optimised durante o funcionamento contra volumes de dados de grandes dimensões. O objetivo é guardar horas, minutos e segundos não milissegundos. Consequentemente, é mais útil pensar de procedimentos armazenados como contentores para a lógica de SQL.     

Quando o SQL Data Warehouse executa o procedimento armazenado as instruções SQL são analisadas, traduzidas e otimizada em tempo de execução. Durante este processo cada instrução é convertida em consultas distribuídas. O código do SQL Server, na verdade, está a ser executado contra os dados é diferente para a consulta foi submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos de aninhamento armazenado
Quando os procedimentos armazenados chamar outros procedimentos armazenados ou executar sql dinâmico, em seguida, a invocação de código ou de procedimento armazenada interna possui correspondências é denominada ser aninhadas.

Armazém de dados do SQL Server suporta um máximo de 8 níveis de aninhamento. Isto é ligeiramente diferente do SQL Server. O nível de aninhamento no SQL Server é 32.

A chamada de procedimento armazenado de nível superior equivale a aninhar nível 1

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também facilita a EXEC outra chamada, em seguida, este irá aumentar o nível de aninhamento para 2

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se o segundo procedimento, em seguida, executa algumas sql dinâmico, em seguida, este irá aumentar o nível de aninhamento para 3

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Tenha em atenção o SQL Data Warehouse não suporta atualmente@NESTLEVEL. Terá de manter um um registo do seu nível de aninhamento por si. Não é provável será atingiu o limite de nível de 8 aninhamento, mas se o fizer terá da funcionar novamente o código e "aplanar", para que o se enquadra-se este limite.

## <a name="insertexecute"></a>INSERT... EXECUTAR
Armazém de dados do SQL Server não permite a consumir o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, é uma abordagem alternativa, que pode utilizar.

Consulte o seguinte artigo no [tabelas temporárias] para obter um exemplo sobre como fazê-lo.

## <a name="limitations"></a>Limitações
Existem alguns aspetos dos procedimentos armazenados de Transact-SQL que não estão implementados no armazém de dados do SQL Server.

São:

* procedimentos armazenados temporariamente
* procedimentos armazenados numerados
* procedimentos armazenados expandidos
* Procedimentos armazenado de CLR
* opção de encriptação
* opção de replicação
* parâmetros de valor de tabela
* parâmetros só de leitura
* parâmetros predefinidos
* contextos de execução
* instrução de retorno

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
