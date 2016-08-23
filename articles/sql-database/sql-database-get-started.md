<properties
    pageTitle="Tutorial de Base de Dados SQL: criar uma base de dados SQL | Microsoft Azure"
    description="Saiba como configurar um servidor lógico de Base de Dados SQL, uma regra de firewall do servidor, a base de dados SQL, dados de exemplo, ligar-se com ferramentas de cliente, configurar utilizadores e a regra de firewall de base de dados."
    keywords="tutorial de base de dados sql, criar uma base de dados sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/05/2016"
    ms.author="carlrab"/>

# Tutorial de Base de Dados SQL: criar uma base de dados SQL em minutos com o portal do Azure

**Base de dados individual**

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Neste tutorial, irá aprender a utilizar o portal do Azure para:

- Criar um servidor lógico de Base de Dados SQL para alojar bases de dados SQL
- Crie uma base de dados SQL sem dados, com dados de exemplo ou dados de uma cópia de segurança de uma base de dados SQL.
- Crie uma regra de firewall ao nível do servidor para um único endereço IP ou para um intervalo de endereços IP.

Utilize estas ligações para efetuar estas mesmas tarefas, utilizando o [C#](sql-database-get-started-csharp.md) ou o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Passos seguintes
Agora que concluiu este tutorial de Base de Dados SQL e criou uma base de dados com alguns dados de exemplo, está pronto para explorar, utilizando as suas ferramentas favoritas.

- Se estiver familiarizado com o Transact-SQL e o SQL Server Management Studio, saiba como [Ligar e consultar uma base de dados SQL com SSMS](sql-database-connect-query-ssms.md).

- Se tem conhecimentos de Excel, saiba como [Ligar à base de dados SQL com o Excel](sql-database-connect-excel.md).

- Se estiver pronto para iniciar a codificação, escolha a linguagem de programação em [Bibliotecas de Ligação para Base de Dados SQL e SQL Server](sql-database-libraries.md)

- Se pretender mover as bases de dados SQL Server no local para o Azure, consulte [Migrar uma base de dados para a Base de Dados SQL do Azure](sql-database-cloud-migrate.md) para saber mais.

- Se pretender carregar alguns dados para uma nova tabela a partir de um ficheiro CSV com o BCP, veja [Loading data into SQL Database from a CSV file using BCP (Carregar dados para a Base de Dados SQL a partir de um ficheiro CSV com o BCP)](sql-database-load-from-csv-with-bcp.md).


## Recursos adicionais

[O que é a Base de Dados SQL?](sql-database-technical-overview.md)




<!--HONumber=Aug16_HO1-->


