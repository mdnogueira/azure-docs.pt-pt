---
title: 'PowerShell: Configurar regras de firewall da Base de Dados SQL do Azure | Microsoft Docs'
description: "Saiba como configurar regras de firewall ao nível do servidor para endereços IP que acedem a bases de dados SQL do Azure através do PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: d80bd1fbb5cdb0492e521a4d600f657fac0e3325


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Descrição geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

A Base de Dados SQL do Azure utiliza regras de firewall para permitir ligações para os servidores e bases de dados. Pode alterar as definições da firewall ao nível do servidor e ao nível da base de dados para a base de dados mestra ou uma base de dados do utilizador no seu servidor de Base de Dados SQL para seletivamente permitir o acesso à base de dados.

> [!IMPORTANT]
> Para permitir que as aplicações do Azure se liguem ao seu servidor de base de dados, as ligações do Azure têm de estar ativadas. Para obter mais informações sobre regras de firewall e como ativar ligações a partir do Azure, consulte [Azure SQL Database Firewall (Firewall de Base de Dados SQL do Azure)](sql-database-firewall-configure.md). Se estiver a efetuar ligações dentro do limite de cloud do Azure, poderá ter de abrir algumas das portas TCP adicionais. Para obter mais informações, consulte a secção "V12 of SQL Database: Outside vs inside (V12 da Base de Dados SQL: exterior vs interior)" de [Ports beyond 1433 for ADO.NET 4.5 and SQL Database V12 (Portas além de 1433 para ADO.NET 4.5 e Base de Dados SQL V12)](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Criar regras de firewall do servidor
As regras de firewall ao nível do servidor podem ser criadas, atualizadas e eliminadas com o Azure PowerShell.

Para criar uma nova regra de firewall ao nível do servidor, execute o cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). O exemplo seguinte ativa um intervalo de endereços IP no servidor do Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Para modificar uma regra existente de firewall ao nível do servidor, execute o cmdlet [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx). O exemplo seguinte altera o intervalo dos endereços IP aceitáveis para a regra com o nome ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

Para eliminar uma regra existente de firewall ao nível do servidor, execute o cmdlet [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx). O exemplo seguinte elimina a regra com o nome ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gerir regras de firewall com o PowerShell
Também pode utilizar o PowerShell para gerir regras de firewall. Para obter mais informações, consulte os seguintes tópicos:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre como utilizar o Transact-SQL para criar regras de firewall ao nível do servidor e ao nível da base de dados, consulte [Configure Azure SQL Database server-level and database-level firewall rules using T-SQL (Configurar regras de firewall ao nível do servidor e ao nível da base de dados da Base de Dados SQL do Azure com o T-SQL)](sql-database-configure-firewall-settings-tsql.md).

Para obter informações sobre como criar regras de firewall ao nível do servidor com outros métodos, consulte:

* [Configure Azure SQL Database server-level firewall rules using the Azure portal (Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o Portal do Azure)](sql-database-configure-firewall-settings.md)
* [Configure Azure SQL Database server-level firewall rules using the REST API (Configurar regras de firewall ao nível do servidor da Base de Dados SQL do Azure com a API REST)](sql-database-configure-firewall-settings-rest.md)

Para ver um tutorial sobre como criar uma base de dados, consulte [Create a SQL database in minutes using the Azure portal (Criar uma Base de Dados SQL em poucos minutos com o Portal do Azure)](sql-database-get-started.md).
Para obter ajuda para ligar a uma base de dados SQL do Azure a partir de aplicações de código aberto ou de terceiros, veja [Client quick-start code samples to SQL Database (Exemplos de código de início rápido de cliente para a Base de Dados SQL)](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para as bases de dados, veja [Manage database access and login security (Gerir acesso da base de dados e segurança do início de sessão)](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Proteger a sua base de dados](sql-database-security-overview.md)
* [Centro de Segurança do Motor da Base de Dados do SQL Server e da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!--HONumber=Jan17_HO1-->


