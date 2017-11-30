---
title: "Preparar máquinas para configurar a recuperação após desastre entre regiões do Azure após a migração para o Azure utilizando a recuperação de Site | Microsoft Docs"
description: "Este artigo descreve como preparar máquinas para configurar a recuperação após desastre entre regiões do Azure após a migração para o Azure utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: ponatara
ms.openlocfilehash: 2943d33744a29da7ffd1f1544fc81696195ecce1
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Replicar VMs do Azure para noutra região após a migração para o Azure utilizando o Azure Site Recovery

>[!NOTE]
> Azure Site Recovery replicação para máquinas de virtuais (VMs) do Azure está atualmente em pré-visualização.

## <a name="overview"></a>Descrição geral

Este artigo ajuda-o a preparar máquinas virtuais do Azure para a replicação entre duas regiões do Azure depois destas máquinas tiverem sido migradas do ambiente no local para o Azure utilizando o Azure Site Recovery.

## <a name="disaster-recovery-and-compliance"></a>Recuperação após desastre e conformidade
Hoje, as empresas mais estiver a mover as cargas de trabalho para o Azure. Com que as empresas mover fundamentais no local as cargas de trabalho de produção para o Azure, ao configurar a recuperação após desastre para estas cargas de trabalho é obrigatório para a compatibilidade e para salvaguardar contra quaisquer interrupções na região do Azure.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Passos para preparar máquinas migradas para a replicação
Para preparar migrou máquinas para configurar a replicação para outra região do Azure:

1. Concluir a migração.
2. Instale o agente do Azure, se necessário.
3. Remova o serviço de mobilidade.  
4. Reinicie a VM.

Estes passos são descritos mais detalhadamente nas secções seguintes.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>Passo 1: Migrar cargas de trabalho em execução em VMs Hyper-V, as VMs VMware e servidores físicos para ser executada em VMs do Azure

Para configurar a replicação e efetue a migração no local, Hyper-V, VMware e cargas de trabalho de físicas para o Azure, siga os passos a [máquinas de virtuais do IaaS do Azure migrar entre regiões do Azure com o Azure Site Recovery](site-recovery-migrate-azure-to-azure.md) artigo. 

Após a migração, não precisa de consolidação ou eliminar uma ativação pós-falha. Em vez disso, selecione o **concluir a migração** opção para cada máquina que pretende migrar:
1. Em **Itens Replicados**, clique com o botão direito na VM e clique em **Concluir a Migração**. Clique em **OK** para concluir o passo. Pode controlar o progresso nas propriedades da VM pela monitorização a tarefa de migração completa no **as tarefas de recuperação de Site**.
2. O **concluir a migração** ação conclui o processo de migração, remove a replicação da máquina e deixa de faturação de recuperação de Site para a máquina.

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>Passo 2: Instalar o agente da VM do Azure na máquina virtual
O Azure [agente da VM](../../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux) tem de estar instalado na máquina virtual para a extensão de recuperação de sites para funcionar e para ajudar a proteger a VM.

>[!IMPORTANT]
>O instalador do serviço de mobilidade a partir da versão 9.7.0.0, nas máquinas de virtuais do Windows, instala também o agente de VM do Azure mais recente disponível. Na migração, a máquina virtual cumpre a pré-requisitos para a utilização de qualquer extensão VM, incluindo a extensão de recuperação de Site a instalação do agente. O agente de VM do Azure tem de ser instalado manualmente apenas se o serviço de mobilidade instalado na máquina migrada versão 9.6 ou anterior.

A tabela seguinte fornece informações adicionais sobre como instalar o agente da VM e a validar que tenha sido instalado:

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalar o agente da VM |Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necessita de privilégios de administrador para concluir a instalação. |Instalar a versão mais recente [agente Linux](../../virtual-machines/linux/agent-user-guide.md). Necessita de privilégios de administrador para concluir a instalação. Recomendamos que instale o agente do seu repositório de distribuição. Iremos *não é recomendada a* instalar o agente de VM com Linux diretamente a partir do GitHub.  |
| A validar a instalação do agente VM |1. Procure a pasta de C:\WindowsAzure\Packages na VM do Azure. Deverá ver o ficheiro WaAppAgent.exe. <br>2. Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O **versão do produto** campo deve ser 2.6.1198.718 ou superior. |N/D |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>Passo 3: Remover o serviço de mobilidade da máquina virtual migrada

Se tiver migrado sua máquinas de VMware no local ou servidores físicos Windows/Linux, tem de remover/desinstalar manualmente o serviço de mobilidade da máquina virtual migrada.

>[!IMPORTANT]
>Este passo não é necessário para as VMs de Hyper-V migrados para o Azure.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Desinstale o serviço de mobilidade na VM do Windows Server
Utilize um dos seguintes métodos para desinstalar o serviço de mobilidade num computador Windows Server.

##### <a name="uninstall-by-using-the-windows-ui"></a>Desinstalar através da IU do Windows
1. No painel de controlo, selecione **programas**.
2. Selecione **servidor de destino mestre do serviço de mobilidade recuperação Microsoft Azure Site**e, em seguida, selecione **desinstalação**.

##### <a name="uninstall-at-a-command-prompt"></a>Desinstalar uma linha de comandos
1. Abra uma janela de linha de comandos como administrador.
2. Para desinstalar o serviço de mobilidade, execute o seguinte comando:

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Desinstale o serviço de mobilidade num computador com Linux
1. No seu servidor Linux, inicie sessão como um **raiz** utilizador.
2. Num terminal, aceda a /user/local/ASR.
3. Para desinstalar o serviço de mobilidade, execute o seguinte comando:

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>Passo 4: Reiniciar a VM

Depois de desinstalar o serviço de mobilidade, reinicie a VM antes de configurar a replicação para outra região do Azure.


## <a name="next-steps"></a>Passos seguintes
- Começar a proteger as cargas de trabalho por [replicar máquinas virtuais do Azure](../azure-to-azure-quickstart.md).
- Saiba mais sobre [redes orientações para replicar máquinas virtuais do Azure](../site-recovery-azure-to-azure-networking-guidance.md).
