---
title: "PowerShell: Criar cópias de segurança e restaurar bases de dados SQL do Azure | Microsoft Docs"
description: "Este tutorial mostra como restaurar a partir de cópias de segurança automáticas para um ponto anterior no tempo, armazenar cópias de segurança automáticas no cofre dos Serviços de Recuperação do Azure e restaurar a partir do cofre dos Serviços de Recuperação do Azure com o PowerShell"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 93efe1a08149e7c027830b03a9e426ac5a05b27b
ms.openlocfilehash: 8a3ede8af471e656e830e38e0cf2f3a909fdaadb


---


# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-powershell"></a>Tutorial: Criar cópia de segurança e restaurar uma base de dados SQL do Azure com o PowerShell

Neste tutorial, vai aprender a utilizar o Azure PowerShell para:

- Ver as cópias de segurança existentes de uma base de dados
- Restaurar uma base de dados para um ponto anterior no tempo
- Configurar a retenção de longa duração de um ficheiro de cópia de segurança de base de dados no cofre dos Serviços de Recuperação do Azure
- Restaurar uma base de dados a partir do cofre dos Serviços de Recuperação do Azure

**Estimativa de tempo**: a conclusão deste tutorial demora, aproximadamente, 30 minutos (partindo do princípio de que já cumpriu os pré-requisitos).


## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](https://azure.microsoft.com/free/) ou [Ativar as vantagens de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Tem de ligar ao Azure com uma conta que seja membro do proprietário da subscrição ou da função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

* Precisa da versão mais recente do Azure PowerShell instalada e em execução. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

* Concluiu a [Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure através do portal do Azure e do SQL Server Management Studio](sql-database-get-started.md) ou a [versão do PowerShell](sql-database-get-started-powershell.md) equivalente. Se não o tiver feito, conclua este tutorial de pré-requisitos ou execute o script do PowerShell no final da [versão do PowerShell](sql-database-get-started-powershell.md) antes de continuar.

> [!TIP]
> Pode realizar estas mesmas tarefas num tutorial de introdução com [o portal do Azure](sql-database-get-started-backup-recovery-portal.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Ver o ponto de restauro mais antigo de cópias de segurança geradas pelo serviço de uma base de dados

Nesta secção do tutorial, vê informações sobre o ponto de restauro mais antigo das [cópias de segurança automáticas geradas pelo serviço](sql-database-automated-backups.md) da base de dados. 

Pode restaurar uma base de dados para qualquer ponto no tempo entre o ponto de restauro mais antigo e a última cópia de segurança disponível (seis minutos antes da data atual). 

O fragmento seguinte utiliza o cmdlet [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) para obter o ponto de restauro mais antigo da base de dados que pretende restaurar. A hora é devolvida como UTC, mas os fragmentos seguintes mostram trabalhar na hora local. O ponto de restauro mais recente disponível de uma base de dados em direto é normalmente cerca de seis minutos atrás, pelo que o ponto de restauro mais recente é simplesmente definido para a hora atual menos seis minutos. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo

Nesta secção do tutorial irá restaure a base de dados para uma nova base de dados a partir de um ponto específico anterior no tempo. 

>[!NOTE]
>Não pode alterar o servidor para o qual está a restaurar para um ponto específico anterior no tempo. Para restaurar para um servidor diferente, utilize o [Georrestauro](sql-database-disaster-recovery.md#recover-using-geo-restore). Tenha em atenção que pode restaurar para um [conjunto de bases de dados elásticas](sql-database-elastic-jobs-overview.md) ou para um escalão de preço diferente. 

O fragmento seguinte utiliza o cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) para restaurar o $databaseToRestore que configurámos no fragmento anterior. O parâmetro **-PointInTime** necessita de um valor de hora UTC formatado semelhante a: *09/12/2016 20:00:00*. O fragmento converte a hora local para si.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configurar a retenção de longa duração das cópias de segurança automáticas num cofre dos Serviços de Recuperação do Azure 

Nesta secção do tutorial, irá [configurar um cofre dos Serviços de Recuperação do Azure para reter cópias de segurança automáticas](sql-database-long-term-retention.md) durante um período maior do que o período de retenção para a camada de serviços (até 10 anos). 


> [!TIP]
> Para eliminar cópias de segurança de retenção de longa duração, consulte [Manage long-term backup retention using PowerShell (Gerir a retenção de cópia de segurança de longa duração com o PowerShell)](sql-database-manage-long-term-backup-retention-powershell.md).


### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

> [!IMPORTANT]
> O cofre tem de estar localizado na mesma região do servidor lógico SQL do Azure e tem de utilizar o mesmo grupo de recursos do servidor lógico.

O fragmento seguinte utiliza os cmdlets [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault), e [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) para criar um novo cofre dos serviços de recuperação e definir o nível de redundância para cópias de segurança no cofre. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>Configure o servidor para utilizar o cofre de recuperação que acabou de criar para as suas cópias de segurança de retenção a longo prazo

O fragmento seguinte utiliza o cmdlet [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) para associar o cofre dos serviços de recuperação anteriormente criado com um servidor específico do SQL do Azure.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Criar uma política de retenção

Uma política de retenção é onde define o período de tempo para manter uma cópia de segurança da base de dados. 

O fragmento seguinte utiliza o cmdlet [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) para obter a política de retenção predefinida que utilizamos como modelo para criar novas políticas. Configuramos o nosso modelo para manter a nossa cópia de segurança durante dois anos e, em seguida, executamos o cmdlet [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy) para criar, por último, a nossa nova política. 

Tenha em atenção que alguns cmdlets precisam que defina o contexto do cofre antes de executar ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)) para ver este cmdlet em alguns fragmentos relacionados. Configuramos o contexto porque a política faz parte do cofre. Pode criar várias políticas de retenção para cada cofre e, em seguida, aplicar a política pretendida para bases de dados específicas. 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Configure uma base de dados para utilizar a política de retenção definida anteriormente

O fragmento seguinte utiliza o cmdlet [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) para aplicar a nossa nova política numa base de dados específica.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> Depois de configuradas, as cópias de segurança aparecem no cofre nos próximos sete dias. Continue este tutorial assim que as cópias de segurança apareçam no cofre.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>Ver informações da cópia de segurança e cópias de segurança em retenção de longa duração

Nesta secção do tutorial é possível ver informações sobre as cópias de segurança da base de dados em [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md). 

Os seguinte fragmentos consultam informações do Cofre com os cmdlets [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) e [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint).

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurar uma base de dados a partir de uma cópia de segurança em retenção de longa duração de cópia de segurança

Nesta secção do tutorial, irá restaurar a base de dados para uma nova base de dados a partir de uma cópia de segurança no cofre dos Serviços de Recuperação do Azure.

Tal como os fragmentos de ponto anterior no tempo neste tutorial, o restauro a partir de retenção de cópia de segurança de longo prazo utiliza o cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase), exceto desta vez que utiliza o parâmetro **- FromLongTermRetentionBackup** (em oposição ao parâmetro utilizado anteriormente) **- FromPointInTimeBackup**.

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>Concluir o script do Azure PowerShell para restaurar a partir de um ponto anterior no tempo, e para configurar e restaurar a partir da retenção de cópia de segurança de longa duração com o PowerShell

> [!NOTE]
> Ao tentar restaurar a cópia de segurança mais recente no final deste script, irá obter uma exceção *Não pode indexar para uma matriz nula*, se não existir nenhuma cópia de segurança no cofre.

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md)
- Para saber mais sobre o restauro de cópias de segurança, veja [restaurar a partir de cópia de segurança](sql-database-recovery-using-backups.md)



<!--HONumber=Feb17_HO3-->


