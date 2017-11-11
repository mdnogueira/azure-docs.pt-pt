---
title: "Implementar e gerir a cópia de segurança para as VMs do Azure com o PowerShell | Microsoft Docs"
description: "Saiba como implementar e gerir a cópia de segurança do Azure com o PowerShell."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/9/2017
ms.author: markgal;trinadhk;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88ca71f83696c5865405799cddc3645778261408
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>Utilizar cmdlets de AzureRM.Backup para fazer uma cópia de segurança de máquinas virtuais
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [Clássico](backup-azure-vms-classic-automation.md)
>
>

Este artigo mostra como utilizar o Azure PowerShell para cópia de segurança e recuperação de VMs do Azure. O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Resource Manager e Clássico. Este artigo abrange utilizando o modelo de implementação clássica para cópias de segurança para um cofre de cópia de segurança. Se não tiver criado um cofre de cópia de segurança na sua subscrição, consulte a versão do Gestor de recursos deste artigo, [AzureRM.RecoveryServices.Backup de utilização de cmdlets para fazer uma cópia de segurança de máquinas virtuais](backup-azure-vms-automation.md). A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

> [!IMPORTANT]
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 30 de Novembro de 2017, não poderá utilizar o PowerShell para criar cofres de cópia de segurança.<br/> **Até 30 de Novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>

## <a name="concepts"></a>Conceitos
Este artigo fornece informações específicas para os cmdlets do PowerShell utilizados para fazer uma cópia de segurança de máquinas virtuais. Para obter informações introdutórias sobre como proteger VMs do Azure, consulte [planear a infraestrutura de cópia de segurança de VM no Azure](backup-azure-vms-introduction.md).

> [!NOTE]
> Antes de começar, leia o [pré-requisitos](backup-azure-vms-prepare.md) necessários para trabalhar com cópias de segurança do Azure e o [limitações](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) da VM atual solução de cópia de segurança.
>
>

Para utilizar eficazmente o PowerShell, demorar um pouco para compreender a hierarquia de objetos e o local iniciar.

![Hierarquia de objeto](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

Os dois fluxos mais importantes são ativar a proteção para uma VM e restaurar dados a partir de um ponto de recuperação. O foco este artigo é para o ajudar a tornar-se adept ao trabalhar com os cmdlets do PowerShell para ativar estes dois cenários.

## <a name="setup-and-registration"></a>Configuração e o registo
Para começar:

1. [Transferir o mais recente do PowerShell](https://github.com/Azure/azure-powershell/releases) (versão mínima necessária é: 1.0.0)
2. Localize os cmdlets do PowerShell de cópia de segurança do Azure disponíveis, escrevendo o seguinte comando:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

As seguintes tarefas de configuração e o registo podem ser automatizadas com o PowerShell:

* Criar um cofre de cópia de segurança
* Registar as VMs com o serviço de cópia de segurança do Azure

### <a name="create-a-backup-vault"></a>Criar um cofre de cópia de segurança
> [!WARNING]
> Para os clientes utilizando a cópia de segurança do Azure pela primeira vez, tem de registar o fornecedor de cópia de segurança do Azure a ser utilizado com a sua subscrição. Isto pode ser efetuado executando o seguinte comando: Register AzureRmResourceProvider - ProviderNamespace "Microsoft.Backup"
>
>

Pode criar um novo cofre de cópia de segurança utilizando o **New-AzureRmBackupVault** cmdlet. O Cofre de cópia de segurança é um recurso ARM, por isso terá de colocá-lo dentro de um grupo de recursos. Numa consola do Azure PowerShell elevada, execute os seguintes comandos:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Pode obter uma lista de todos os cofres de cópia de segurança numa determinada subscrição através de **Get-AzureRmBackupVault** cmdlet.

> [!NOTE]
> É conveniente armazenar o objeto do Cofre de cópia de segurança numa variável. O objeto de cofre é necessário como entrada para vários cmdlets de cópia de segurança do Azure.
>
>

### <a name="registering-the-vms"></a>As VMs a registar
É o primeiro passo para configurar a cópia de segurança com cópia de segurança do Azure para registar o seu computador ou a VM com um cofre de cópia de segurança do Azure. O **Register-AzureRmBackupContainer** cmdlet aceita as informações de entrada de uma máquina virtual do IaaS do Azure e regista-o com o Cofre especificado. A operação register associa a máquina virtual do Azure com o Cofre de cópia de segurança e controla a VM através do ciclo de vida de cópia de segurança.

Registar a VM com o serviço de cópia de segurança do Azure cria um objeto de contentor de nível superior. Um contentor normalmente contém vários itens que podem ser feitos, mas no caso de VMs haver apenas um item de cópia de segurança para o contentor.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Fazer cópia de segurança de VMs do Azure
### <a name="create-a-protection-policy"></a>Criar uma política de proteção
Não é obrigatória ao criar uma nova política de proteção para iniciar a cópia de segurança das suas VMs. O Cofre é fornecido com um 'Política predefinida' que pode ser utilizado para ativar rapidamente a proteção e, em seguida, editá-lo mais tarde, com os detalhes da direita. Pode obter uma lista das políticas disponíveis no cofre utilizando o **Get-AzureRmBackupProtectionPolicy** cmdlet:

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de cópia de segurança é apresentado no portal do Azure, o fuso horário é alinhado ao seu sistema local, juntamente com o Desvio UTC.
>
>

Uma política de cópia de segurança está associada a, pelo menos, uma política de retenção. A política de retenção define quanto tempo um ponto de recuperação é mantido com cópia de segurança do Azure. O **New-AzureRmBackupRetentionPolicy** cmdlet cria os objetos de PowerShell que contêm informações de política de retenção. Estes objetos de política de retenção são utilizados como entradas para a *New-AzureRmBackupProtectionPolicy* cmdlet, ou diretamente com o *ativar AzureRmBackupProtection* cmdlet.

Uma política de cópia de segurança define quando e com que frequência é feita a cópia de segurança de um item. O **New-AzureRmBackupProtectionPolicy** cmdlet cria um objeto do PowerShell que contém informações de política de cópia de segurança. A política de cópia de segurança é utilizada como uma entrada para o *ativar AzureRmBackupProtection* cmdlet.

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>Ativar a proteção
Ativar a proteção envolve dois objetos - o Item e a política, e ambos têm de pertencer ao mesmo cofre. Depois da política foi associada com o item, o fluxo de trabalho de cópia de segurança irá iniciar no agendamento definido.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>Cópia de segurança inicial
A agenda de cópia de segurança tratará a ter de fazer a cópia inicial completa para o item e a cópia incremental para as cópias de segurança subsequentes. No entanto, se pretender forçar a cópia de segurança inicial com acontecer num determinado período de tempo ou até mesmo imediatamente, em seguida, utilizar o **AzureRmBackupItem de cópia de segurança** cmdlet:

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> O fuso horário dos campos StartTime e EndTime mostrado no PowerShell é UTC. No entanto, quando as informações semelhantes são apresentadas no portal do Azure, o fuso horário é alinhado com o relógio do seu sistema local.
>
>

### <a name="monitoring-a-backup-job"></a>Monitorização de uma tarefa de cópia de segurança
A maioria das operações de execução longa na cópia de segurança do Azure são modelled como uma tarefa. Isto torna mais fácil acompanhar o progresso sem ter de manter o portal do Azure abra permanente.

Para obter o estado mais recente de uma tarefa em curso, utilize o **Get-AzureRmBackupJob** cmdlet.

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Em vez de consulta estas tarefas de conclusão - o que é código desnecessário, adicional - é mais simples utilizar o **espera AzureRmBackupJob** cmdlet. Quando utilizar um script, o cmdlet irá colocar em pausa a execução até que a tarefa é concluída ou for atingido o valor de limite de tempo especificado.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Restaurar uma VM do Azure
Para restaurar dados de cópia de segurança, tem de identificar o Item de cópia de segurança e o ponto de recuperação que contém os dados de ponto no tempo. Esta informação é fornecida para o cmdlet de restauro AzureRmBackupItem para iniciar um restauro de dados a partir do cofre para a conta do cliente.

### <a name="select-the-vm"></a>Selecione a VM
Para obter o objeto do PowerShell que identifica o Item de cópia de segurança adequados, terá de iniciar a partir do contentor no cofre e a trabalhar para baixo a hierarquia de objeto. Para selecionar o contentor que representa a VM, utilize o **Get-AzureRmBackupContainer** cmdlet e que para encaminhar o **Get-AzureRmBackupItem** cmdlet.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>Escolha um ponto de recuperação
Agora pode listar todos os pontos de recuperação do item de cópia de segurança utilizando o **Get-AzureRmBackupRecoveryPoint** cmdlet e escolha o ponto de recuperação para restaurar. Normalmente, os utilizadores escolher a mais recente *AppConsistent* ponto na lista.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

A variável ```$rp``` é uma matriz de pontos de recuperação para a cópia de segurança selecionada item, ordenada pela ordem inversa da hora - o ponto de recuperação mais recente é no índice 0. Utilize a indexação da matriz de PowerShell padrão para escolher o ponto de recuperação. Por exemplo: ```$rp[0]``` irá selecionar o ponto de recuperação mais recente.

### <a name="restoring-disks"></a>Restauro dos discos
Não há uma principal diferença entre as operações de restauro feito através do portal do Azure e através do PowerShell do Azure. Com o PowerShell, a operação de restauro interrompe em restaurar os discos e informações de configuração do ponto de recuperação. Não cria uma máquina virtual.

> [!WARNING]
> O restauro-AzureRmBackupItem não criar uma VM. Restaura apenas os discos para a conta de armazenamento especificada. Não é o mesmo comportamento que irá ocorrer no portal do Azure.
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Pode obter os detalhes de utilização de operação de restauro de **Get-AzureRmBackupJobDetails** cmdlet depois de concluída a tarefa de restauro. O *ErrorDetails* propriedade terá as informações necessárias para reconstruir a VM.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>Criar a VM
Criar a VM sem discos restauradas pode ser feito utilizando os cmdlets de gestão de serviço do Azure PowerShell mais antigos, os novos modelos Azure Resource Manager, ou mesmo utilizando o portal do Azure. Um exemplo rápido, mostramos como obter existe utilizando os cmdlets de gestão de serviço do Azure.

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Para obter mais informações sobre como criar uma VM a partir de discos restauradas, leia sobre os seguintes cmdlets:

* [AzureDisk adicionar](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [Novo AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [Novo-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>Exemplos de código
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1. Obter o estado de conclusão da tarefa subtarefas
Para controlar o estado de conclusão de subtarefas individuais, pode utilizar o **Get-AzureRmBackupJobDetails** cmdlet:

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2. Criar um relatório diário/semanal das tarefas de cópia de segurança
Normalmente, os administradores querem saber quais as tarefas de cópia de segurança foi executada nas últimas 24 horas, o estado dessas tarefas de cópia de segurança. Além disso, a quantidade de dados transferidos fornece aos administradores uma forma de estimar a respetiva utilização mensal de dados. O script abaixo obtém os dados não processados a partir do serviço de cópia de segurança do Azure e apresenta as informações na consola do PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Se pretender adicionar capacidades do charting a saída deste relatório, saiba da mensagem de blogue do TechNet [Charting com o PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>Passos seguintes
Se preferir utilizar o PowerShell para interagir com os recursos do Azure, consulte o artigo do PowerShell para proteger o Windows Server, [implementar e gerir cópia de segurança para o Windows Server](backup-client-automation-classic.md). Há também um artigo do PowerShell para gerir cópias de segurança do DPM, [implementar e gerir cópia de segurança do DPM](backup-dpm-automation-classic.md). Estes artigos têm uma versão de implementações do Resource Manager, bem como implementações clássicas.
