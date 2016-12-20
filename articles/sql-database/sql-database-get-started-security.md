---
title: "Tutorial de Base de Dados SQL: Introdução à Segurança"
description: Saiba como criar contas de utilizador para aceder e gerir uma base de dados.
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: e846654b659c1240451e3ac26973fbfce206936d


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Tutorial da SQL Database: criar contas de utilizador de base de dados SQL para aceder e gerir uma base de dados
> [!div class="op_single_selector"]
> * [Tutorial de introdução](sql-database-get-started-security.md)
> * [Conceder acesso](sql-database-manage-logins.md)
> 
> 

Neste tutorial, irá aprender a utilizar o SQL Server Management Studio (SSMS) para:

* Iniciar sessão na SQL Database ao utilizar um início de sessão principal ao nível do servidor.
* Criar uma conta de utilizador da SQL Database.
* Conceder [permissões db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) a um utilizador da SQL Database.
* Ligar a uma Base de Dados SQL com uma conta de utilizador que não é principal ao nível do servidor.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que já concluiu este tutorial de Base de Dados SQL e criou uma conta de utilizador, e concedeu ao utilizador permissões de dbo de conta de utilizador, está pronto para saber mais sobre a [Segurança da Base de Dados SQL](sql-database-manage-logins.md).




<!--HONumber=Dec16_HO1-->


