<properties
    pageTitle="Nova configuração de Base de Dados SQL com o PowerShell | Microsoft Azure"
    description="Saiba como criar uma nova base de dados SQL Server com o PowerShell. As tarefas comuns de configuração de base de dados podem ser geridas através de cmdlets do PowerShell."
    keywords="criar nova base de dados sql, configuração da base de dados"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Criar uma nova Base de Dados SQL Server e executar tarefas comuns de configuração de base de dados com os cmdlets do PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Saiba como criar uma nova Base de Dados SQL com os cmdlets do PowerShell. (Para criar bases de dados elásticas, consulte [Criar um novo conjunto de bases de dados elásticas com o PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## Configuração de base de dados: criar um grupo de recursos, o servidor e a regra de firewall

Assim que tiver acesso para executar os cmdlets na subscrição do Azure selecionada, o passo seguinte consiste em estabelecer o grupo de recursos que contém o servidor onde a base de dados será criada. Pode editar o comando seguinte para utilizar qualquer localização válida que escolher. Execute **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** para obter uma lista de localizações válidas.

Execute o seguinte comando para criar um novo grupo de recursos:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Depois de criar com êxito o novo grupo de recursos, as seguintes informações serão apresentadas **ProvisioningState : Succeeded**.


### Criar um servidor

As Bases de Dados SQL são criadas dentro de servidores de Base de Dados SQL do Azure. Execute **Novo AzureRmSqlServer** para criar um novo servidor. Substitua *ServerName* pelo nome do seu servidor. Este nome tem de ser exclusivo para todos os servidores da Base de Dados SQL do Azure. Irá obter um erro se o nome do servidor já estiver atribuído. Também vale a pena realçar que este comando pode demorar vários minutos a concluir. Pode editar o comando para utilizar qualquer localização válida que escolher, mas deve utilizar a mesma localização que utilizou para o grupo de recursos criado no passo anterior.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Quando executar este comando, ser-lhe-á pedido o seu nome de utilizador e palavra-passe. Não introduza as suas credenciais do Azure. Em vez disso, introduza o nome de utilizador e palavra-passe que serão as credenciais do administrador que pretende criar para o novo servidor.

Os detalhes do servidor são apresentados depois de o servidor ser criado com êxito.

### Configurar uma regra de firewall de servidor para permitir o acesso ao servidor

Estabelece uma regra de firewall para aceder ao servidor. Execute o seguinte comando e substitua os endereços IP de início e de fim pelos valores válidos para o seu computador.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes da regra de firewall são apresentados depois de a regra ser criada com êxito.

Para permitir que outros serviços do Azure acedam ao servidor, adicione uma regra de firewall e defina StartIpAddress e EndIpAddress como 0.0.0.0. Tenha em atenção que esta definição permite que o tráfego do Azure de qualquer subscrição do Azure aceda ao servidor.

Para mais informações, consulte [Firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md).


## Criar uma nova base de dados SQL

Tem agora um grupo de recursos, um servidor e uma regra de firewall configurados para que possa aceder ao servidor.

O seguinte comando cria uma base de dados SQL nova (em branco) na camada de serviço Standard com um nível de desempenho S1:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes da base de dados são apresentados depois de a base de dados ser criada com êxito.

## Criar um novo script do PowerShell de base de dados SQL

Segue-se um script do PowerShell da nova base de dados SQL:

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"

    $ServerName = "uniqueservername"

    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"

    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"


    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"

    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp

    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel

    $SqlDatabase



## Passos seguintes
Depois de criar uma nova base de dados SQL e efetuar tarefas básicas de configuração de base de dados, está pronto para o seguinte:

- [Ligar à Base de Dados SQL com o SQL Server Management Studio e executar uma consulta T-SQL de exemplo](sql-database-connect-query-ssms.md).


## Recursos Adicionais

- [Base de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Aug16_HO1-->


