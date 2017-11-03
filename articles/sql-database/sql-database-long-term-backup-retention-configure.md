---
title: "Configurar a retenção de cópias de segurança longo prazo - base de dados SQL do Azure | Microsoft Docs"
description: "Saiba como armazenar cópias de segurança automatizadas no cofre do Azure Recovery Services e para restaurar a partir do Cofre de serviços de recuperação do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: carlrab
ms.openlocfilehash: 9b218756277e52a4d582b1e8e42200f78d38580e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention"></a>Configurar e restaurar a partir de retenção de cópias de segurança de longa duração de SQL Database do Azure

Pode configurar o Cofre dos serviços de recuperação do Azure para armazenar cópias de segurança de base de dados de SQL do Azure e, em seguida, recuperar uma base de dados através de cópias de segurança retidas no cofre utilizando o portal do Azure ou o PowerShell.

## <a name="azure-portal"></a>Portal do Azure

As secções seguintes mostram como utilizar o portal do Azure para configurar o Cofre dos serviços de recuperação do Azure, veja as cópias de segurança no cofre e restauro do cofre.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Configurar o cofre, registar o servidor e selecione as bases de dados

[Configurar um cofre dos serviços de recuperação do Azure para manter cópias de segurança automatizadas](sql-database-long-term-retention.md) durante um período mais longo do que o período de retenção para o escalão de serviço. 

1. Abra o **do SQL Server** página do seu servidor.

   ![página do SQL server](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Clique em **Retenção de longa duração de cópia de segurança**.

   ![ligação de retenção de longa duração de cópia de segurança](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. No **retenção de cópias de segurança de longa duração** página do seu servidor, reveja e aceite os termos de pré-visualização (a menos que tenha o tiver feito - ou esta funcionalidade já não está em pré-visualização).

   ![aceite os termos de pré-visualização](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Para configurar a retenção de cópias de segurança de longa duração, selecione a base de dados na grelha e, em seguida, clique em **configurar** na barra de ferramentas.

   ![selecionar base de dados para retenção de longa duração de cópia de segurança](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. No **configurar** página, clique em **configurar definições necessárias** em **cofre da recuperação de serviço**.

   ![configurar ligação ao cofre](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. No **cofre dos serviços de recuperação** página, selecione um cofre existente, se aplicável. Caso contrário, se não for encontrado nenhum cofre dos serviços de recuperação para a sua subscrição, clique para sair do fluxo e criar um cofre dos serviços de recuperação.

   ![criar a ligação do Cofre](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. No **cofres dos serviços de recuperação** página, clique em **adicionar**.

   ![adicionar a ligação do Cofre](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. No **cofre dos serviços de recuperação** , indique um nome válido para o Cofre dos serviços de recuperação.

   ![nome do novo cofre](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Selecione a sua subscrição e o grupo de recursos e, em seguida, selecione a localização do cofre. Quando terminar, clique em **Guardar**.

   ![Criar Cofre](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > O cofre tem de estar localizado na mesma região do servidor lógico SQL do Azure e tem de utilizar o mesmo grupo de recursos do servidor lógico.
   >

10. Depois de criar o novo cofre, execute os passos necessários para regressar ao **cofre dos serviços de recuperação** página.

11. No **cofre dos serviços de recuperação** página, clique no cofre e, em seguida, clique em **selecione**.

   ![selecionar o cofre existente](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. No **configurar** página, forneça um nome válido para a nova política de retenção, modifique a política de retenção predefinido conforme adequado e, em seguida, clique em **OK**.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. No **retenção de cópias de segurança de longa duração** página para a base de dados, clique em **guardar** e, em seguida, clique em **OK** para aplicar a política de retenção de cópias de segurança de longa duração para todas as bases de dados.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Clique em **Guardar** para ativar a retenção de longa duração de cópia de segurança com esta nova política para o cofre dos Serviços de Recuperação do Azure que configurou.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Depois de configuradas, as cópias de segurança aparecem no cofre nos próximos sete dias. Não continue este tutorial até que as cópias de segurança apareçam no cofre.
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Cópias de segurança de vista no retenção de longo prazo através do portal do Azure

Ver informações sobre as cópias de segurança da base de dados no [retenção de cópias de segurança de longa duração](sql-database-long-term-retention.md). 

1. No portal do Azure, abra o seu Cofre de serviços de recuperação do Azure para as cópias de segurança da base de dados (aceda a **todos os recursos** e selecione-o da lista de recursos para a sua subscrição) para ver a quantidade de armazenamento utilizado pela sua cópias de segurança da base de dados no cofre.

   ![ver o cofre dos serviços de recuperação com cópias de segurança](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Abra o **base de dados SQL** página para a base de dados.

   ![nova página de base de dados de exemplo](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Na barra de ferramentas, clique em **Restaurar**.

   ![barra de ferramentas de restauro](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Na página de restauro, clique em **longo prazo**.

5. Nas cópias de segurança do cofre do Azure, clique em **Selecionar uma cópia de segurança** para ver as cópias de segurança da base de dados disponíveis na retenção de longa duração de cópia de segurança.

   ![cópias de segurança no cofre](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Restaurar uma base de dados a partir de uma cópia de segurança na retenção de cópias de segurança de longa duração no portal do Azure

Restaurar a base de dados para uma nova base de dados de uma cópia de segurança no cofre do Azure Recovery Services.

1. No **cópias de segurança do cofre do Azure** página, clique em cópia de segurança a restaurar e, em seguida, clique em **selecione**.

   ![Selecione a cópia de segurança no cofre](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Na caixa de texto **Nome da base de dados**, forneça o nome da base de dados restaurada.

   ![nome da nova base de dados](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Clique em **OK** para restaurar a base de dados a partir da cópia de segurança no cofre para a nova base de dados.

4. Na barra de ferramentas, clique no ícone de notificação para ver o estado da tarefa de restauro.

   ![progresso da tarefa de restauro a partir do cofre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Quando a tarefa de restauro está concluída, abra o **bases de dados SQL** página para ver a base de dados recentemente restaurado.

   ![restaurar base de dados a partir do cofre](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

As secções seguintes mostram como utilizar o PowerShell para configurar o Cofre dos serviços de recuperação do Azure, veja as cópias de segurança no cofre e restauro do cofre.

### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Utilize o [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) para criar um cofre dos serviços de recuperação.

> [!IMPORTANT]
> O cofre tem de estar localizado na mesma região do servidor lógico SQL do Azure e tem de utilizar o mesmo grupo de recursos do servidor lógico.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Definir o servidor para utilizar o Cofre de recuperação para as respetivas cópias de segurança de retenção de longo prazo

Utilize o [conjunto AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) cmdlet para associar um cofre dos serviços de recuperação criado anteriormente um servidor SQL do Azure específico.

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Criar uma política de retenção

Uma política de retenção é onde define o período de tempo para manter uma cópia de segurança da base de dados. Utilize o [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) cmdlet para obter a política de retenção predefinido para utilizar como o modelo para a criação de políticas. Neste modelo, o período de retenção é definido para 2 anos. Em seguida, execute o [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) para criar uma política finally. 

> [!NOTE]
> Alguns cmdlets requer que defina o contexto de cofre antes de executar ([conjunto AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)) para ver este cmdlet em alguns fragmentos relacionados. Definir o contexto porque a política faz parte do cofre. Pode criar várias políticas de retenção para cada cofre e, em seguida, aplicar a política pretendida para bases de dados específicas. 


```PowerShell
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

Utilize o [conjunto AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) cmdlet para aplicar a nova política para uma base de dados específica.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Ver informações da cópia de segurança e cópias de segurança em retenção de longa duração

Ver informações sobre as cópias de segurança da base de dados no [retenção de cópias de segurança de longa duração](sql-database-long-term-retention.md). 

Utilize os seguintes cmdlets para ver informações de cópia de segurança:

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
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

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurar uma base de dados a partir de uma cópia de segurança em retenção de longa duração de cópia de segurança

Restaurar a partir da retenção de cópias de segurança de longo prazo utiliza o [restauro-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) cmdlet.

```PowerShell
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
> Aqui, pode ligar à base de dados restaurada, utilizando o SQL Server Management Studio para efetuar tarefas de necessárias, como juntar extrair um bits de dados da base de dados restaurada para copiar para a base de dados existente ou elimine o existente da base de dados e mudar o nome de restaurada base de dados para o nome de base de dados existente. Consulte [ponto de restauro de tempo](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md)
- Para saber mais sobre o restauro de cópias de segurança, veja [restaurar a partir de cópia de segurança](sql-database-recovery-using-backups.md)
