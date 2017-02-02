---
title: 'T-SQL: Regras de firewall da Base de Dados SQL do Azure | Microsoft Docs'
description: "Saiba como configurar regras de firewall ao nível do servidor e ao nível da base de dados para endereços IP que acedem a bases de dados SQL do Azure através do Transact-SQL."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2f24c1c43300a3c5035fbb4e90c3d13f73c1bc74
ms.openlocfilehash: ef51f63984d342422ee5f2a93acfee6357df57ca


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configure Azure SQL Database server-level and database-level firewall rules using T-SQL (Configurar regras de firewall ao nível da base de dados e do servidor da Base de Dados SQL do Azure com o T-SQL)
> [!div class="op_single_selector"]
> * [Descrição geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

A Base de dados SQL do Microsoft Azure utiliza regras de firewall para permitir ligações para os servidores e bases de dados. Pode alterar as definições da firewall ao nível do servidor e ao nível da base de dados para a base de dados mestra ou do utilizador no seu servidor de Base de Dados SQL do Azure para seletivamente permitir o acesso à base de dados.

> [!IMPORTANT]
> Para permitir que as aplicações do Azure se liguem ao seu servidor de base de dados, as ligações do Azure têm de estar ativadas. Para obter mais informações sobre regras de firewall e como ativar ligações a partir do Azure, consulte [Azure SQL Database Firewall (Firewall de Base de Dados SQL do Azure)](sql-database-firewall-configure.md). Se estiver a efetuar ligações dentro do limite de cloud do Azure, poderá ter de abrir algumas das portas TCP adicionais. Para obter mais informações, consulte a secção **V12 of SQL Database: Outside vs inside (V12 da Base de Dados SQL: exterior vs interior)** de [Ports beyond 1433 for ADO.NET 4.5 and SQL Database V12 (Portas além de 1433 para ADO.NET 4.5 e Base de Dados SQL V12)](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>Regras de firewall ao nível do servidor
Apenas o início de sessão principal ao nível do servidor ou o administrador do Azure Active Directory pode criar uma regra de firewall ao nível do servidor através do Transact-SQL.

1. Inicie uma janela de consulta e ligue à base de dados mestra virtual com o SQL Server Management Studio.
2. As regras de firewall ao nível do servidor podem ser selecionadas, criadas, atualizadas ou eliminadas de dentro da janela de consulta.
3. Para criar ou atualizar as regras de firewall ao nível do servidor, execute o procedimento armazenado `sp_set_firewall_rule`. O exemplo seguinte ativa um intervalo de endereços IP no servidor do Contoso.<br/>Comece por verificar as regras que já existem.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Em seguida, adicione uma regra de firewall.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Para eliminar uma regra de firewall ao nível do servidor, execute o procedimento armazenado sp_delete_firewall_rule. O exemplo seguinte elimina a regra com o nome ContosoFirewallRule.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Para obter mais informações sobre estes procedimentos armazenados, consulte [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Regras de firewall ao nível da base de dados
Apenas um utilizador de base de dados com a permissão **CONTROLO** na base de dados (por exemplo, o proprietário da base de dados) pode criar uma regra de firewall ao nível da base de dados.

1. Depois de criar uma firewall ao nível do servidor para o seu endereço IP, inicie uma janela de consulta através do Portal clássico ou do SQL Server Management Studio.
2. Ligue à base de dados em que pretende criar uma regra de firewall ao nível da base de dados.
   
    Para criar uma nova uma regra de firewall ao nível da base de dados existente, ou atualizá-la, execute o procedimento armazenado `sp_set_database_firewall_rule`. O exemplo seguinte cria uma nova regra de firewall com o nome ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Para eliminar uma regra de firewall ao nível da base de dados existente, ou atualizá-la, execute o procedimento armazenado `sp_delete_database_firewall_rule`. O exemplo seguinte elimina a regra com o nome ContosoFirewallRule.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para obter mais informações sobre estes procedimentos armazenados, consulte [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

> [!NOTE]
> Para obter um tutorial que demonstre a utilização de firewalls ao nível da base de dados, veja [SQL Database tutorial: SQL Server authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules (Tutorial da Base de Dados SQL: autenticação do SQL Server, inícios de sessão e contas de utilizador, funções de base de dados, permissões, regras de firewall ao nível do servidor e regras de firewall ao nível da base de dados)](sql-database-control-access-sql-authentication-get-started.md).
>


## <a name="next-steps"></a>Passos seguintes
Para ler artigos explicativos sobre como criar regras de firewall ao nível do servidor com outros métodos, consulte: 

* [Configure Azure SQL Database server-level firewall rules using the Azure portal (Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o Portal do Azure)](sql-database-configure-firewall-settings.md)
* [Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configure Azure SQL Database server-level firewall rules using the REST API (Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com a API REST)](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar uma base de dados, consulte [Create a SQL database in minutes using the Azure portal (Criar uma Base de Dados SQL em poucos minutos com o Portal do Azure)](sql-database-get-started.md).
Para obter ajuda para ligar a uma base de dados SQL do Azure a partir de aplicações de código aberto ou de terceiros, veja [Client quick-start code samples to SQL Database (Exemplos de código de início rápido de cliente para a Base de Dados SQL)](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para as bases de dados, veja [Manage database access and login security (Gerir acesso da base de dados e segurança do início de sessão)](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Proteger a sua base de dados](sql-database-security-overview.md)
* [Centro de Segurança do Motor da Base de Dados do SQL Server e da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Jan17_HO3-->


