---
title: "Eliminar um cofre de recuperação de sites"
description: "Saiba como eliminar um cofre de recuperação de sites do Azure, com base no cenário de recuperação de sites."
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: 77bc7e068629c5dbf650b39844912b1b7a6155b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="delete-a-site-recovery-vault"></a>Eliminar um cofre de recuperação de sites
Dependências que podem impedir eliminar um cofre do Azure Site Recovery. As ações que precisa de tomar variam com base no cenário de recuperação de sites: VMware para o Azure, o Hyper-V (com e do System Center Virtual Machine Manager) para o Azure e cópia de segurança do Azure. Para eliminar um cofre utilizado na cópia de segurança do Azure, consulte [eliminar um cofre de cópia de segurança no Azure](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Eliminar um cofre de recuperação de sites 
Para eliminar o cofre, siga os passos recomendados para o seu cenário.

### <a name="vmware-vms-to-azure"></a>VMs do VMware para o Azure

1. Eliminar todos os protegidos VMs, seguindo os passos no [desative a proteção de um VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Eliminar todas as políticas de replicação ao seguir os passos em [eliminar uma política de replicação](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Eliminar referências ao vCenter seguindo os passos no [eliminar um vCenter](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).

4. Eliminar o servidor de configuração seguindo os passos no [desativar um servidor de configuração](site-recovery-vmware-to-azure-manage-configuration-server.md##delete-or-unregister-a-configuration-server).

5. Elimine o cofre.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>VMs de Hyper-V (com o Virtual Machine Manager) para o Azure
1. Eliminar todos os protegidos VMs, seguindo os passos no[desative a proteção para uma máquina virtual de Hyper-V replicar no Azure utilizando o System Center VMM para Azure cenário](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Desassocie & Eliminar todas as políticas de replicação ao navegar para o Cofre -> **infraestrutura de recuperação de Site** -> **para o System Center VMM** -> **replicação Políticas**

3.  Eliminar referências aos servidores do Virtual Machine Manager seguindo os passos no [anular o registo de um ligado ao servidor VMM](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Elimine o cofre.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>VMs de Hyper-V (do Virtual Machine Manager) para o Azure
1. Eliminar todos os protegidos VMs, seguindo os passos no [desative a proteção para uma máquina virtual de Hyper-V (Hyper-V para o Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Desassocie & Eliminar todas as políticas de replicação ao navegar para o Cofre -> **infraestrutura de recuperação de Site** -> **para Sites de Hyper-V** -> **políticas de replicação**

3. Eliminar referências para servidores Hyper-V, seguindo os passos no [anular o registo de um anfitrião Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Elimine o site Hyper-V.

5. Elimine o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Utilize o PowerShell para forçar a eliminar o Cofre 

> [!Important]
> Se estiver a testar o produto e não estão preocupados com a perda de dados, utilize o método delete de force para remover rapidamente o Cofre e as respetivas dependências.
> O comando do PowerShell elimina todos os conteúdos do cofre e **não reversível**.

Para eliminar o Cofre de recuperação de sites, mesmo se não existirem itens protegidos, utilize estes comandos:

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault