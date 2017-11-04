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
ms.openlocfilehash: 86806c5dbafc1fd88c434dcee6292683d050cd2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais de IaaS do Azure entre regiões do Azure com o Azure Site Recovery
## <a name="overview"></a>Descrição geral
Bem-vindo ao Azure Site Recovery! Utilize este artigo se pretender migrar VMs do Azure entre regiões do Azure.
>[!NOTE]
>
> Para replicar as VMs do Azure para outra região para as necessidades de migração e de recuperação após desastre, consulte [neste documento](site-recovery-azure-to-azure.md). Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em pré-visualização.

Antes de começar, tenha em atenção que:

* O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: o Azure Resource Manager e clássico. O Azure também tem dois portais – o Portal Clássico do Azure que suporta o modelo de implementação clássica, e o Portal do Azure com suporte para ambos os modelos de implementação. Os passos básicos para migração são os mesmos se estiver a configurar a recuperação de Site no Gestor de recursos ou em clássico. No entanto, as instruções da IU e capturas de ecrã neste artigo são relevantes para o portal do Azure.



Publique comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos
Eis o que precisa para esta implementação:

* **Máquinas virtuais de IaaS**: A VMs que pretende migrar. Migrar estas VMs por encará-los como máquinas físicas.

## <a name="deployment-steps"></a>Passos da implementação
Esta secção descreve os passos de implementação no portal do Azure.

1. [Criar um cofre](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Ativar a replicação](site-recovery-azure-to-azure.md) para as VMs que pretende migrar e escolha o Azure como origem.
  >[!NOTE]
  >
  > Atualmente, a replicação nativa de VMs do Azure através de discos geridos não são suportadas. Pode utilizar a opção "Físico para o Azure" na [neste documento](site-recovery-vmware-to-azure.md) migrar VMs com discos geridos.
3. [Executar uma ativação pós-falha](site-recovery-failover.md). Após a conclusão da replicação inicial, pode executar uma ativação pós-falha de uma região do Azure para outro. Opcionalmente, pode criar um plano de recuperação e executar uma ativação pós-falha, para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre outros cenários de replicação no [que é o Azure Site Recovery?](site-recovery-overview.md)
