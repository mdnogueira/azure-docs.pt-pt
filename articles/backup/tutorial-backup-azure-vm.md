---
title: "Cópia de segurança do Azure VMs no Azure em dimensionamento | Microsoft Docs"
description: "Este tutorial detalhes cópia de segurança de várias máquinas virtuais do Azure para um cofre dos serviços de recuperação."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "cópia de segurança da máquina virtual; fazer uma cópia de segurança de máquina virtual; cópia de segurança e recuperação após desastre"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: 
ms.openlocfilehash: db4e1392acaeb2431d29a851113b7bc5a6dc1e9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Cópia de segurança de máquinas virtuais do Azure no Azure à escala

Este tutorial descreve em detalhe como fazer cópias de segurança de máquinas virtuais do Azure para um cofre dos serviços de recuperação. A maioria do trabalho para fazer uma cópia de segurança de máquinas virtuais é a preparação. Antes de pode fazer cópias de segurança (ou proteger) uma máquina virtual, tem de concluir o [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger as suas VMs. 

> [!IMPORTANT]
> Este tutorial parte do princípio de que já criou um grupo de recursos e uma máquina virtual do Azure.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

A [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor que detém os pontos de recuperação para os itens a cópia de segurança. Um cofre dos serviços de recuperação é um recurso do Azure que pode ser implementado e gerido como parte de um grupo de recursos do Azure. Neste tutorial, vai criar um cofre dos serviços de recuperação no mesmo grupo de recursos que a máquina virtual a ser protegido.


A primeira que é utilizar a cópia de segurança do Azure, tem de registar o fornecedor de serviços de recuperação do Azure com a sua subscrição. Se já tenha registado o fornecedor com a sua subscrição, avance para o passo seguinte.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Crie um cofre dos Serviços de Recuperação com **New-AzureRmRecoveryServicesVault**. Lembre-se de que especifique o nome do grupo de recursos e localização utilizados ao configurar a máquina virtual que pretende criar cópias de segurança. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Cmdlets de cópia de segurança do Azure muitos requerem o objeto de cofre dos serviços de recuperação como entrada. Por este motivo, é conveniente armazenar o objeto de cofre dos serviços de recuperação de cópia de segurança numa variável. Em seguida, utilize **conjunto AzureRmRecoveryServicesBackupProperties** para definir o **- BackupStorageRedundancy** opção para [armazenamento Georredundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Fazer cópia de segurança de máquinas virtuais do Azure

Antes de poder executar a cópia de segurança inicial, tem de definir o contexto do cofre. O contexto de cofre é o tipo de dados protegidos no cofre. Quando criar um cofre dos serviços de recuperação, este inclui proteção predefinida e as políticas de retenção. A política de proteção predefinido aciona uma tarefa de cópia de segurança por dia num momento especificado. A política de retenção predefinido mantém o ponto de recuperação diários 30 dias. Para este tutorial, aceite a política predefinida. 

Utilize  **[conjunto AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  para definir o contexto do cofre. Depois do contexto de cofre for definido, aplica-se a todos os cmdlets subsequentes. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Utilize  **[AzureRmRecoveryServicesBackupItem de cópia de segurança](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**  para acionar a tarefa de cópia de segurança. A tarefa de cópia de segurança cria um ponto de recuperação. Se estiver a cópia de segurança inicial, o ponto de recuperação é uma cópia de segurança completa. Cópias de segurança subsequentes criem uma cópia incremental.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Elimine o Cofre dos serviços de recuperação

Para eliminar um cofre dos serviços de recuperação, tem de eliminar primeiro quaisquer pontos de recuperação no cofre e, em seguida, anular o registo do cofre. Os seguintes comandos de detalhe estes passos. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros
Caso se depare com problemas ao efetuar uma cópia de segurança da máquina virtual, consulte o [cópia de segurança do Azure máquinas de virtuais artigo de resolução de problemas](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Passos seguintes
Agora que que protege as máquinas virtuais, consulte os artigos seguintes para saber mais sobre tarefas de gestão e como restaurar máquinas virtuais a partir de um ponto de recuperação.

* Para modificar a política de cópia de segurança, consulte [AzureRM.RecoveryServices.Backup de utilização de cmdlets para fazer uma cópia de segurança de máquinas virtuais](backup-azure-vms-automation.md#create-a-protection-policy).
* [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md)
* [Monitorizar máquinas virtuais](backup-azure-arm-restore-vms.md)
