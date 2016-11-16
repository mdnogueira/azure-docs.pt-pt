---
title: "Ligar à Base de Dados SQL - SQL Server Management Studio | Microsoft Docs"
description: "Saiba como ligar à Base de Dados SQL no Azure, utilizando o SQL Server Management Studio (SSMS). Em seguida, execute uma consulta de exemplo com Transact-SQL (T-SQL)."
metacanonical: 
keywords: "ligar à base de dados sql, sql server management studio"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0eb25eb76c6c6c2446ac0b2b07c65975c3719db0


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-tsql-query"></a>Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Este artigo mostra como ligar a uma Base de Dados SQL do Azure, utilizando o SQL Server Management Studio (SSMS). Depois de se ligar com êxito, é executada uma consulta de Transact-SQL (T-SQL) simples para verificar a comunicação com a base de dados.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[!INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

## <a name="run-sample-queries"></a>Executar consultas de exemplo
Depois de se ligar ao seu servidor, pode ligar-se a uma base de dados e executar uma consulta de exemplo. Se for a primeira vez que escreve consultas, consulte o artigo [Writing Transact-SQL Statements (Escrever Declarações de Transact-SQL)](https://msdn.microsoft.com/library/ms365303.aspx).

1. No **Explorador de Objetos**, navegue para uma base de dados no servidor, como a base de dados de exemplo **AdventureWorks**.
2. Clique com o botão direito do rato na base de dados e, em seguida, selecione **Nova Consulta**:
   
    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)
3. Na janela da consulta, copie e cole o seguinte:
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
4. Clique no botão **Executar**:
   
    ![Êxito. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## <a name="next-steps"></a>Passos seguintes
Pode utilizar instruções T-SQL para criar e gerir bases de dados no Azure de forma semelhante à do SQL Server. Se estiver familiarizado com a utilização de T-SQL com o SQL Server, consulte [informações Transact-SQL da Base de Dados SQL do Azure)](sql-database-transact-sql-information.md) para um resumo das diferenças.

Se estiver familiarizado com T-SQL, consulte [Tutorial: Escrever Instruções Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e [Referência de Transact-SQL (Motor de Base de Dados)](https://msdn.microsoft.com/library/bb510741.aspx).

Para começar a criar utilizadores de base de dados e administradores utilizadores de base de dados, consulte [Introdução à segurança da Base de Dados SQL do Azure](sql-database-get-started-security.md)

Para mais informações sobre o SSMS, consulte o artigo [Use SQL Server Management Studio (Utilizar o SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Nov16_HO2-->


