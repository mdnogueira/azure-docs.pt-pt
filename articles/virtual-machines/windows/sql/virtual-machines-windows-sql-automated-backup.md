---
title: "Cópia de segurança automatizada para o SQL Server 2014 Virtual Machines do Azure | Microsoft Docs"
description: "Explica a funcionalidade de cópia de segurança automatizada para as VMs de 2014 do SQL Server em execução no Azure. Este artigo é específico para VMs com o Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 91aab896dd5f06c950ee0ed8f36cc6a953d91611
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Cópia de segurança automatizada para máquinas de virtuais do SQL Server 2014 (Gestor de recursos)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Configura automaticamente a cópia de segurança automatizada [cópia de segurança gerida no Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os existentes e novas bases de dados numa VM do Azure a executar o SQL Server 2014 Standard ou Enterprise. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. Cópia de segurança automatizada depende o [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada, considere os seguintes pré-requisitos:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatizada funciona de cópia de segurança com o SQL Server 2014. Se estiver a utilizar o SQL Server 2016, pode utilizar v2 de cópia de segurança automatizada para fazer cópias de segurança das bases de dados. Para obter mais informações, consulte [v2 de cópia de segurança automatizada para o SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuração de base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completa em cópias de segurança, consulte [cópia de segurança nos completo modelo de recuperação](https://technet.microsoft.com/library/ms190217.aspx).
- Tem de estar bases de dados de destino na instância predefinida do SQL Server. A extensão de IaaS do SQL Server não suporta instâncias nomeadas.

**Modelo de implementação do Azure**:

- Resource Manager

**O Azure PowerShell**:

- [Instalar os comandos do Azure PowerShell mais recentes](/powershell/azure/overview) se planear configurar cópia de segurança automatizada com o PowerShell.

> [!NOTE]
> Cópia de segurança automatizada baseia-se na extensão de agente do SQL Server IaaS. Imagens de galeria da máquina virtual SQL atuais adicionar esta extensão, por predefinição. Para obter mais informações, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada. Os passos de configuração reais variam dependendo se utilizar o portal do Azure ou os comandos do Azure Windows PowerShell.

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/desativar (desativada) | Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure a executar o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias a manter uma cópia de segurança. |
| **Conta de armazenamento** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no blob storage. Um contentor é criado nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e nome da máquina. |
| **Encriptação** | Ativar/desativar (desativada) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada na mesma `automaticbackup` contentor utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** | Texto da palavra-passe | Uma palavra-passe para as chaves de encriptação. Isto apenas é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança foi efetuada. |

## <a name="configuration-in-the-portal"></a>Configuração no Portal do

Pode utilizar o portal do Azure para configurar a cópia de segurança automatizada durante o aprovisionamento de ou para VMs de 2014 existente do SQL Server.

### <a name="new-vms"></a>Novas VMs

Utilize o portal do Azure para configurar a cópia de segurança automatizada quando cria um novo SQL Server 2014 máquina no modelo de implementação Resource Manager.

No **definições do SQL Server** painel, selecione **cópia de segurança automatizada**. A seguinte captura de ecrã de portal do Azure mostra o **cópia de segurança do SQL Server automatizada** painel.

![Configuração de cópia de segurança do SQL Server automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Para o contexto, consulte o tópico completo sobre [aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

Para máquinas virtuais do SQL Server existentes, selecione a máquina virtual do SQL Server. Em seguida, selecione o **configuração do SQL Server** secção o **definições** painel.

![SQL Server cópia de segurança automatizada para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

No **configuração do SQL Server** painel, clique em de **editar** botão na secção de cópia de segurança automatizada.

![Configurar a cópia de segurança do SQL Server automatizada para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Quando terminar, clique em de **OK** botão na parte inferior do **configuração do SQL Server** painel para guardar as alterações.

Se pretende ativar a cópia de segurança automatizada pela primeira vez, o Azure configura o agente do SQL Server IaaS em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a cópia de segurança automatizada está configurada. Aguarde alguns minutos para que o agente ser instalado, configurado. Depois de que o portal do Azure irá refletir as novas definições.

> [!NOTE]
> Também pode configurar a cópia de segurança automatizada através de um modelo. Para obter mais informações, consulte [modelo de início rápido do Azure para cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell

Pode utilizar o PowerShell para configurar a cópia de segurança automatizada. Antes de começar, tem de:

- [Transfira e instale o Azure PowerShell mais recente](http://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associá-lo com a sua conta. Pode fazê-lo ao seguir os passos a [configurar a subscrição](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) secção do tópico aprovisionamento.

### <a name="install-the-sql-iaas-extension"></a>Instale a extensão de IaaS do SQL Server
Se aprovisionar uma máquina virtual do SQL Server a partir do portal do Azure, a extensão de IaaS do SQL Server já deve estar instalada. Pode determinar se este é instalado para a VM ao chamar **Get-AzureRmVM** comando e examinar o **extensões** propriedade.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão de agente do IaaS do SQL Server estiver instalada, deve ver, listado como "SqlIaaSAgent" ou "SQLIaaSExtension". **ProvisioningState** para a extensão também deve mostrar "Com êxito".

Se não está instalado ou falha no aprovisionamento, pode instalá-lo com o seguinte comando. Para além do grupo de recursos e nome VM, também tem de especificar a região (**$region**) que a VM está localizada em.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

### <a id="verifysettings"></a>Verifique as definições atuais

Se ativou a cópia de segurança automatizada durante o aprovisionamento, pode utilizar o PowerShell para verificar a configuração atual. Execute o **Get-AzureRmVMSqlServerExtension** de comandos e examine a **AutoBackupSettings** propriedade:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Deve obter resultado semelhante ao seguinte:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Se o resultado mostra que **ativar** está definido como **falso**, terá de ativar a cópia de segurança automatizada. Boas notícias são que pode ativar e configurar cópia de segurança automatizada da mesma forma. Consulte a secção seguinte para obter estas informações.

> [!NOTE] 
> Se verifique as definições de imediatamente depois de efetuar uma alteração, é possível que irá obter novamente os valores de configuração antigo. Aguarde alguns minutos e verifique as definições novamente para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar a cópia de segurança automatizada
Pode utilizar o PowerShell para ativar a cópia de segurança automatizada, bem como para modificar a configuração e o comportamento em qualquer altura.

Em primeiro lugar, selecione ou crie uma conta de armazenamento para os ficheiros de cópia de segurança. O script seguinte seleciona uma conta de armazenamento ou criá-lo se não existir.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Cópia de segurança automatizada não suporta cópias de segurança armazenar no armazenamento premium, mas pode demorar cópias de segurança de discos VM que utilizam o armazenamento Premium.

Em seguida, utilize o **New-AzureRmVMSqlServerAutoBackupConfig** comando para ativar e configurar as definições de cópia de segurança automatizada para armazenar as cópias de segurança na conta do storage do Azure. Neste exemplo, as cópias de segurança são definidas para ser mantidas durante 10 dias. O segundo comando, **conjunto AzureRmVMSqlServerExtension**, atualiza a VM do Azure especificada com estas definições.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode demorar alguns minutos para instalar e configurar o agente do SQL Server IaaS.

> [!NOTE]
> Existem outras definições para **New-AzureRmVMSqlServerAutoBackupConfig** que se apliquem apenas a SQL Server 2016 e cópia de segurança automatizada v2. SQL Server 2014 não suporta as seguintes definições: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, e **LogBackupFrequencyInMinutes**. Se tentar configurar estas definições numa máquina virtual do SQL Server 2014, não há nenhum erro, mas não ser aplicadas as definições. Se pretender utilizar estas definições numa máquina virtual do SQL Server 2016, consulte [v2 de cópia de segurança automatizada para o SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Para ativar a encriptação, modificar o script anterior para passar o **EnableEncryption** parâmetro juntamente com uma palavra-passe (cadeia segura) para o **CertificatePassword** parâmetro. O script seguinte Ativa as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar as definições são aplicadas, [verificar a configuração de cópia de segurança automatizada](#verifysettings).

### <a name="disable-automated-backup"></a>Desativar a cópia de segurança automatizada

Para desativar a cópia de segurança automatizada, execute o script mesmo sem a **-ativar** parâmetro para o **New-AzureRmVMSqlServerAutoBackupConfig** comando. A ausência de **-ativar** parâmetro sinalizar o comando para desativar esta funcionalidade. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a cópia de segurança automatizada.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O script seguinte fornece um conjunto de variáveis que pode personalizar para ativar e configurar a cópia de segurança automatizada para a VM. No seu caso, poderá ter de personalizar o script com base nos seus requisitos. Por exemplo, terá de efetuar alterações, se pretendesse desativar a cópia de segurança das bases de dados do sistema ou ativar a encriptação.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passos seguintes

Cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Pelo que é importante para [consulte a documentação para cópia de segurança gerida](https://msdn.microsoft.com/library/dn449496.aspx) para compreender o comportamento e implicações.

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte tópico: [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).

