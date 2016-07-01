<properties
    pageTitle="Tutorial de Base de Dados SQL: Introdução à Segurança"
    description="Saiba como criar contas de utilizador para aceder e gerir uma base de dados."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="carlrab"/>

# Tutorial de Base de Dados SQL: criar contas de utilizador de base de dados SQL para aceder e gerir uma base de dados com o portal do Azure

Neste tutorial, irá aprender a utilizar o portal do Azure para:

- Iniciar sessão na Base de Dados SQL ao utilizar um início de sessão principal ao nível do servidor
- Criar uma conta de utilizador de Base de Dados SQL
- Conceder permissões de dbo de conta de utilizador de Base de Dados SQL dentro de uma base de dados de utilizador
- Ligar a uma Base de Dados SQL com uma conta de utilizador que não é principal ao nível do servidor 

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Passos seguintes
Agora que já concluiu este tutorial de Base de Dados SQL e criou uma conta de utilizador, e concedeu ao utilizador permissões de dbo de conta de utilizador, está pronto para saber mais sobre a [Segurança da Base de Dados SQL](sql-database-manage-logins.md).





<!--HONumber=Jun16_HO2-->


