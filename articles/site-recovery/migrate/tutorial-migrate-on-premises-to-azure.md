---
title: "Migrar máquinas no local para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como migrar as máquinas no local para o Azure, utilizando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/27/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: e268a6d53a9f0b001ad0da8c9ce7ea1a9046960c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas no local para o Azure

O [do Azure Site Recovery](../site-recovery-overview.md) serviço gere e orquestra a replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial mostra como migrar VMs no local e servidores físicos para o Azure, com a recuperação de Site. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar os pré-requisitos para a implementação
> * Criar um cofre dos serviços de recuperação para a recuperação de sites
> * Implementar servidores de gestão no local
> * Configurar uma política de replicação e ativar a replicação
> * Executar um exercício de recuperação de desastres para Certifique-se de que tudo está a funcionar
> * Executar uma ativação pós-falha única para o Azure

## <a name="overview"></a>Descrição geral

Migrar uma máquina ao ativar a replicação para a mesma e efetuar a ativação pós-falha para o Azure.


## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa de fazer para este tutorial.

- [Preparar](../tutorial-prepare-azure.md) recursos do Azure, incluindo uma subscrição do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- [Preparar](../tutorial-prepare-on-premises-vmware.md) VMware no local servidores VMware e VMs.
- Tenha em atenção que não são suportados exportados pelo paravirtualized controladores de dispositivos.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

[!INCLUDE [site-recovery-create-vault](../../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecione um objetivo de proteção

Selecione o que pretende replicar e para onde.
1. Clique em **cofres dos serviços de recuperação** > cofre.
2. No Menu de recursos, clique em **recuperação de Site** > **preparar infraestrutura** > **objetivo de proteção**.
3. No **objetivo de proteção**, selecione:
    - **VMware**: selecione **para o Azure** > **Sim, com o VMWare vSphere hipervisor**.
    - **Máquina física**: selecione **para o Azure** > **não virtualizados/outras**.
    - **Hyper-V**: selecione **para o Azure** > **Sim, com o Hyper-V**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

- [Configurar](../tutorial-vmware-to-azure.md#set-up-the-source-environment) o ambiente de origem para as VMs de VMware.
- [Configurar](../tutorial-physical-to-azure.md#set-up-the-source-environment) o ambiente de origem para servidores físicos.
- [Configurar](../tutorial-hyper-v-to-azure.md#set-up-the-source-environment) o ambiente de origem para as VMs de Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e certifique-se de recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

- [Configurar uma política de replicação](../tutorial-vmware-to-azure.md#create-a-replication-policy) para VMware VMs.


## <a name="enable-replication"></a>Ativar a replicação

- [Ativar a replicação](../tutorial-vmware-to-azure.md#enable-replication) para VMware VMs.


## <a name="run-a-test-migration"></a>Executar uma migração de teste

Executar um [ativação pós-falha de teste](../tutorial-dr-drill-azure.md) para o Azure, certificar-se de que tudo está a funcionar conforme esperado.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute uma ativação pós-falha para as máquinas que pretende migrar.

1. No **definições** > **replicado itens** clique na máquina > **ativação pós-falha**.
2. No **ativação pós-falha** selecionar um **ponto de recuperação** para efetuar a ativação pós-falha. Selecione o ponto de recuperação mais recente.
3. A definição de chave de encriptação não é relevante para este cenário.
4. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que a recuperação de sites para tentar efetuar um encerramento de máquinas virtuais de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo se falha de encerramento. Pode seguir o progresso de ativação pós-falha no **tarefas** página.
5. Certifique-se de que a VM do Azure é apresentada no Azure conforme esperado.
6. No **replicado itens**, clique com o botão direito a VM > **concluir a migração**. Isto termina o processo de migração, deixa de replicação para a VM e deixa de faturação de recuperação de Site para a VM.

    ![Concluir a migração](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: replicação de VM está parada antes de começa a ativação pós-falha. Se cancelar uma ativação pós-falha em curso, deixa de ativação pós-falha, mas a VM não irá replicar novamente.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. É possível que repare já tempos de ativação pós-falha para servidores físicos, VMware Linux máquinas, VMs de VMware que não tenham o permite o serviço DHCP e as VMs de VMware que não têm os seguintes controladores de arranque de teste: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Replicar VMs do Azure para noutra região após a migração para o Azure](site-recovery-azure-to-azure-after-migration.md)
