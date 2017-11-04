---
title: "Implementar e gerir cópias de segurança para VMs implementadas no Resource Manager com o PowerShell | Microsoft Docs"
description: "Utilizar o PowerShell para implementar e gerir cópias de segurança no Azure para as VMs implementadas no Resource Manager"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db04f8c6ab61d33df80cd442abc5636867e5809a
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Utilizar cmdlets de AzureRM.RecoveryServices.Backup para fazer uma cópia de segurança de máquinas virtuais
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [Clássico](backup-azure-vms-classic-automation.md)
>
>

Este artigo mostra como utilizar cmdlets do Azure PowerShell para criar cópias de segurança e recuperar uma máquina virtual do Azure (VM) a partir de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é um recurso do Azure Resource Manager e é utilizado para proteger dados e recursos nos serviços de cópia de segurança do Azure e o Azure Site Recovery. Pode utilizar um cofre dos serviços de recuperação para proteger as VMs implementadas no Azure Service Manager e VMs implementadas no Azure Resource Manager.

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo é para utilização com VMs criadas com o modelo do Resource Manager.
>
>

Este artigo explica como utilizar o PowerShell para proteger uma VM e restaurar dados a partir de um ponto de recuperação.

## <a name="concepts"></a>Conceitos
Se não estiver familiarizado com o serviço de cópia de segurança do Azure, para uma descrição geral do serviço, consulte [que é o Backup do Azure?](backup-introduction-to-azure-backup.md) Antes de começar, certifique-se de que abrange os essentials sobre os pré-requisitos necessários para funcionar com cópia de segurança do Azure e as limitações da solução de cópia de segurança de VM atual.

Para utilizar eficazmente o PowerShell, é necessário compreender a hierarquia de objetos e o local iniciar.

![Hierarquia de objeto de serviços de recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para ver a referência de cmdlet do AzureRm.RecoveryServices.Backup PowerShell, consulte o [cópia de segurança do Azure - Cmdlets de serviços de recuperação](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) na biblioteca do Azure.

## <a name="setup-and-registration"></a>Configuração e o registo
Para começar:

1. [Transfira a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (é a versão mínima necessária: 1.4.0)
2. Localize os cmdlets do PowerShell de cópia de segurança do Azure disponíveis, escrevendo o seguinte comando:

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


As seguintes tarefas podem ser automatizadas com o PowerShell:

* Criar um cofre dos Serviços de Recuperação 
* Fazer uma cópia de segurança de VMs do Azure
* Acionar uma tarefa de cópia de segurança
* Monitorizar uma tarefa de cópia de segurança
* Restaurar uma VM do Azure

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Os seguintes passos encaminharem através da criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de cópia de segurança.

1. Se estiver a utilizar o Backup do Azure pela primeira vez, tem de utilizar o  **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**  cmdlet para registar o fornecedor de serviços de recuperação do Azure com a sua subscrição.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. O Cofre de serviços de recuperação é um recurso de Gestor de recursos, por isso terá de colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um grupo de recursos com o  **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**  cmdlet. Ao criar um grupo de recursos, especifique o nome e a localização para o grupo de recursos.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Utilize o  **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)**  cmdlet para criar o Cofre dos serviços de recuperação. Lembre-se de que especifique a mesma localização do cofre que foi utilizado para o grupo de recursos.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Especifique o tipo de redundância de armazenamento a utilizar. Pode utilizar [localmente redundante armazenamento (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) ou [Georreplicação redundante armazenamento (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). O exemplo seguinte mostra que a opção - BackupStorageRedundancy para testvault está definida como GeoRedundant.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Cmdlets de cópia de segurança do Azure muitos requerem o objeto de cofre dos serviços de recuperação como entrada. Por este motivo, é conveniente armazenar o objeto de cofre dos serviços de recuperação de cópia de segurança numa variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição
Utilize  **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**  para ver a lista de todos os cofres na subscrição atual. Pode utilizar este comando para verificar que foi criado um novo cofre de ou para ver os cofres disponíveis na subscrição.

Execute o comando Get-AzureRmRecoveryServicesVault, para ver todos os cofres na subscrição. O exemplo seguinte mostra as informações apresentadas para cada cofre.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure
Utilize um cofre dos serviços de recuperação para proteger as máquinas virtuais. Antes de aplicar a proteção, defina o contexto de cofre (do tipo de dados protegidos no cofre) e certifique-se a política de proteção. A política de proteção está a agenda quando executar tarefas de cópia de segurança e quanto cada instantâneo de cópia de segurança é mantido.

### <a name="set-vault-context"></a>Contexto do Cofre de conjunto
Antes de ativar a proteção numa VM, utilize  **[conjunto AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  para definir o contexto do cofre. Depois do contexto de cofre for definido, aplica-se a todos os cmdlets subsequentes. O exemplo a seguir define o contexto do cofre para o cofre, *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Criar uma política de proteção
Quando criar um cofre dos serviços de recuperação, este inclui proteção predefinida e as políticas de retenção. A política de proteção predefinido aciona uma tarefa de cópia de segurança por dia num momento especificado. A política de retenção predefinido mantém o ponto de recuperação diários 30 dias. Pode utilizar a política predefinida para proteger rapidamente a sua VM e editar a política mais tarde com detalhes diferentes.

Utilize  **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**  para ver as políticas de proteção no cofre. Pode utilizar este cmdlet para obter uma política específica ou para ver as políticas associadas um tipo de carga de trabalho. O exemplo seguinte obtém as políticas para o tipo de carga de trabalho, AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de cópia de segurança é apresentado no portal do Azure, a hora é ajustada para o seu fuso horário local.
>
>

Uma política de proteção de cópia de segurança está associada a, pelo menos, uma política de retenção. Política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser eliminado. Utilize  **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)**  para ver a política de retenção predefinido.  Da mesma forma, pode utilizar  **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)**  para obter a política de agenda predefinida. O  **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**  cmdlet cria um objeto do PowerShell que contém informações de política de cópia de segurança. Os objetos de política de agendamento e a retenção são utilizados como entradas para a  **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**  cmdlet. O exemplo seguinte armazena a política de agendamento e a política de retenção em variáveis. O exemplo utiliza essas variáveis para definir os parâmetros ao criar uma política de proteção, *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Ativar a proteção
Assim que tiver definido a política de cópia de segurança de proteção, ainda tem de ativar a política para um item. Utilize  **[ativar AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)**  para ativar a proteção. Ativar a proteção requer dois objetos - o item e a política. Depois da política foi associada ao cofre, o fluxo de trabalho de cópia de segurança é acionado ao tempo definido no agendamento de política.

O exemplo seguinte ativa a proteção para o item, V2VM, utilizando a política, NewPolicy. Para ativar a proteção em VMs do Gestor de recursos não encriptadas

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para ativar a proteção em VMs encriptadas (encriptados utilizando BEK e KEK), terá de conceder a permissão de serviço de cópia de segurança do Azure para ler as chaves e segredos do Cofre de chaves.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para ativar a proteção no encriptados VMs (encriptadas utilizando apenas BEK), terá de conceder a permissão de serviço de cópia de segurança do Azure para ler os segredos do Cofre de chaves.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Se estiver a utilizar a nuvem do Azure Government, utilize ff281ffe-705c-4f53-9f37-a40e6f2c68f3 o valor para o parâmetro **- ServicePrincipalName** no [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet.
>
>

Para VMs clássicas

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção
Para modificar a política de proteção, utilize [conjunto AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) para modificar os objetos SchedulePolicy ou RetentionPolicy.

O exemplo a seguir altera a retenção do ponto de recuperação a 365 dias.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Acione uma cópia de segurança
Pode utilizar  **[AzureRmRecoveryServicesBackupItem de cópia de segurança](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**  para acionar uma tarefa de cópia de segurança. Se estiver a cópia de segurança inicial, é uma cópia de segurança completa. Cópias de segurança subsequentes efetuar uma cópia incremental. Certifique-se de utilizar  **[conjunto AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  para definir o contexto de cofre antes de acionar a tarefa de cópia de segurança. O exemplo seguinte parte do princípio de contexto de cofre foi definido.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> O fuso horário dos campos no PowerShell StartTime e EndTime é UTC. No entanto, quando o tempo é apresentado no portal do Azure, a hora é ajustada para o seu fuso horário local.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorização de uma tarefa de cópia de segurança
Pode monitorizar operações de longa execução, tais como as tarefas de cópia de segurança, sem utilizar o portal do Azure. Para obter o estado de uma tarefa em curso, utilize o  **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**  cmdlet. Este cmdlet obtém as tarefas de cópia de segurança de um cofre específico e que Cofre é especificado no contexto do cofre. O exemplo seguinte obtém o estado de uma tarefa em curso como uma matriz e armazena o estado na variável $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de consulta estas tarefas de conclusão - o que é desnecessário códigos adicionais - utilize o  **[espera AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**  cmdlet. Este cmdlet interrompe a execução até que a tarefa é concluída ou for atingido o valor de limite de tempo especificado.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Restaurar uma VM do Azure
Não há uma principal diferença entre o restauro de uma VM com o portal do Azure e o restauro de uma VM com o PowerShell. Com o PowerShell, a operação de restauro está concluída assim que os discos e as informações de configuração do ponto de recuperação são criadas.

> [!NOTE]
> A operação de restauro não criar uma máquina virtual.
>
>

Para criar uma máquina virtual a partir do disco, consulte a secção [criar a VM de discos armazenados](backup-azure-vms-automation.md#create-a-vm-from-stored-disks). Os passos básicos para restaurar uma VM do Azure são:

* Selecione a VM
* Escolha um ponto de recuperação
* Restaurar os discos
* Criar a VM de discos armazenados

O gráfico seguinte mostra a hierarquia de objeto de RecoveryServicesVault para baixo até o BackupRecoveryPoint.

![Hierarquia de objeto de serviços de recuperação que mostra BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar dados de cópia de segurança, identifica o item de cópia de segurança e o ponto de recuperação que contém os dados de ponto no tempo. Utilize o  **[restauro AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**  cmdlet para restaurar dados a partir do cofre para a conta do cliente.

### <a name="select-the-vm"></a>Selecione a VM
Para obter o objeto do PowerShell que identifica o item de cópia de segurança adequados, iniciar a partir do contentor no cofre e a trabalhar a hierarquia de objeto. Para selecionar o contentor que representa a VM, utilize o  **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)**  cmdlet e que para encaminhar o  **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**  cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" –Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Escolha um ponto de recuperação
Utilize o  **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)**  cmdlet para listar todos os pontos de recuperação para o item de cópia de segurança. Em seguida, escolha o ponto de recuperação para restaurar. Se não souber o ponto de recuperação a utilizar, é uma boa prática para escolher a mais recente RecoveryPointType = AppConsistent ponto na lista.

No script seguinte, a variável, **$rp**, é uma matriz de pontos de recuperação para o item de cópia de segurança selecionado, dos últimos sete dias. A matriz é ordenada pela ordem inversa de tempo com o ponto de recuperação mais recente no índice 0. Utilize a indexação da matriz de PowerShell padrão para escolher o ponto de recuperação. No exemplo, $rp [0] seleciona o ponto de recuperação mais recente.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Restaurar os discos
Utilize o  **[restauro AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**  cmdlet para restaurar dados de um item de cópia de segurança e configuração para um ponto de recuperação. Depois de identificar um ponto de recuperação, utilizá-lo como o valor para o **- RecoveryPoint** parâmetro. No código de exemplo anterior, **$rp [0]** foi o ponto de recuperação a utilizar. No seguinte código de exemplo, **$rp [0]** é o ponto de recuperação a utilizar para restaurar o disco.

Para restaurar os discos e as informações de configuração:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Utilize o  **[espera AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**  cmdlet para aguardar a conclusão da tarefa de restauro.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Depois de concluída a tarefa de restauro, utilize o  **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)**  cmdlet para obter os detalhes da operação de restauro. A propriedade de JobDetails tem as informações necessárias para reconstruir a VM.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Depois de restaurar os discos, consulte a secção seguinte para criar a VM.

## <a name="create-a-vm-from-restored-disks"></a>Criar uma VM a partir de discos restaurados
Depois de ter restaurado os discos, utilize estes passos para criar e configurar a máquina virtual de disco.

> [!NOTE]
> Para criar VMs encriptadas de discos restaurados, a função do Azure tem de ter permissão para efetuar a ação **Microsoft.KeyVault/vaults/deploy/action**. Se a função não tem esta permissão, crie uma função personalizada com esta ação. Para obter mais informações, consulte [funções personalizadas no Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).
>
>

1. Consulta as propriedades de disco restauradas para os detalhes da tarefa.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Definir o contexto de armazenamento do Azure e restaurar o ficheiro de configuração JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Utilize o ficheiro de configuração JSON para criar a configuração de VM.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Anexe o disco do SO e discos de dados. Dependendo da configuração do seu VMs, consulte a secção relevante para ver os respetivos cmdlets:

    #### <a name="non-managed-non-encrypted-vms"></a>VMs de não gerido, não encriptados

    Utilize o seguinte exemplo para VMs não geridos, não encriptada.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>VMs encriptadas, não geridos (apenas BEK)

    Para VMs encriptadas, não geridos (encriptadas utilizando apenas BEK), tem de restaurar o segredo ao Cofre de chaves antes de pode anexar discos. Para obter mais informações, consulte o artigo [restaurar uma máquina virtual encriptada a partir de um ponto de recuperação de cópia de segurança do Azure](backup-azure-restore-key-secret.md). O exemplo seguinte mostra como ligar o SO e discos de dados para as VMs encriptadas.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>VMs encriptadas, não geridos (BEK e KEK)

    Para VMs encriptadas, não geridos (encriptadas utilizando BEK e KEK), tem de restaurar a chave e o segredo ao Cofre de chaves antes de pode anexar discos. Para obter mais informações, consulte o artigo [restaurar uma máquina virtual encriptada a partir de um ponto de recuperação de cópia de segurança do Azure](backup-azure-restore-key-secret.md). O exemplo seguinte mostra como ligar o SO e discos de dados para as VMs encriptadas.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>Não encriptada de VMs geridas

    Para VMs geridas não encriptada, terá de criar discos geridos a partir do blob storage e, em seguida, ligue os discos. Para obter informações aprofundadas, consulte o artigo [anexar um disco de dados para uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md). O código de exemplo seguinte mostra como anexar os discos de dados para VMs gerido não encriptada.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>Gerido, VMs encriptadas (apenas BEK)

    Para VMs encriptadas geridas (encriptadas utilizando apenas BEK), terá de criar discos geridos a partir do blob storage e, em seguida, ligue os discos. Para obter informações aprofundadas, consulte o artigo [anexar um disco de dados para uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md). O código de exemplo seguinte mostra como anexar os discos de dados para as VMs encriptadas geridas.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>VMs geridas do, encriptadas (BEK e KEK)

    Para VMs encriptadas geridas (encriptadas utilizando BEK e KEK), terá de criar discos geridos a partir do blob storage e, em seguida, ligue os discos. Para obter informações aprofundadas, consulte o artigo [anexar um disco de dados para uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md). O código de exemplo seguinte mostra como anexar os discos de dados para as VMs encriptadas geridas.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. Configurar as definições de rede.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Criar a máquina virtual.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>Passos seguintes
Se preferir utilizar o PowerShell interaja com os recursos do Azure, consulte o artigo PowerShell [implementar e gerir cópia de segurança para o Windows Server](backup-client-automation.md). Se gerir cópias de segurança do DPM, consulte o artigo [implementar e gerir cópia de segurança do DPM](backup-dpm-automation.md). Ambos estes artigos tem uma versão para implementações de clássico e implementações do Resource Manager.  
