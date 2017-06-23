
# Descrição geral
## [O que é o Azure Backup?](backup-introduction-to-azure-backup.md)

# Introdução
## [Fazer uma cópia de segurança de ficheiros e pastas](backup-try-azure-backup-in-10-mins.md)
## [Fazer uma cópia de segurança de máquinas virtuais do Azure](backup-azure-vms-first-look.md)
## [Proteger VMs do Azure](backup-azure-vms-first-look-arm.md)

# Procedimento
## Utilizar o PowerShell
### [VMs do Azure no Portal do Azure](backup-azure-vms-automation.md)
### [VMs do Azure no portal clássico](backup-azure-vms-classic-automation.md)
### [DPM no Portal do Azure](backup-dpm-automation.md)
### [DPM no portal clássico](backup-dpm-automation-classic.md)
### [Windows Server no Portal do Azure](backup-client-automation.md)
### [Windows Server no portal clássico](backup-client-automation-classic.md)

## Servidor do Backup do Azure
### [Matriz de proteção do Azure Backup Server](backup-mabs-protection-matrix.md)
### Instalar ou atualizar
#### [Preparar as cargas de trabalho do Azure Backup Server no Portal do Azure](backup-azure-microsoft-azure-backup.md)
#### [Preparar as cargas de trabalho do Azure Backup Server no portal clássico](backup-azure-microsoft-azure-backup-classic.md)
#### [Adicionar armazenamento ao Azure Backup Server](backup-mabs-add-storage.md)
#### [Atualizar o Azure Backup Server para v.2](backup-mabs-upgrade-to-v2.md)
#### [Instalação autónoma do Azure Backup Server](backup-mabs-unattended-install.md)
### Proteger cargas de trabalho
#### [Utilizar o Azure Backup Server para fazer cópias de segurança de um servidor VMware](backup-azure-backup-server-vmware.md)
#### [Utilizar o Azure Backup Server para fazer cópias de segurança do Exchange](backup-azure-exchange-mabs.md)
#### [Utilizar o Azure Backup Server para fazer cópias de segurança de um farm do SharePoint](backup-azure-backup-sharepoint-mabs.md)
#### [Utilizar o Azure Backup Server para fazer cópias de segurança do SQL](backup-azure-sql-mabs.md)
#### [Proteger o estado do sistema e a recuperação bare-metal](backup-mabs-system-state-and-bmr.md)
### Resolução de problemas
#### [Resolução de problemas do Azure Backup Server](backup-azure-mabs-troubleshoot.md)


## Data Protection Manager
### [Preparar as cargas de trabalho do DPM no portal do Azure](backup-azure-dpm-introduction.md)
### [Preparar as cargas de trabalho do DPM no portal clássico](backup-azure-dpm-introduction-classic.md)
### [Utilizar o DPM do System Center para fazer a cópia de segurança do servidor do Exchange](backup-azure-backup-exchange-server.md)
### [Recuperar dados no cofre de Cópia de Segurança para um servidor alternativo do DPM](backup-azure-alternate-dpm-server.md)
### [Utilizar o DPM para efetuar cópias de segurança de cargas de trabalho do SQL Server](backup-azure-backup-sql.md)
### [Utilizar o DPM para fazer cópia de segurança de um farm do SharePoint](backup-azure-backup-sharepoint.md)

## VMs do Azure
### Preparar a VM
#### [Preparar as máquinas virtuais do Azure](backup-azure-vms-prepare.md)
#### [Preparar máquinas virtuais implementadas com o Resource Manager](backup-azure-arm-vms-prepare.md)
### Planear o ambiente
#### [Planear a infraestrutura de cópia de segurança da VM](backup-azure-vms-introduction.md)
### Fazer cópias de segurança de VMs
#### [Fazer cópias de segurança de máquinas virtuais do Azure para o cofre de cópias de segurança](backup-azure-vms.md)
#### [Fazer cópia de segurança de máquinas virtuais do Azure para um cofre dos Serviços de Recuperação](backup-azure-arm-vms.md)
#### [Fazer cópias de segurança de máquinas virtuais encriptadas](backup-azure-vms-encryption.md)
### Gerir e monitorizar VMs
#### [Gerir e monitorizar as cópias de segurança de VMs do Azure no portal clássico](backup-azure-manage-vms-classic.md)
#### [Gerir cópias de segurança de VMs do Azure no Portal do Azure](backup-azure-manage-vms.md)
#### [Monitorizar alertas para cópias de segurança de VMs do Azure no Portal do Azure](backup-azure-monitor-vms.md)
### Restaurar dados a partir de VMs
#### [Recuperar ficheiros a partir de cópias de segurança de VM do Azure](backup-azure-restore-files-from-vm.md)
#### [Restaurar máquinas virtuais no Azure](backup-azure-restore-vms.md)
#### [Restaurar VMs implementadas pelo Azure Resource Manager no Portal do Azure](backup-azure-arm-restore-vms.md)
#### [Restaurar a chave do Key Vault e o segredo para VMs encriptadas utilizando o Azure Backup](backup-azure-restore-key-secret.md)
#### [Restaurar máquinas virtuais encriptadas](backup-azure-vms-encryption.md)

## Base de Dados SQL do Azure
### [Configurar retenção de cópia de segurança de longa duração](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Ver cópias de segurança num cofre dos Serviços de Recuperação](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Restaurar a partir de retenção de cópia de segurança de longa duração](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Eliminar cópias de segurança SQL do Azure de longa duração](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Ficheiros e pastas do Windows
### [Windows Server com o modelo de implementação clássico](backup-configure-vault-classic.md)
### [Windows Server com o modelo de implementação do Resource Manager](backup-configure-vault.md)
### [Gerir cofres de Cópia de Segurança com o modelo de implementação clássica](backup-azure-manage-windows-server-classic.md)
### [Monitorizar e gerir cofres dos Serviços de Recuperação](backup-azure-manage-windows-server.md)
### [Recuperar ficheiros para um Windows Server com o modelo de implementação Resource Manager](backup-azure-restore-windows-server.md)
### [Recuperar ficheiros para um Windows Server com o modelo de implementação clássico](backup-azure-restore-windows-server-classic.md)

## [FAQ](backup-azure-backup-faq.md)

## Resolução de problemas
### [Problemas de cópia de segurança da VM do Azure no Portal do Azure](backup-azure-vms-troubleshoot.md)
### [Problemas de cópia de segurança da VM do Azure no portal clássico](backup-azure-vms-troubleshoot-classic.md)
### [Falha na Cópia de Segurança de VM do Azure: não foi possível comunicar com o agente da VM relativamente ao estado do instantâneo - o tempo limite da subtarefa de instantâneo da VM foi excedido](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Conceitos
## [Descrição geral dos cofres dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md)
## [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md)
## [Eliminar um cofre do Azure Backup](backup-azure-delete-vault.md)
## [Controlo de Acesso Baseado em Funções](backup-rbac-rs-vault.md)
## [Segurança para cópias de segurança híbridas](backup-azure-security-feature.md)
## [Configurar a cópia de segurança offline](backup-azure-backup-import-export.md)
## [Substituir a sua biblioteca de unidades de banda](backup-azure-backup-cloud-as-tape.md)
## [Cópias de segurança de VMs do Linux consistentes com a aplicação](backup-azure-linux-app-consistent.md)

# Referência
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/backup/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=backup)
