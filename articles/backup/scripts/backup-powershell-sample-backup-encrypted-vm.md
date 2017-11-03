---
title: "Exemplo de Script do PowerShell do Azure - cópia de segurança uma máquina virtual do Azure | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - cópia de segurança uma máquina virtual do Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4376add4a2e51806bd5db228ad2fe2afcf2e4f57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Fazer uma cópia de segurança de uma máquina virtual do Azure encriptada com o PowerShell

Este script cria um cofre dos serviços de recuperação com armazenamento georredundante (GRS) para uma máquina virtual do Azure encriptado. A política de proteção predefinida é aplicada ao cofre. A política gera uma cópia de segurança diária para a máquina virtual e retém cada cópia de segurança durante 30 dias. O script também aciona o ponto de recuperação inicial da máquina virtual e mantém esse ponto de recuperação para a 365 dias. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar a implementação. Cada item nas ligações de tabela para a documentação específica do comando.


| Comando | Notas | 
|---|---| 
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. | 
| [Novo AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Cria um cofre dos serviços de recuperação para armazenar as cópias de segurança. | 
| [Conjunto AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Conjuntos de cópia de segurança propriedades de armazenamento no Cofre de serviços de recuperação. | 
| [Novo AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Cria uma política de proteção utilizando a política de agendamento e a política de retenção no Cofre de serviços de recuperação. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Define as permissões no Cofre de chaves para conceder o acesso de principal de serviço para as chaves de encriptação. | 
| [Ativar AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Permite a cópia de segurança para um item com uma política de proteção de cópia de segurança especificado. | 
| [Conjunto AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de cópia de segurança existente. | 
| [Cópia de segurança AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Inicia uma cópia de segurança para um item de cópia de segurança do Azure protegido que não está associado ao agendamento de cópia de segurança. |
| [Espera-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Aguarda uma tarefa de cópia de segurança do Azure concluir. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos. | 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

