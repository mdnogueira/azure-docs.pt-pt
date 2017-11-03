---
title: "Pré-requisitos para replicação no Azure utilizando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo resume pré-requisitos para replicar VMs e máquinas físicas no Azure utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Pré-requisitos para replicar máquinas virtuais do Azure para noutra região, utilizando o Azure Site Recovery

> [!div class="op_single_selector"]
> * [Replicar a partir do Azure para o Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replicar no local para o Azure](site-recovery-prereq.md)

O serviço do Azure Site Recovery contribui para a sua estratégia de recuperação (BCDR) de continuidade e desastre negócio através da orquestração:
* Replicação de máquinas virtuais do Azure para outra região do Azure.
* Replicação de servidores físicos no local e as máquinas virtuais no Azure ou para um datacenter secundário. 

Quando ocorrem falhas na sua localização principal, pode efetuar a ativação pós-falha para uma localização secundária para manter as aplicações e cargas de trabalho disponíveis. Pode efetuar a sua localização principal quando se retomam as operações normais. Para mais informações sobre a recuperação de sites, consulte [que é o Site Recovery?](site-recovery-overview.md).

Este artigo resume os pré-requisitos necessários para iniciar a replicação de recuperação de sites no local para o Azure.

Publique quaisquer comentários na parte inferior do artigo, ou coloque questões técnicas no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisitos do Azure

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | A [Microsoft Azure](http://azure.microsoft.com/) conta.<br/><br/> Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**O serviço de recuperação do site** | Para obter mais informações sobre os preços da recuperação de sites, consulte [preços da recuperação de Site](https://azure.microsoft.com/pricing/details/site-recovery/). Recomendamos que crie um cofre dos serviços de recuperação na região do Azure que pretende utilizar como uma localização de recuperação após desastre de destino. Por exemplo, se as VMs de origem estão em execução nos EUA leste e que pretende replicar para EUA Central, recomendamos que crie o Cofre nos EUA Central.|
**Capacidade do Azure** | Para a região do Azure que pretende utilizar como a localização de recuperação após desastre de destino, tem de ter uma subscrição com capacidade suficiente para as máquinas virtuais, contas de armazenamento e componentes de rede. Pode contactar o suporte para adicionar mais capacidade.
**Orientações de armazenamento** | Certifique-se de que segue o [orientações de armazenamento](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) para a origem de Azure máquinas virtuais para evitar quaisquer problemas de desempenho. Se seguir as predefinições, a recuperação de sites cria as contas de armazenamento necessários com base na configuração da origem. Se personalizar e selecione as suas próprias definições, certifique-se de que segue o [metas de escalabilidade para discos da máquina virtual](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Documentação de orientação do funcionamento em rede** | Terá de lista branca de conectividade de saída da sua VM do Azure para intervalos de IP ou URLs específicas. Para obter mais detalhes, consulte o [redes orientações para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md) artigo.
**VM do Azure** | Certifique-se de que todos os certificados de raiz mais recentes do Windows ou uma VM com Linux. Se os certificados de raiz mais recentes não estiverem presentes, não é possível registar a VM para a recuperação de sites, devido a restrições de segurança.

>[!NOTE]
>Para obter mais detalhes sobre o suporte para configurações específicas, leia o [matriz de suporte](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [redes orientações para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Começar a proteger as cargas de trabalho por [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
