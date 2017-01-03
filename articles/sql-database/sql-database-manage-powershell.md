---
title: Gerir Base de Dados SQL do Azure com o PowerShell | Microsoft Docs
description: "Gestão de Base de Dados SQL do Azure com o PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: adad6b8e27e0996559d5e6dacb8dd60fbf52a631
ms.openlocfilehash: 0c1ce1c29e447d9db4ef0df7873ef89cb835abee


---
# <a name="managing-azure-sql-database-using-powershell"></a>Gerir Base de Dados SQL do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Este tópico mostra os cmdlets do PowerShell que são utilizados para executar muitas tarefas de base de dados SQL do Azure. Para obter uma lista completa, consulte [Cmdlets de Base de Dados SQL do Azure](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx).

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar uma firewall baseada no servidor, ver propriedades do servidor, estabelecer uma ligação e consultar base de dados mestra, criar uma base de dados de exemplo e uma base de dados em branco, consultar propriedades de base de dados, estabelecer uma ligação e consultar a base de dados de exemplo, consulte [Get Started Tutorial (Tutorial de Introdução)](sql-database-get-started-powershell.md).
>

## <a name="how-do-i-create-a-resource-group"></a>Como criar um grupo de recursos?
Para criar um grupo de recursos para a sua base de dados SQL e recursos relacionados do Azure, utilize o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](../powershell-azure-resource-manager.md).
Para ver o tutorial completo, consulte [Get started with Azure SQL Database servers, databases, and firewall rules by using Azure PowerShell (Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure com o Azure PowerShell)](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server"></a>Como criar um servidor de base de dados SQL?
Para criar um servidor de base de dados SQL, utilize o cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) . Substitua *server1* pelo nome do seu servidor. Os nomes de servidor têm de ser exclusivos para todos os servidores da base de dados SQL do Azure. Irá obter um erro se o nome do servidor já estiver atribuído. Este comando pode demorar vários minutos a concluir. O grupo de recursos tem de existir na sua subscrição.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Para obter mais informações sobre os servidores, consulte [Funcionalidades de Base de Dados SQL](sql-database-features.md). Para ver o tutorial completo, consulte [Get started with Azure SQL Database servers, databases, and firewall rules by using Azure PowerShell (Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure com o Azure PowerShell)](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>Como criar uma regra de firewall do servidor de base de dados SQL?
Para criar uma regra de firewall para aceder ao servidor, execute o cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). Execute o seguinte comando e substitua os endereços IP de início e de fim pelos valores válidos para o seu cliente. O grupo de recursos e servidor tem de existir na sua subscrição.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Para permitir que outros serviços do Azure acedam ao seu servidor, crie uma regra da firewall e defina `-StartIpAddress` e `-EndIpAddress` para **0.0.0.0** de ambos. Esta regra de firewall especial permite que todo o tráfego do Azure aceda ao servidor.

Para mais informações, consulte [Firewall da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para ver o tutorial completo, consulte [Get started with Azure SQL Database servers, databases, and firewall rules by using Azure PowerShell (Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure com o Azure PowerShell)](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database"></a>Como criar uma base de dados SQL?
Para criar uma base de dados SQL, utilize o cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx). O grupo de recursos e servidor tem de existir na sua subscrição. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para mais informações, consulte [What is SQL Database (O que é a Base de Dados SQL)](sql-database-technical-overview.md). Para ver o tutorial completo, consulte [Get started with Azure SQL Database servers, databases, and firewall rules by using Azure PowerShell (Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure com o Azure PowerShell)](sql-database-get-started-powershell.md).

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>Como posso alterar o nível de desempenho de uma base de dados SQL?
Para alterar o nível de desempenho, aumente ou reduza verticalmente com o cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx). O grupo de recursos, servidor e base de dados tem de existir na sua subscrição. Defina o `-RequestedServiceObjectiveName` para um único espaço (como o fragmento seguinte) para o escalão Básico. Defina-o como *S0*, *S1*, *P1*, *P6*, etc., como no exemplo anterior para outros escalões.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para mais informações, consulte [SQL Database options and performance: Understand what's available in each service tier (Opções e desempenho da Base de Dados SQL: compreender o que está disponível em casa escalão de serviço)](sql-database-service-tiers.md). Para obter um script de exemplo, consulte [Sample PowerShell script to change the service tier and performance level of your SQL database (Script do PowerShell de exemplo para alterar o nível de desempenho e o escalão de serviço da sua base de dados SQL)](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>Como copiar uma base de dados SQL no mesmo servidor?
Para copiar uma base de dados SQL no mesmo servidor, utilize o cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx). Defina o `-CopyServerName` e `-CopyResourceGroupName` para os mesmos valores que o grupo de recursos e o servidor da base de dados de origem.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Para mais informações, consulte [Copiar uma Base de Dados SQL do Azure](sql-database-copy.md). Para obter um script de exemplo, consulte [Copiar um script do PowerShell da base de dados SQL](sql-database-copy-powershell.md#example-powershell-script).

## <a name="how-do-i-delete-a-sql-database"></a>Como eliminar uma base de dados SQL?
Para eliminar uma base de dados SQL. Utilize o cmdlet [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx). O grupo de recursos, servidor e base de dados tem de existir na sua subscrição.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>Como eliminar um servidor da base de dados SQL?
Para eliminar um servidor de base de dados SQL, utilize o cmdlet [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx) .

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>Como criar e gerir conjuntos elásticos com o PowerShell?
Para obter detalhes sobre como criar conjuntos elásticos através do PowerShell, consulte [Create a new elastic pool with PowerShell (Criar um novo conjunto elástico com o PowerShell)](sql-database-elastic-pool-create-powershell.md).

Para obter detalhes sobre como gerir conjuntos elásticos através do PowerShell, consulte [Monitor and manage an elastic pool with PowerShell (Monitorizar e gerir um conjunto elástico com o PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="related-information"></a>Informações relacionadas
* [Cmdlets da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Referência de Cmdlet do Azure](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Dec16_HO5-->


