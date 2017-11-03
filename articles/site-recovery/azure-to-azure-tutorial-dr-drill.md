---
title: "Executar um exercício de recuperação após desastre para as VMs do Azure para uma região secundária do Azure com o Azure Site Recovery (pré-visualização)"
description: "Saiba como executar um exercício de recuperação após desastre para as VMs do Azure para uma região secundária do Azure, utilizando o serviço do Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5bcd3d64714951508d984c17326e845ae4842670
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>Executar um exercício de recuperação após desastre para as VMs do Azure para uma região secundária do Azure (pré-visualização)

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação (BCDR) de continuidade e desastre negócio ao manter as suas aplicações empresariais a cópia de segurança e em execução disponíveis durante a falhas planeadas e não. Recuperação de site gere e orquestra a recuperação de desastre de máquinas no local e máquinas virtuais do Azure (VMs), incluindo a replicação, ativação pós-falha e recuperação.

Este tutorial mostra como executar um exercício de recuperação após desastre para uma VM do Azure, de uma região do Azure para outro, com uma ativação pós-falha de teste. Um exercício valida a estratégia de replicação sem perda de dados ou o período de indisponibilidade e não afeta o ambiente de produção. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique os pré-requisitos
> * Execute uma ativação pós-falha de teste para uma única VM

## <a name="prerequisites"></a>Pré-requisitos

- Antes de executar uma ativação pós-falha de teste, recomendamos que verifique as propriedades VM para se certificar de que tudo está conforme esperado.  Aceder às propriedades do VM no **replicado itens**. O **Essentials** painel mostra informações sobre o estado e as definições de máquinas.
- Recomendamos que utilize uma rede separada da VM do Azure para ativação pós-falha de teste e não a rede predefinida que foi configurada quando ativar a replicação.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. No **definições** > **itens replicados**, clique na VM **+ ativação pós-falha de teste** ícone.

2. No **ativação pós-falha de teste**, selecione um ponto de recuperação a utilizar para a ativação pós-falha:

   - **Mais recentes processados**: a ativação pós-falha da VM para o ponto de recuperação mais recente que foi processado pelo serviço de recuperação de sites. O carimbo de hora é apresentado. Com esta opção, não tempo é gasto a processar dados, pelo que fornece um RTO baixa (objetivo de tempo de recuperação)
   - **Mais recente consistentes da aplicação**: esta opção se falhar a todas as VMs do ponto de recuperação consistentes com aplicação mais recente. O carimbo de hora é apresentado.
   - **Personalizada**: selecionar qualquer ponto de recuperação.

3. Selecione o destino do Azure rede virtual para as VMs do Azure na região secundária será ligada após a ocorrência da ativação pós-falha.

4. Para iniciar a ativação pós-falha, clique em **OK**. Para acompanhar o progresso, clique na VM para abrir as respetivas propriedades. Em alternativa, pode clicar no **ativação pós-falha de teste** tarefa no nome do cofre > **definições** > **tarefas** > **as tarefas de recuperação de Site**.
5. Após a ativação pós-falha, a réplica VM do Azure é apresentado no portal do Azure > **máquinas virtuais**. Certifique-se de que a VM está em execução, adequada e ligado à rede pretendida.
6. Para eliminar as VMs que foram criadas durante a ativação pós-falha de teste, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação ou item replicado. No **notas**, registar e guardar todas as observações associadas à ativação pós-falha de teste.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha de produção](azure-to-azure-tutorial-failover-failback.md)
