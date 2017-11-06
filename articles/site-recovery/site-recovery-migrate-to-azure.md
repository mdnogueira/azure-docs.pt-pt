---
title: "Migrar VMs no local e servidores físicos para o Azure com o Site Recovery | Microsoft Docs"
description: "Este artigo descreve como migrar VMs no local e servidores físicos para o Azure, com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Migrar para o Azure com o Site Recovery

Leia este artigo para saber mais sobre como utilizar o serviço do [Azure Site Recovery](site-recovery-overview.md) para migrar máquinas virtuais (VMs) no local e servidores físicos para VMs do Azure.

## <a name="before-you-start"></a>Antes de começar

Veja este vídeo para uma descrição geral dos passos necessários para migrar para o Azure.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer com migração?

Pode implementar o Site Recovery para a replicação de VMs no local e servidores físicos e para os migrar.

- Ao replicar, pode configurar máquinas no local para que repliquem regularmente para o Azure. Quando ocorrer uma falha, pode então efetuar a ativação pós-falha das máquinas a partir do site no local para o Azure, e aceder-lhes a partir daí. Quando o site no local estiver novamente disponível, faça a reativação pós-falha a partir do Azure.
- Quando utiliza o Site Recovery para migração, as máquinas no local são replicadas para o Azure. Efetua então a respetiva ativação pós-falha a partir do site no local para o Azure, e conclui o processo de migração. Não há nenhuma reativação pós-falha envolvida.  

## <a name="what-can-site-recovery-migrate"></a>O que pode ser migrado com o Site Recovery?

Pode:

- **Migrar do local**: migre VMs de Hyper-V no local, VMs de VMware e servidores físicos para o Azure. Após a migração, as cargas de trabalho em execução nas máquinas no local vão estar em execução em VMs do Azure. 
- **Migrar no Azure**: migre VMs do Azure entre regiões do Azure. 
- **Migrar AWS**: migre instâncias do Windows AWS para VMs IaaS do Azure. 

## <a name="migrate-from-on-premises-to-azure"></a>Migrar do local para o Azure

Para migrar VMs de VMware, VMs de Hyper-V no local e servidores físicos, tem de seguir quase os mesmos passos que faria para a replicação completa. 


## <a name="migrate-between-azure-regions"></a>Migrar entre regiões do Azure

Para migrar VMs do Azure entre regiões, siga quase os mesmos passos, tal como faria para migração completa.

1. [Ative a replicação](azure-to-azure-tutorial-enable-replication.md) nas máquinas que quer migrar.
2. [Execute uma ativação pós-falha rápida de teste](azure-to-azure-tutorial-dr-drill.md), para confirmar que está tudo a funcionar.
3. Em seguida, [execute uma ativação pós-falha não planeada](azure-to-azure-tutorial-failover-failback.md) com a opção **Concluir Migração**.
4. Depois de concluir a migração, pode [configurar a replicação para a recuperação após desastre](site-recovery-azure-to-azure-after-migration.md), da região do Azure para a qual migrou, para uma região secundária.



## <a name="migrate-aws-to-azure"></a>Migrar o AWS para o Azure

Pode migrar instâncias do AWS para VMs do Azure.
- Neste cenário, só a migração é suportada. Por outras palavras, pode replicar as instâncias do AWS e fazer a ativação pós-falha das mesmas para o Azure, mas não a reativação pós-falha.
- Para fins de migração, as instâncias do AWS são tratadas da mesma forma que os servidores físicos. Configura um cofre dos Serviços de Recuperação, implementa um servidor de configuração no local para gerir a replicação, adiciona-o ao cofre e especifica as definições da replicação.
- Ativa a replicação nas máquinas que quer migrar e executa uma ativação pós-falha de teste rápida. Em seguida, executa uma ativação pós-falha não planeada com a opção **Concluir Migração**.






## <a name="next-steps"></a>Passos seguintes

- [Migrar máquinas no local para o Azure](tutorial-migrate-on-premises-to-azure.md)
- [Migrar VMs de uma região do Azure para outra](site-recovery-migrate-azure-to-azure.md)
- [Migrar o AWS para o Azure](tutorial-migrate-aws-to-azure.md)
