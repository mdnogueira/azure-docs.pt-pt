---
title: "Nova configuração de Base de Dados SQL com o PowerShell | Microsoft Docs"
description: "Saiba como criar uma base de dados SQL com o PowerShell. As tarefas comuns de configuração de base de dados podem ser geridas através de cmdlets do PowerShell."
keywords: "criar nova base de dados sql, configuração da base de dados"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: daf63815baaf0843f56668b8f310642dbaaf0e54


---
# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Criar uma Base de Dados SQL e executar tarefas comuns de configuração de base de dados com os cmdlets do PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-get-started.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> * [C#](sql-database-get-started-csharp.md)
> 
> 

Saiba como criar uma base de dados SQL com os cmdlets do PowerShell. (Para criar bases de dados elásticas, consulte [Criar um novo conjunto de bases de dados elásticas com o PowerShell](sql-database-elastic-pool-create-powershell.md).)

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Configuração de base de dados: criar um grupo de recursos, o servidor e a regra de firewall
Assim que tiver acesso para executar os cmdlets na subscrição do Azure selecionada, o passo seguinte consiste em estabelecer o grupo de recursos que contém o servidor onde a base de dados será criada. Pode editar o comando seguinte para utilizar qualquer localização válida que escolher. Execute **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** para obter uma lista de localizações válidas.

Execute o seguinte comando para criar um grupo de recursos:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Criar um servidor
As Bases de Dados SQL são criadas dentro de servidores de Base de Dados SQL do Azure. Execute o [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) para criar um servidor. O nome para o servidor tem de ser exclusivo para todos os servidores da Base de Dados SQL do Azure. Irá obter um erro se o nome do servidor já estiver atribuído. Também vale a pena realçar que este comando pode demorar vários minutos a concluir. Pode editar o comando para utilizar qualquer localização válida que escolher, mas deve utilizar a mesma localização que utilizou para o grupo de recursos criado no passo anterior.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Quando executar este comando, ser-lhe-á pedido o seu nome de utilizador e palavra-passe. Não introduza as suas credenciais do Azure. Em vez disso, introduza o nome de utilizador e a palavra-passe para criar como administrador do servidor. O script na parte inferior deste artigo mostra como pode definir as credenciais do servidor em código.

Os detalhes do servidor são apresentados depois de o servidor ser criado com êxito.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Configurar uma regra de firewall de servidor para permitir o acesso ao servidor
Para aceder ao servidor, tem de estabelecer uma regra de firewall. Execute o comando [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) e substitua os endereços IP de início e de fim pelos valores válidos para o seu computador.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes da regra de firewall são apresentados depois de a regra ser criada com êxito.

Para permitir que outros serviços do Azure acedam ao servidor, adicione uma regra de firewall e defina StartIpAddress e EndIpAddress como 0.0.0.0. Esta regra permite que o tráfego do Azure de qualquer subscrição do Azure aceda ao servidor.

Para mais informações, consulte [Firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md).

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL
Tem agora um grupo de recursos, um servidor e uma regra de firewall configurados para que possa aceder ao servidor.

O seguinte comando [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) cria uma base de dados SQL (em branco) na camada de serviço Standard com um nível de desempenho S1:

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes da base de dados são apresentados depois de a base de dados ser criada com êxito.

## <a name="create-a-sql-database-powershell-script"></a>Criar um script do PowerShell de base de dados SQL
O seguinte script do PowerShell cria uma base de dados SQL e todos os recursos dependentes. Substitua todos os `{variables}` com valores específicos da sua subscrição e recursos (remova **{}** quando definir os valores).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation

    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"

    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds

    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip

    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp


    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"

    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo


    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Passos seguintes
Depois de criar uma base de dados SQL e efetuar tarefas básicas de configuração de base de dados, está pronto para o seguinte:

* [Gerir a Base de Dados SQL com o PowerShell](sql-database-manage-powershell.md)
* [Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Recursos Adicionais
* [Cmdlets da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Base de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Dec16_HO1-->


