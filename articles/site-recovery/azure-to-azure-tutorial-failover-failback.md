---
title: "Efetuar a ativação pós-falha e falhar back-VMs do Azure são replicados para uma região do Azure secundária com o Azure Site Recovery (pré-visualização)"
description: "Saiba como efetuar a ativação pós-falha e falhar back replicação de VMs do Azure para uma região secundária do Azure com o Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 02b709bb8dbab5d10ce9f4cf6155ff26ce229298
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Ativação pós-falha e falhar novamente Azure VMs entre regiões do Azure (pré-visualização)

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre, gerir e da orquestração de replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial descreve como efetuar a ativação pós-falha numa única VM do Azure para uma região secundária do Azure. Depois de ter a efetuar a ativação pós-falha, falhar novamente para a região primária quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Efetuar a ativação pós-falha da VM do Azure
> * Voltar a proteger a VM do Azure secundária, para que replica para a região primária
> * Falhar novamente a VM secundária
> * Voltar a proteger a VM principal para a região secundária

## <a name="prerequisites"></a>Pré-requisitos

- Disponibilizar concluiu uma [exercício de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md) para verificar que tudo está a funcionar conforme esperado.
- Verifique as propriedades da VM antes de executar a ativação pós-falha de teste. A VM tem de cumprir [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="run-a-failover-to-the-secondary-region"></a>Executar uma ativação pós-falha para a região secundária

1. No **replicado itens**, selecione a VM que pretende efetuar a ativação pós-falha > **ativação pós-falha**

   ![Ativação pós-falha](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. No **ativação pós-falha**, selecione um **ponto de recuperação** para efetuar a ativação pós-falha. Pode utilizar uma das seguintes opções:

   * **Mais recente** (predefinição): esta opção processa todos os dados no serviço de recuperação de Site e fornece o objetivo de ponto de recuperação (RPO) mais baixa.
   * **Mais recentes processados**: esta opção reverte a máquina virtual para o ponto de recuperação mais recente que tenha sido processada pelo serviço de recuperação de sites.
   * **Personalizada**: Utilize esta opção para efetuar a ativação pós-falha para um ponto de recuperação específico. Esta opção é útil para efetuar uma ativação pós-falha de teste.

3. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que a recuperação de sites para tentar efetuar um encerramento de máquinas virtuais de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo se falha de encerramento.

4. Siga o progresso de ativação pós-falha no **tarefas** página.

5. Após a ativação pós-falha, valide a máquina virtual através do registo mesmo. Se pretender voltar a outro ponto de recuperação para a máquina virtual, em seguida, pode utilizar **alterar o ponto de recuperação** opção.

6. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **consolidar** a ativação pós-falha.
   Consolidar elimina todos os pontos de recuperação disponíveis com o serviço. O **alterar o ponto de recuperação** opção já não está disponível.

## <a name="reprotect-the-secondary-vm"></a>Voltar a proteger a VM secundária

Após a ativação pós-falha da VM, terá de voltar a proteger, para que este replica novamente para a região primária.

1. Certifique-se de que a VM está a ser o **ativação pós-falha consolidada** estado e verifique se a região primária está disponível e poderá criar e aceder a recursos de novo no mesmo.
2. No **cofre** > **replicado itens**, faça duplo clique a VM é foi efetuada a ativação pós-falha e, em seguida, selecione **voltar a proteger**.

   ![Faça duplo clique para voltar a proteger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Tenha em atenção que a direção da proteção secundária para a região primária, já está selecionada.
3. Reveja o **grupo de recursos, conjuntos de disponibilidade, armazenamento e rede** informações. Quaisquer recursos marcados (novo) são criados como parte da operação de reproteção.
4. Clique em **OK** para acionar uma tarefa de reproteção. Esta tarefa implementa o site de destino com os dados mais recentes. Em seguida, replica as diferenças para a região primária. A VM está agora num estado protegido.

## <a name="fail-back-to-the-primary-region"></a>Falhar novamente para a região primária

Depois de são proteger VMs, pode falhar novamente para a região primária, conforme necessário para. Para tal, siga o [ativação pós-falha](#run-a-failover) instruções.
