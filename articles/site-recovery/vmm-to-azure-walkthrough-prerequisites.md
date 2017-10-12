---
title: "Rever os pré-requisitos para a replicação de Hyper-V para o Azure (com o System Center VMM) com o Azure Site Recovery | Microsoft Docs"
description: "Descreve os pré-requisitos para configurar a replicação, a ativação pós-falha e a recuperação de VMs de Hyper-V no local em clouds do VMM para o Azure com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>Passo 2: Rever os pré-requisitos do Hyper-V (com o VMM) para a replicação do Azure

Depois de rever a [arquitetura do cenário](vmm-to-azure-walkthrough-architecture.md), leia este artigo para ter a certeza de que compreende os pré-requisitos das implementações. 

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | Precisa de uma [conta do Microsoft Azure](http://azure.microsoft.com/).
**Armazenamento do Azure** | Precisa de uma conta de armazenamento do Azure para armazenar dados replicados.<br/><br/> A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação do Azure.<br/><br/>Pode utilizar o [armazenamento georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) ou o armazenamento localmente redundante. Recomendamos o armazenamento georredundante. Com o armazenamento georredundante, os dados são resilientes se ocorrer uma falha regional ou se a região primária não puder ser recuperada.<br/><br/> Pode utilizar uma conta de armazenamento do Azure standard ou utilizar o [armazenamento premium](../storage/common/storage-premium-storage.md) do Azure. O armazenamento premium pode alojar cargas de trabalho de E/S intensivo e é, geralmente, utilizado em VMs que precisam de um desempenho de E/S consistentemente alto e baixa latência. Se utilizar o armazenamento premium para os dados replicados, também vai precisar de uma conta de armazenamento standard. Esta conta armazena os registos de replicação que capturam alterações contínuas aos dados no local.
**Rede do Azure** | Precisa de uma [rede do Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md) à qual as VMs do Azure se possam ligar após a ativação pós-falha. A rede do Azure tem de estar na mesma região que o cofre dos Serviços de Recuperação.
**Servidores VMM no local** | Precisa de um ou mais servidores VMM em execução no System Center 2012 R2 ou posterior.<br/><br/> Cada servidor VMM tem de ter uma ou mais clouds privadas. Cada cloud precisa de um ou mais grupos de anfitriões.<br/><br/> O servidor VMM precisa de acesso à Internet.
**Hyper-V no local** | Os servidores anfitriões de Hyper-V têm de executar, pelo menos, o Windows Server 2012 R2 com a função Hyper-V ativada ou o Microsoft Hyper-V Server 2012 R2. Têm de estar instaladas as atualizações mais recentes.<br/><br/> O anfitrião Hyper-V tem de estar localizado num grupo de anfitriões do VMM (localizado numa cloud do VMM).<br/><br/> Um anfitrião tem de ter uma ou mais VMs que pretende replicar.<br/><br/> Os anfitriões Hyper-V têm de estar ligados à Internet para replicação no Azure, diretamente ou com um proxy. Os servidores Hyper-V têm de ter as correções descritas no artigo [2961977](https://support.microsoft.com/kb/2961977).
**VMs de Hyper-V no local** | As VMs que pretender replicar devem executar um [sistema operativo suportado](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). O nome da VM pode ser modificado quando a replicação estiver ativada. 




## <a name="next-steps"></a>Passos seguintes

Avance para o [Passo 3: Planear a capacidade](vmm-to-azure-walkthrough-capacity.md)
