<properties
    pageTitle="Criar um novo conjunto de bases de dados elásticas com o PowerShell | Microsoft Azure"
    description="Saiba como utilizar o PowerShell para aumentar horizontalmente recursos da Base de Dados SQL do Azure, criando um conjunto de bases de dados elásticas dimensionável para gerir várias bases de dados."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Criar um novo conjunto de bases de dados elásticas com o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [conjunto de bases de dados elásticas](sql-database-elastic-pool.md), utilizando cmdlets do PowerShell. 

Para códigos de erro comuns, consulte [Códigos de erro de SQL para aplicações de clientes de Base de Dados SQL: erro de ligação à base de dados e outros problemas](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Os conjuntos elásticos estão geralmente disponível (GA) em todas as regiões Azure exceto nos E.U.A. Centro-Norte e na Índia Ocidental onde se encontra, de momento, em pré-visualização.  O GA dos conjuntos elásticos nestas regiões será fornecido assim que possível. Além disso, os conjuntos elásticos não suportam atualmente as bases de dados que utilizam [OLTP dentro da memória ou análise dentro da memória](sql-database-in-memory.md).


Tem de estar a executar o Azure PowerShell 1.0 ou superior. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## Criar um novo conjunto

O cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cria um novo conjunto. Os valores de eDTU por conjunto e das Dtus mínimas e máximas estão restritos pelo valor da camada de serviço (básica, standard ou premium). Consulte [eDTU e limites de armazenamento para conjuntos elásticos e bases de dados elásticas](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Criar uma nova base de dados elástica num conjunto

Utilize o cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e defina o parâmetro **ElasticPoolName** para o conjunto de destino. Para mover uma base de dados existente para um conjunto, consulte [Mover uma base de dados para um conjunto elástico](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Criar um conjunto e preenchê-lo com várias bases de dados novas 

A criação de um grande número de bases de dados num agrupamento pode demorar a ser concluída ao utilizar o portal ou cmdlets do PowerShell que criam apenas uma base de dados de cada vez. Para automatizar a criação de um novo conjunto, consulte [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## Exemplo: criar um conjunto com o PowerShell 

Este script cria um novo grupo de recursos e um novo servidor do Azure. Quando lhe for pedido, forneça um nome de utilizador administrador e a palavra-passe para o novo servidor (não as credenciais do Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Passos seguintes

- [Gerir o conjunto](sql-database-elastic-pool-manage-powershell.md)
- [Criar tarefas elásticas](sql-database-elastic-jobs-overview.md) As tarefas elásticas permitem executar scripts T-SQL em qualquer número de bases de dados no conjunto.
- [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): utilize ferramentas de base de dados elástica para aumentar horizontalmente.




<!--HONumber=sep16_HO1-->


