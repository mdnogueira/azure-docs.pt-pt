---
title: "Restaurar um armazém de dados SQL do Azure (PowerShell) | Microsoft Docs"
description: Tarefas do PowerShell para restaurar um Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: ac62f154-c8b0-4c33-9c42-f480808aa1d2
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: 6c17c2ff522ae5a77c15ba287f50157882d5f2c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurar um armazém de dados SQL do Azure (PowerShell)
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Neste artigo, ficará a saber como restaurar um Azure SQL Data Warehouse com o PowerShell.

## <a name="before-you-begin"></a>Antes de começar
**Certifique-se a capacidade DTU.** Cada SQL Data Warehouse for alojado por um servidor de SQL Server (por exemplo, myserver.database.windows.net) tem uma quota DTU predefinido.  Antes, pode restaurar um SQL Data Warehouse, certifique-se de que o seu SQL server tem suficiente quota de DTU restantes para a base de dados a ser restaurado. Para saber como calcular DTU necessário ou para pedir a DTU mais, consulte [pedir uma alteração de quota DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Instalar o PowerShell
Para utilizar o Azure PowerShell com o SQL Data Warehouse, terá de instalar o Azure PowerShell versão 1.0 ou superior.  Pode verificar a sua versão, executando **Get-Module - ListAvailable-Name AzureRM**.  A versão mais recente pode ser instalada na [instalador de plataforma Web Microsoft][Microsoft Web Platform Installer].  Para mais informações sobre como instalar a versão mais recente, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados do Active Directory ou em pausa
Para restaurar uma base de dados de utilização de um instantâneo de [restauro-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet do PowerShell.

1. Abra o Windows PowerShell.
2. Lista de todas as subscrições associadas à sua conta e ligar à sua conta do Azure.
3. Selecione a subscrição que contém a base de dados a ser restaurado.
4. Lista de pontos de restauro da base de dados.
5. Escolha o ponto de restauro pretendido utilizando a RestorePointCreationDate.
6. Restaure a base de dados para o ponto de restauro pretendido.
7. Certifique-se de que a base de dados restaurada está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Depois do restauro foi concluída, pode configurar a base de dados recuperada, seguindo [configurar a base de dados após a recuperação][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada
Para restaurar uma base de dados eliminada, utilize o [restauro-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet.

1. Abra o Windows PowerShell.
2. Lista de todas as subscrições associadas à sua conta e ligar à sua conta do Azure.
3. Selecione a subscrição que contém a base de dados eliminada a ser restaurado.
4. Obter a base de dados eliminada específico.
5. Restaure a base de dados eliminada.
6. Certifique-se de que a base de dados restaurada está online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Depois do restauro foi concluída, pode configurar a base de dados recuperada, seguindo [configurar a base de dados após a recuperação][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Restaurar a partir de uma região geográfica do Azure
Para recuperar uma base de dados, utilize o [restauro-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet.

1. Abra o Windows PowerShell.
2. Lista de todas as subscrições associadas à sua conta e ligar à sua conta do Azure.
3. Selecione a subscrição que contém a base de dados a ser restaurado.
4. Obter a base de dados que pretende recuperar.
5. Crie o pedido de recuperação para a base de dados.
6. Verifique o estado da base de dados restaurada a georreplicação.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar a base de dados após o restauro foi concluída, consulte [configurar a base de dados após a recuperação][Configure your database after recovery].
> 
> 

A base de dados recuperada será TDE-ativada se a base de dados de origem for TDE-ativado.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as funcionalidades de continuidade do negócio das edições do SQL Database do Azure, leia o [descrição geral da continuidade de negócio da linha de base de dados do Azure SQL][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
