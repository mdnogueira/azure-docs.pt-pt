---
title: "Replicar uma VM do Azure para outra região do Azure (pré-visualização)"
description: "Este guia de Introdução fornece os passos necessários para replicar uma VM do Azure com uma região do Azure numa região diferente."
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 4c52f15ba5be9171c00ccd466045d66973d3d5de
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-an-azure-vm-to-another-azure-region-preview"></a>Replicar uma VM do Azure para outra região do Azure (pré-visualização)

O [do Azure Site Recovery](../site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação (BCDR) de continuidade e desastre negócio ao manter as suas aplicações empresariais a cópia de segurança e em execução disponíveis durante a falhas planeadas e não. Recuperação de site gere e orquestra a recuperação de desastre de máquinas no local e máquinas virtuais do Azure (VMs), incluindo a replicação, ativação pós-falha e recuperação.

Este guia de introdução descreve como replicar uma VM do Azure para uma região do Azure diferente.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Ativar a replicação para a VM do Azure

1. No portal do Azure, clique em **máquinas virtuais**e selecione a VM que pretende replicar.

2. No **definições**, clique em **recuperação após desastre (pré-visualização)**.
3. No **configurar a recuperação de desastre** > **região de destino** selecione a região de destino para a qual irá replicar.
4. Para este guia de introdução, aceite as outras predefinições.
5. Clique em **ativar a replicação**. Esta ação inicia uma tarefa para ativar a replicação para a VM.

    ![Ativar a replicação](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>Verifique as definições

Depois de concluída a tarefa de replicação, pode verificar o estado de replicação, modifique as definições de definições de replicação e testar a implementação.

1. No menu de VM, clique em **recuperação após desastre (pré-visualização)**.
2. Pode verificar o estado de funcionamento de replicação, de pontos de recuperação que foram criados e de origem e destino regiões no mapa.

   ![Estado de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Limpar recursos

A VM na região primária deixa de ser replicada ao desativar a replicação para o mesmo:

- As definições de replicação de origem são limpas automaticamente.
- Site recuperação faturação para a VM também é interrompida.

Pare a replicação, da seguinte forma:

1. Selecione a VM.
2. No **recuperação após desastre (pré-visualização)**, clique em **mais**.
3. Clique em **desative a replicação**.

   ![Desative a replicação](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, replicados numa única VM para uma região secundária.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre para as VMs do Azure](azure-to-azure-tutorial-enable-replication.md)
