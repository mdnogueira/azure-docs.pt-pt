---
title: Preparar o destino (VMware para o Azure) | Microsoft Docs
description: "Este artigo descreve como preparar o ambiente do Azure para iniciar a replicação de máquinas virtuais VMware para o Azure."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: bsiva
ms.openlocfilehash: 98e0a7cd77e8e6e9ce124845aad49bd03a2bf1d8
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar o destino (VMware para o Azure)
> [!div class="op_single_selector"]
> * [VMware para o Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Físico para o Azure](./site-recovery-prepare-target-physical-to-azure.md)

Este artigo descreve como preparar o ambiente do Azure para iniciar a replicação de máquinas virtuais VMware para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume:
- Criou um cofre dos serviços de recuperação para proteger as máquinas virtuais de VMware. Pode criar um cofre dos serviços de recuperação do [portal do Azure](http://portal.azure.com "portal do Azure").
- Tiver [configuração do ambiente no local](./site-recovery-set-up-vmware-to-azure.md) replicar máquinas virtuais VMware no Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir o **objetivo de proteção do passo 1:Select** e **passo 2: preparar a origem**, é direcionado para **passo 3: destino**

![Preparar o destino](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Subscrição:** no menu pendente, selecione a subscrição que pretende replicar as máquinas virtuais.
2. **Modelo de implementação:** selecione o modelo de implementação (clássico ou do Resource Manager)

Com base no modelo de implementação escolhida, é executada uma validação para se certificar de que tem pelo menos uma conta de armazenamento compatíveis e de rede virtual na subscrição de destino para replicar e ativação pós-falha da máquina virtual.

Depois de validações concluir com êxito, clique em OK para ir para o passo seguinte.

Se não tiver uma conta de armazenamento do Resource Manager compatível ou de rede virtual, pode criar no clicando a **+ contas de armazenamento** ou **+ rede** botões na parte superior da página.

## <a name="next-steps"></a>Passos seguintes
[Configurar as definições de replicação](./site-recovery-setup-replication-settings-vmware.md).
