---
title: "Preparar o destino (físico para o Azure) | Microsoft Docs"
description: "Este artigo descreve como preparar o ambiente do Azure para iniciar a replicar servidores físicos que executem Windows ou Linux para o Azure."
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
ms.date: 5/31/2017
ms.author: bsiva
ms.openlocfilehash: aa7a32ace8354f615a8b8cc137f6bdf48fbadf48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar o destino (VMware para o Azure)
> [!div class="op_single_selector"]
> * [VMware para o Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Físico para o Azure](./site-recovery-prepare-target-physical-to-azure.md)

Este artigo descreve como preparar o ambiente do Azure para iniciar a replicar servidores físicos (x64) que executem Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume o seguinte:
- Criou um cofre dos serviços de recuperação para proteger os servidores físicos. Pode criar um cofre dos serviços de recuperação do [portal do Azure](http://portal.azure.com "portal do Azure").
- Tiver [configuração do ambiente no local](./site-recovery-set-up-physical-to-azure.md) replicar servidores físicos no Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir o **objetivo de proteção do passo 1:Select** e **passo 2: preparar a origem**, é direcionado para **passo 3: destino**

![Preparar o destino](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Subscrição:** no menu pendente, selecione a subscrição que pretende replicar os servidores físicos para.
2. **Modelo de implementação:** selecione o modelo de implementação (clássico ou do Resource Manager)

Com base no modelo de implementação escolhida, é executada uma validação para se certificar de que tem pelo menos uma conta de armazenamento compatíveis e de rede virtual na subscrição de destino para replicar e ativação pós-falha os servidores físicos para.

Depois de validações concluir com êxito, clique em OK para ir para o passo seguinte.

Se não tiver uma conta de armazenamento do Resource Manager compatível ou de rede virtual, ou se pretende adicionar mais, pode fazê-clicando a **+ contas de armazenamento** ou **+ rede** botões no topo do painel.

## <a name="next-steps"></a>Passos seguintes
[Configurar as definições de replicação](./site-recovery-setup-replication-settings-vmware.md).
