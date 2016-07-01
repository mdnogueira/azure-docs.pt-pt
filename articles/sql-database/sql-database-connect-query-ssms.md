<properties
    pageTitle="Ligar à Base de Dados SQL - SQL Server Management Studio | Microsoft Azure"
    description="Saiba como ligar à Base de Dados SQL no Azure, utilizando o SQL Server Management Studio (SSMS). Em seguida, execute uma consulta de exemplo com Transact-SQL (T-SQL)."
    metaCanonical=""
    keywords="connect to sql database,sql server management studio"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sstein;carlrab" />

# Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Este artigo mostra como ligar a uma base de dados SQL do Azure, utilizando a versão mais recente do SQL Server Management Studio (SSMS), e executar uma consulta simples com instruções Transact-SQL (T-SQL).

[AZURE.INCLUDE [Sign in](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

Para informações sobre regras de firewall, consulte [Como: Configurar Definições de Firewall (Base de Dados SQL do Azure)](sql-database-configure-firewall-settings.md).

## Executar consultas de exemplo

Depois de ligar ao seu servidor lógico, pode ligar a uma base de dados e executar uma consulta de exemplo. 

1. No **Object Explorer**, navegue para uma base de dados no servidor para o qual tem permissão, como a base de dados de exemplo **AdventureWorks**.
2. Clique com o botão direito do rato na base de dados e, em seguida, selecione **Nova Consulta**.

    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Na janela da consulta, copie e cole o seguinte código.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Clique no botão **Executar**.  A seguinte captura de ecrã mostra uma consulta com êxito.

    ![Êxito. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Passos seguintes

Pode utilizar instruções T-SQL para criar e gerir bases de dados no Azure de forma semelhante à do SQL Server. Se estiver familiarizado com a utilização de T-SQL com o SQL Server, consulte [informações Transact-SQL da Base de Dados SQL do Azure)](sql-database-transact-sql-information.md) para um resumo das diferenças.

Se estiver familiarizado com T-SQL, consulte [Tutorial: Escrever Instruções Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e [Referência de Transact-SQL (Motor de Base de Dados)](https://msdn.microsoft.com/library/bb510741.aspx).

Para começar a criar utilizadores de base de dados e administradores utilizadores de base de dados, consulte [Introdução à segurança da Base de Dados SQL do Azure](sql-database-get-started-security.md)



<!--HONumber=Jun16_HO2-->


