---
title: "Reveja a arquitetura da replicação de VMs do Azure entre regiões do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral dos componentes e arquitetura utilizada quando replicar VMs do Azure entre regiões do Azure utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a449969e627c67660973ccf17123958678dd8844
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Arquitetura de replicação do Azure para o Azure


Este artigo descreve a arquitetura e processos utilizados quando replicar, efetuar a ativação pós-falha e recuperar máquinas virtuais do Azure (VMs) entre regiões do Azure, utilizando o [do Azure Site Recovery](../site-recovery-overview.md) serviço.

>[!NOTE]
>Replicação de VM do Azure com o serviço de recuperação de sites está atualmente em pré-visualização.



## <a name="architectural-components"></a>Componentes da arquitetura

O seguinte gráfico fornece uma vista de alto nível de um ambiente de VM do Azure numa região específica (neste exemplo, a localização EUA Leste). Num ambiente VM do Azure:
- As aplicações podem estar em execução em VMs com discos distribuídos por contas de armazenamento.
- As VMs podem ser incluídas numa ou mais sub-redes dentro de uma rede virtual.


**Replicação do Azure para o Azure**

![ambiente de cliente](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Processo de replicação

### <a name="step-1"></a>Passo 1

Ao ativar a replicação de VM do Azure, os recursos abaixo são criados automaticamente na região de destino, com base nas definições de região de origem. Pode personalizar as definições de recursos de destino conforme necessário. 

![Ativar o processo de replicação, o passo 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Recurso** | **Detalhes**
--- | ---
**Grupo de recursos de destino** | O grupo de recursos aos quais as VMs replicadas pertencem após a ativação pós-falha.
**Rede virtual de destino** | A rede virtual no qual as VMs replicadas estão localizadas após a ativação pós-falha. Um mapeamento da rede é criado entre redes virtuais de origem e de destino e vice-versa.
**Contas de armazenamento de cache** | Replicar as alterações de VMs de origem para uma conta de armazenamento de destino, são controladas e enviados para a conta de armazenamento de cache na localização de destino. Isto assegura um impacto mínimo no aplicações de produção em execução na VM.
**Contas de armazenamento de destino**  | Contas de armazenamento na localização de destino para o qual os dados são replicados.
**Conjuntos de disponibilidade de destino**  | Conjuntos de disponibilidade na qual as VMs replicadas estão localizadas após a ativação pós-falha.

### <a name="step-2"></a>Passo 2

Como a replicação está ativada, o serviço de mobilidade de extensão de recuperação de Site é automaticamente instalado na VM:

1. A VM está registada com a recuperação de Site.

2. Replicação contínua está configurada para a VM. Operações de escrita de dados nos discos de VM continuamente são transferidas para a conta de armazenamento de cache, na localização de origem.

   ![Ativar o processo de replicação, o passo 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 Recuperação de site nunca necessita de conectividade de entrada para a VM. Conectividade de saída só é necessário, endereços IP/os URLs do serviço de recuperação de sites, endereços IP/os URLs de autenticação do Office 365 e endereços IP de conta de armazenamento de cache.

### <a name="step-3"></a>Passo 3

Após a conclusão da replicação contínua em curso, escritas em disco imediatamente são transferidas para a conta de armazenamento de cache. Recuperação de site processa os dados e envia-a para a conta de armazenamento de destino. Depois dos dados são processados, pontos de recuperação são gerados na conta de armazenamento de destino em alguns minutos.

## <a name="failover-process"></a>Processo de ativação pós-falha

Quando seleciona uma ativação pós-falha, as VMs são criadas no grupo de recursos de destino, a rede virtual de destino, a sub-rede de destino e, no conjunto de disponibilidade de destino. Durante uma ativação pós-falha, pode utilizar qualquer ponto de recuperação.

![Processo de ativação pós-falha](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passos seguintes

Siga o [início rápido](azure-to-azure-quickstart.md) para ativar a replicação da VM do Azure para uma região secundária.

