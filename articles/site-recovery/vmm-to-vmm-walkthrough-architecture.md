---
title: "Rever a arquitetura da replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo apresenta uma descrição geral da arquitetura para replicar VMs Hyper-V no local para um site do System Center VMM secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>Passo 1: Rever a arquitetura da replicação do Hyper-V para um site secundário

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais (VMs) de Hyper-V no local em clouds do System Center Virtual Machine Manager (VMM) para um site de VMM secundário com o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Publique comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componentes da arquitetura

Segue-se aquilo de que precisa para replicar VMs de Hyper-V para um site de VMM secundário.

**Componente** | **Localização** | **Detalhes**
--- | --- | ---
**Azure** | Subscrição do Azure. | Criar um cofre dos Serviços de Recuperação na subscrição do Azure para orquestrar e gerir a replicação entre localizações do VMM.
**Servidor VMM** | Precisa de uma localização primária e secundária do VMM. | Recomenda-se um servidor VMM no site primário e um no site secundário 
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. | Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | No servidor de anfitrião Hyper-V. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

## <a name="replication-process"></a>Processo de replicação

1. Tem de configurar a conta do Azure, criar um cofre dos Serviços de Recuperação e especificar o que quer replicar.
2. Tem de configurar as definições de replicação de origem e destino, as quais incluem a instalação do Fornecedor do Azure Site Recovery nos servidores VMM e o agente dos Serviços de Recuperação do Microsoft Azure em cada anfitrião Hyper-V.
3. Tem de criar uma política de replicação para a cloud do VMM de origem. A política é aplicada a todas as VMs localizadas nos anfitriões na cloud.
4. Tem de ativar a replicação para cada VMM e a replicação inicial de uma VM tem de ocorrer de acordo com as definições que escolher.
5. Após a replicação inicial, começa a replicação das alterações delta. As alterações registadas relativas a um item são guardadas num ficheiro .hrl.


![Local para local](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma [ativação pós-falha](site-recovery-failover.md) planeada ou não planeada entre os sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar a ativação pós-falha de várias máquinas.
4. Se fizer uma ativação pós-falha não planeada para um site secundário, as máquinas na localização secundária não estão ativadas para proteção ou replicação após a ativação pós-falha. Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
5. Em seguida, consolida a ativação pós-falha para começar a aceder à carga de trabalho da VM de réplica.
6. Quando o site primário estiver novamente disponível, inicia a replicação inversa, para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
7. Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.



## <a name="next-steps"></a>Passos seguintes

Vá para o [Passo 2: Rever os pré-requisitos e as limitações](vmm-to-vmm-walkthrough-prerequisites.md).
