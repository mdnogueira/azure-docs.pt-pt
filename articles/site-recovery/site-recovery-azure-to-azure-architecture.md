---
title: "Como funciona a replicação de máquina virtual do Azure entre regiões do Azure no Azure Site Recovery?  | Microsoft Docs"
description: "Este artigo fornece uma descrição geral dos componentes e arquitetura utilizada quando replicar VMs do Azure entre regiões do Azure utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 7a3e1eb129db530295cbf28bbce1dbe48575d50d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>Como funciona a replicação de VM do Azure no Site Recovery?


Este artigo descreve os componentes e processos envolvido na replicação e recuperação de máquinas virtuais do Azure (VMs) de uma região para outro utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.

>[!NOTE]
>Replicação de VM do Azure com o serviço de recuperação de sites está atualmente em pré-visualização.

Publique comentários na parte inferior deste artigo ou coloque questões no [Azure Recovery Services Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (Fórum dos Serviços de Recuperação do Azure).

## <a name="architectural-components"></a>Componentes da arquitetura

O diagrama seguinte fornece uma vista de alto nível de um ambiente de VM do Azure numa região específica (neste exemplo, a localização EUA Leste). Num ambiente VM do Azure:
- As aplicações podem estar em execução em VMs com discos distribuídos por contas de armazenamento.
- As VMs podem ser incluídas numa ou mais sub-redes dentro de uma rede virtual.

![ambiente de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Saiba mais sobre os pré-requisitos de implementação e requisitos de [matriz de suporte](site-recovery-support-matrix-azure-to-azure.md).

## <a name="replication-process"></a>Processo de replicação

### <a name="step-1"></a>Passo 1

Ao ativar a replicação de VM do Azure no portal do Azure, os recursos mostrados no diagrama seguinte e tabela são criados automaticamente na região de destino. Por predefinição, os recursos são criados com base nas definições de região de origem. Pode personalizar as definições de destino, conforme necessário. [Saiba mais](site-recovery-replicate-azure-to-azure.md).

![Ativar o processo de replicação, o passo 1](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

**Recurso** | **Detalhes**
--- | ---
**Grupo de recursos de destino** | O grupo de recursos aos quais as VMs replicadas pertencem após a ativação pós-falha.
**Rede virtual de destino** | A rede virtual no qual as VMs replicadas estão localizadas após a ativação pós-falha. Um mapeamento da rede é criado entre redes virtuais de origem e de destino e vice-versa.
**Contas de armazenamento de cache** | Replicar as alterações em VMs de origem para a conta de armazenamento de destino, são controladas e enviados para a conta de armazenamento de cache na localização de destino. Isto assegura um impacto mínimo no aplicações de produção em execução na VM.
**Contas de armazenamento de destino**  | Contas de armazenamento na localização de destino para o qual os dados são replicados.
**Conjuntos de disponibilidade de destino**  | Conjuntos de disponibilidade na qual as VMs replicadas estão localizadas após a ativação pós-falha.

### <a name="step-2"></a>Passo 2

Como a replicação está ativada, o serviço de mobilidade de extensão de recuperação de Site é instalado automaticamente na VM. Ocorre o seguinte:

1. A VM está registada com a recuperação de Site.

2. Replicação contínua está configurada para a VM. Operações de escrita de dados nos discos de VM continuamente são transferidas para a conta de armazenamento de cache a localização de origem.

   ![Ativar o processo de replicação, o passo 2](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > Recuperação de site nunca necessita de conectividade de entrada para a VM. A VM tem apenas conectividade de saída para endereços IP/os URLs do serviço de recuperação de sites, endereços IP/os URLs de autenticação do Office 365 e endereços IP de conta de armazenamento de cache. Para obter mais informações, consulte o [orientações de redes para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md) artigo.

## <a name="continuous-replication-process"></a>Processo de replicação contínua

Após a replicação contínua está a funcionar, escritas em disco imediatamente são transferidas para a conta de armazenamento de cache. Recuperação de site processa os dados e envia-a para a conta de armazenamento de destino. Depois dos dados são processados, pontos de recuperação são gerados na conta de armazenamento de destino em alguns minutos.

## <a name="failover-process"></a>Processo de ativação pós-falha

Quando seleciona uma ativação pós-falha, as VMs que são criadas no destino grupo de recursos, rede virtual de destino, a sub-rede de destino, e o destino conjunto de disponibilidade. Durante uma ativação pós-falha, pode utilizar qualquer ponto de recuperação.

![Processo de ativação pós-falha](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [rede](site-recovery-azure-to-azure-networking-guidance.md) para replicação de VM do Azure.
- Siga as instruções para [replicar as VMs do Azure.](site-recovery-azure-to-azure.md)
