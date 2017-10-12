---
title: "Início Rápido do Azure - Fazer uma cópia de segurança de uma VM com o PowerShell | Microsoft Docs"
description: "Saiba como fazer cópias de segurança das suas máquinas virtuais com o Azure PowerShell"
services: virtual-machines-windows, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-windows, azure-backup
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3ca0e9d905e23e25b57b46454399ad12e2890d52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer uma cópia de segurança de uma máquina virtual no Azure com o PowerShell
O módulo Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. O Azure Backup cria pontos de recuperação que podem ser armazenados em cofres de recuperação georredundantes. Este artigo mostra em detalhe como fazer uma cópia de segurança de uma máquina virtual (VM) com o módulo Azure PowerShell. Também pode executar estes passos com a [CLI do Azure](quick-backup-vm-cli.md) ou o [portal do Azure](quick-backup-vm-portal.md).

Este início rápido ativa a cópia de segurança numa VM do Azure existente. Se tiver de criar uma VM, pode [criá-la com o Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Este início rápido requer a versão 4.4 ou posterior do módulo Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

Da primeira vez que utilizar o Azure Backup, tem de registar o fornecedor do Serviço de Recuperação do Azure na sua subscrição com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Um cofre dos Serviços de Recuperação é um contentor lógico que armazena os dados da cópia de segurança de todos os recursos protegidos, como as VMs do Azure. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um cofre dos Serviços de Recuperação com [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Especifique o mesmo grupo de recursos e a mesma localização da VM que quer proteger. Se tiver utilizado o [script de exemplo](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para criar a sua VM, o grupo de recursos terá o nome *myResourceGroup*, a VM *myVM* e os recursos estarão na localização *Europa Ocidental*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Por predefinição, o cofre está definido para Armazenamento georredundante. Para proteger ainda mais os seus dados, este nível de redundância de armazenamento garante que os dados das cópias de segurança são replicados numa região do Azure secundária, que está situada a centenas de quilómetros de distância da região principal.

Para utilizar este cofre com os restantes passos, defina o contexto do cofre com [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext)

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Ativar a cópia de segurança em VMs do Azure
Para definir quando uma é executada uma tarefa de cópia de segurança e durante quanto tempo os pontos de recuperação são armazenados, tem de criar e utilizar políticas. A política de proteção predefinida executa uma tarefa de cópia de segurança todos os dias e mantém os pontos de recuperação durante 30 dias. Pode utilizar estes valores da política predefinida para proteger rapidamente a sua VM. Em primeiro lugar, defina a política predefinida com [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Para ativar a proteção de cópia de segurança para uma VM, utilize [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Especifique a política a utilizar, o grupo de recursos e, em seguida, a VM a proteger:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança
Para iniciar uma cópia de segurança agora em vez de aguardar até que a política predefinida execute a tarefa à hora agendada, utilize [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). Esta primeira tarefa de cópia de segurança cria um ponto de recuperação completo. Todas as tarefas de cópia de segurança que se seguem a esta cópia de segurança inicial criam pontos de recuperação incrementais. Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

No conjunto de comandos seguinte, vai especificar um contentor no cofre dos Serviços de Recuperação que contém os dados da cópia de segurança com [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Cada VM da qual vai fazer uma cópia de segurança é tratada como um item. Para iniciar uma tarefa de cópia de segurança, obtenha informações sobre o item da VM com [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Visto que esta primeira tarefa de cópia de segurança cria um ponto de recuperação completo, o processo pode demorar até 20 minutos.


## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança
Para monitorizar o estado das tarefas de cópia de segurança, utilize [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

A saída é semelhante ao exemplo seguinte, o qual mostra que a tarefa de criação da cópia de segurança está **InProgress** (Em curso):

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Quando o *Estado* da tarefa de cópia de segurança mostrar *Concluído*, a VM estará protegida com os Serviços de Recuperação e tem armazenado um ponto de recuperação completo.


## <a name="clean-up-deployment"></a>Limpar a implementação
Quando já não precisar, pode desativar a proteção na VM, remover os pontos de restauro e o cofre dos Serviços de Recuperação e, em seguida, eliminar o grupo de recursos e os recursos da VM associados. Se tiver utilizado uma VM existente, pode ignorar o último cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para manter o grupo de recursos e a VM no local.

Se quiser avançar para um tutorial de Cópia de segurança que explique como restaurar dados para a sua VM, ignore os passos desta secção e aceda a [Passos seguintes](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial. Para saber mais sobre o Azure Backup e os Serviços de Recuperação, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Back up multiple Azure VMs](./tutorial-backup-vm-at-scale.md)(Fazer uma cópia de segurança de várias VMs do Azure)