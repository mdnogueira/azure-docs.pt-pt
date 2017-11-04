---
title: "Automatizada v2 cópia de segurança SQL Server 2016 máquinas virtuais do Azure | Microsoft Docs"
description: "Explica a funcionalidade de cópia de segurança automatizada para o SQL Server 2016 VMs em execução no Azure. Este artigo é específico para VMs com o Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/05/2017
ms.author: jroth
ms.openlocfilehash: e7e14b0243f82c672392d5ab4bb6aca01156465b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automated-backup-v2-for-sql-server-2016-azure-virtual-machines-resource-manager"></a>Automatizada v2 cópia de segurança para o SQL Server 2016 máquinas virtuais do Azure (Gestor de recursos)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Configura automaticamente v2 de cópia de segurança automatizada [cópia de segurança gerida no Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os existentes e novas bases de dados numa VM do Azure a executar as edições do SQL Server 2016 Standard, Enterprise ou programador. Isto permite-lhe configurar cópias de segurança regular da base de dados que utilizam o armazenamento de Blobs do Azure durável. V2 de cópia de segurança automatizada depende o [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a cópia de segurança automatizada v2, reveja os seguintes pré-requisitos:

**Sistema operativo**:

- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- SQL Server 2016 Standard
- SQL Server 2016 Enterprise
- SQL Server 2016 Developer

> [!IMPORTANT]
> V2 de cópia de segurança automatizada funciona com o SQL Server 2016. Se estiver a utilizar o SQL Server 2014, pode utilizar v1 de cópia de segurança automatizada para fazer cópias de segurança das bases de dados. Para obter mais informações, consulte [cópia de segurança automatizada para o SQL Server 2014 do Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

**Configuração de base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo. Para obter mais informações sobre o impacto do modelo de recuperação completa em cópias de segurança, consulte [cópia de segurança nos completo modelo de recuperação](https://technet.microsoft.com/library/ms190217.aspx).
- Bases de dados do sistema não têm de utilizar o modelo de recuperação completo. No entanto, se necessitar de cópias de segurança do registo ser executada para o modelo ou MSDB, tem de utilizar o modelo de recuperação completo.
- Tem de estar bases de dados de destino na instância predefinida do SQL Server. A extensão de IaaS do SQL Server não suporta instâncias nomeadas.

**Modelo de implementação do Azure**:

- Resource Manager

> [!NOTE]
> Cópia de segurança automatizada depende o **extensão de agente do SQL Server IaaS**. Imagens de galeria da máquina virtual SQL atuais adicionar esta extensão, por predefinição. Para obter mais informações, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizada v2. Os passos de configuração reais variam dependendo se utilizar o portal do Azure ou os comandos do Azure Windows PowerShell.

### <a name="basic-settings"></a>Definições básicas

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópia de Segurança Automatizada** | Ativar/desativar (desativada) | Ativa ou desativa a cópia de segurança automatizada para uma VM do Azure a executar o SQL Server 2016 Standard ou Enterprise. |
| **Período de retenção** | 1-30 dias (30 dias) | O número de dias a manter as cópias de segurança. |
| **Conta de armazenamento** | Conta de armazenamento do Azure | Uma conta de armazenamento do Azure a utilizar para armazenar ficheiros de cópia de segurança automatizada no blob storage. Um contentor é criado nesta localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e a base de dados GUID. |
| **Encriptação** |Ativar/desativar (desativada) | Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança estão localizados na conta de armazenamento especificada na mesma **automaticbackup** contentor utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, é gerado um novo certificado com essa palavra-passe, mas o certificado antigo permanece restaurar cópias de segurança anteriores. |
| **Palavra-passe** |Texto da palavra-passe | Uma palavra-passe para as chaves de encriptação. Isto apenas é necessário se a encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que da cópia de segurança foi efetuada. |

### <a name="advanced-settings"></a>Definições avançadas

| Definição | Intervalo (predefinição) | Descrição |
| --- | --- | --- |
| **Cópias de segurança do sistema da base de dados** | Ativar/desativar (desativada) | Quando ativada, esta funcionalidade também irá criar cópias de segurança de bases de dados do sistema: mestre, MSDB e modelo. Para as bases de dados MSDB e o modelo, certifique-se de que estão em modo de recuperação completo se pretender que as cópias de segurança de registo para ser executada. Cópias de segurança de registo nunca direcionadas para mestre. E não existem cópias de segurança são encaminhadas para TempDB. |
| **Agenda de cópia de segurança** | Manual/automática (automatizada) | Por predefinição, a agenda de cópia de segurança será automaticamente determinada com base no crescimento do registo. Agenda de cópia de segurança manual permite ao utilizador especificar a janela de tempo para cópias de segurança. Neste caso, as cópias de segurança apenas nunca ocorrerá a frequência especificada e durante a janela de tempo especificado de um determinado dia. |
| **Frequência de cópia de segurança completa** | Diariamente/semanal | Frequência de cópias de segurança completas. Em ambos os casos, irão iniciar as cópias de segurança completas durante a próxima janela de hora agendada. Quando é selecionada semanal, as cópias de segurança foi abranger vários dias, até que todas as bases de dados com êxito de ter uma cópia de segurança. |
| **Hora de início de cópia de segurança completa** | 00:00 – 23:00 (01:00) | A hora de início de um determinado dia durante os quais podem efetuar cópias de segurança completas. |
| **Janela de tempo de cópia de segurança completa** | 1 – 23 horas (1 hora) | Duração da janela de tempo de um determinado dia durante os quais podem efetuar cópias de segurança completas. |
| **Frequência de cópia de segurança do registo** | 5 – 60 minutos (60 minutos) | Frequência de cópias de segurança do registo. |

## <a name="understanding-full-backup-frequency"></a>Compreender a frequência de cópia de segurança completa
É importante compreender a diferença entre cópias de segurança completas diárias e semanais. Este esforço, iremos ajudá-dois cenários de exemplo.

### <a name="scenario-1-weekly-backups"></a>Cenário 1: Cópias de segurança semanais
Tiver uma VM do SQL Server que contém um número de bases de dados muito grandes.

Na segunda-feira, ativar a cópia de segurança automatizada v2 com as seguintes definições:

- Agenda de cópia de segurança: **Manual**
- Frequência de cópia de segurança de completa: **semanal**
- Hora de início de cópia de segurança de completa: **01:00**
- Janela de tempo de cópia de segurança completa: **1 hora**

Isto significa que a próxima janela de cópia de segurança disponível terça-feira às 1: 00 de 1 hora. Nessa altura, cópia de segurança automatizada irá iniciar a cópia de segurança das bases de dados um cada vez. Neste cenário, as bases de dados, são suficientemente grandes para que as cópias de segurança completas concluirá para as bases de dados de algumas primeiro. No entanto, depois de uma hora nem todas as bases de dados tem uma cópia de segurança.

Quando isto acontecer, cópia de segurança automatizada irá iniciar a cópia de segurança das bases de dados restantes o dia seguinte, quarta-feira às 1: 00 de 1 hora. Se nem todas as bases de dados tem uma cópia de segurança nessa hora, tentará novamente o dia seguinte ao mesmo tempo. Isto irá continuar até que todas as bases de dados tem sido com êxito uma cópia de segurança.

Assim que chegar novamente Terça-feira, cópia de segurança automatizada irá iniciar a cópia de segurança de todas as bases de dados novamente.

Este cenário mostra que a cópia de segurança automatizada só irá funcionar dentro da janela de tempo especificado, e cada base de dados será feita uma vez por semana. Isto também mostra que é possível que as cópias de segurança abranger vários dias em casos em que não é possível concluir todas as cópias de segurança num único dia.

### <a name="scenario-2-daily-backups"></a>Cenário 2: Cópias de segurança diárias
Tiver uma VM do SQL Server que contém um número de bases de dados muito grandes.

Na segunda-feira, ativar a cópia de segurança automatizada v2 com as seguintes definições:

- Agenda de cópia de segurança: Manual
- Frequência de cópia de segurança de completa: diária
- Hora de início de cópia de segurança de completa: 22:00
- Janela de tempo de cópia de segurança completa: 6 horas

Isto significa que a próxima janela de cópia de segurança disponível segunda-feira em 10 PM durante 6 horas. Nessa altura, cópia de segurança automatizada irá iniciar a cópia de segurança das bases de dados um cada vez.

Em seguida, na Terça-feira, 10 para 6 horas, as cópias de segurança completas de todas as bases de dados serão iniciada novamente.

> [!IMPORTANT]
> Durante o agendamento de cópias de segurança diárias, recomenda-se que agende uma janela de tempo wide para garantir que todas as bases de dados podem ser feitas no momento. Isto é especialmente importante no caso em que tiver uma grande quantidade de dados para cópia de segurança.

## <a name="configuration-in-the-portal"></a>Configuração no Portal do

Pode utilizar o portal do Azure para configurar a cópia de segurança automatizada v2 durante o aprovisionamento de ou para VMs de 2016 existente do SQL Server.

### <a name="new-vms"></a>Novas VMs

Utilize o portal do Azure para configurar a cópia de segurança automatizada v2 quando cria um novo SQL Server 2016 máquina no modelo de implementação Resource Manager. 

No **definições do SQL Server** painel, selecione **cópia de segurança automatizada**. A seguinte captura de ecrã de portal do Azure mostra o **cópia de segurança do SQL Server automatizada** painel.

![Configuração de cópia de segurança do SQL Server automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> V2 de cópia de segurança automatizada está desativada por predefinição.

Para o contexto, consulte o tópico completo sobre [aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

Para máquinas virtuais do SQL Server existentes, selecione a máquina virtual do SQL Server. Em seguida, selecione o **configuração do SQL Server** secção o **definições** painel.

![SQL Server cópia de segurança automatizada para VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

No **configuração do SQL Server** painel, clique em de **editar** botão na secção de cópia de segurança automatizada.

![Configurar a cópia de segurança do SQL Server automatizada para VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Quando terminar, clique em de **OK** botão na parte inferior do **configuração do SQL Server** painel para guardar as alterações.

Se pretende ativar a cópia de segurança automatizada pela primeira vez, o Azure configura o agente do SQL Server IaaS em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a cópia de segurança automatizada está configurada. Aguarde alguns minutos para que o agente ser instalado, configurado. Depois de que o portal do Azure irá refletir as novas definições.

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell

Pode utilizar o PowerShell para configurar a cópia de segurança automatizada v2. Antes de começar, tem de:

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
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Se o resultado mostra que **ativar** está definido como **falso**, terá de ativar a cópia de segurança automatizada. Boas notícias são que pode ativar e configurar cópia de segurança automatizada da mesma forma. Consulte a secção seguinte para obter estas informações.

> [!NOTE] 
> Se verifique as definições de imediatamente depois de efetuar uma alteração, é possível que irá obter novamente os valores de configuração antigo. Aguarde alguns minutos e verifique as definições novamente para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup-v2"></a>Configurar v2 de cópia de segurança automatizada
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

Em seguida, utilize o **New-AzureRmVMSqlServerAutoBackupConfig** comando para ativar e configurar as definições de v2 de cópia de segurança automatizada para armazenar as cópias de segurança na conta do storage do Azure. Neste exemplo, as cópias de segurança são definidas para ser mantidas durante 10 dias. Cópias de segurança de base de dados de sistema estão ativadas. Cópias de segurança completas agendadas para semanal com uma janela de tempo, começando 20:00 para duas horas. Cópias de segurança do registo agendadas para cada 30 minutos. O segundo comando, **conjunto AzureRmVMSqlServerExtension**, atualiza a VM do Azure especificada com estas definições.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Pode demorar alguns minutos para instalar e configurar o agente do SQL Server IaaS. 

Para ativar a encriptação, modificar o script anterior para passar o **EnableEncryption** parâmetro juntamente com uma palavra-passe (cadeia segura) para o **CertificatePassword** parâmetro. O script seguinte Ativa as definições de cópia de segurança automatizada no exemplo anterior e adiciona a encriptação.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passos seguintes
V2 de cópia de segurança automatizada configura a cópia de segurança gerida em VMs do Azure. Pelo que é importante para [consulte a documentação para cópia de segurança gerida](https://msdn.microsoft.com/library/dn449496.aspx) para compreender o comportamento e implicações.

Pode encontrar a cópia de segurança adicional e restaurar as orientações para o SQL Server em VMs do Azure no seguinte tópico: [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).

