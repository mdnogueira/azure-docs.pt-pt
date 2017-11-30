---
title: "Migrar VMs IaaS do Azure entre regiões do Azure | Microsoft Docs"
description: "Utilize o Azure Site Recovery para migrar máquinas virtuais do IaaS do Azure de uma região do Azure para outro."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais de IaaS do Azure entre regiões do Azure com o Azure Site Recovery

Utilize este artigo se pretender migrar máquinas virtuais do Azure (VMs) entre regiões do Azure, utilizando o [recuperação de Site](../site-recovery-overview.md) serviço.

## <a name="prerequisites"></a>Pré-requisitos

Terá de VMs de IaaS que pretende migrar.

## <a name="deployment-steps"></a>Passos da implementação

1. [Criar um cofre](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Ativar a replicação](azure-to-azure-tutorial-enable-replication.md#enable-replication) para as VMs que pretende migrar e escolha do Azure como origem. Atualmente, a replicação nativa de VMs do Azure através de discos geridos não é suportada.
3. [Executar uma ativação pós-falha](../site-recovery-failover.md). Após a conclusão da replicação inicial, pode executar uma ativação pós-falha de uma região do Azure para outro. Opcionalmente, pode criar um plano de recuperação e executar uma ativação pós-falha, para migrar várias máquinas virtuais entre regiões. [Saiba mais](../site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre outros cenários de replicação no [que é o Azure Site Recovery?](../site-recovery-overview.md)
