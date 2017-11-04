---
title: "Replicar VMs do Azure entre regiões do Azure para recuperação após desastre tem: Azure para o Azure | Microsoft Docs"
description: "Resume os passos que necessários para replicar as VMs do Azure entre regiões do Azure (Azure para o Azure) com o serviço do Azure Site Recovery para as necessidades de recuperação após desastre."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Replicar VMs do Azure entre regiões com o Azure Site Recovery

>[!NOTE]
>
> Azure Site Recovery replicação para máquinas de virtuais (VMs) do Azure está atualmente em pré-visualização.

Este artigo descreve como replicar as VMs do Azure entre regiões do Azure utilizando o [recuperação de Site](site-recovery-overview.md) serviço no portal do Azure.

Publique comentários e perguntas na parte inferior deste artigo ou no [fórum do Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Recuperação após desastre no Azure

Funcionalidades e capacidades de infraestrutura do Azure incorporada contribuem para uma estratégia de disponibilidade robusto e resiliente para cargas de trabalho que são executados em VMs do Azure. No entanto, existem muitos motivos por que motivo precisa de planear a recuperação de desastres entre regiões do Azure:

- Tem de cumprir as diretrizes de conformidade para aplicações específicas e cargas de trabalho que requerem uma continuidade do negócio e a estratégia de recuperação (BCDR) de desastre.
- Pretende que a capacidade de proteger e recuperar as VMs do Azure com base nas suas decisões de negócio e não apenas com base na funcionalidade do Azure integrada.
- Terá de testar a ativação pós-falha e recuperação de acordo com as suas necessidades comerciais e de conformidade, sem afetar a produção.
- Terá de efetuar a ativação pós-falha para a região de recuperação em caso de desastre e falhar novamente para a região de origem original de forma totalmente integrada.

Utilize a recuperação de Site para a replicação de VM do Azure para o Azure para o ajudar a fazer todas estas tarefas.


## <a name="why-use-site-recovery"></a>Por que utilizar a Recuperação de Sites?      

Recuperação de sites fornece uma forma simples para replicar as VMs do Azure entre regiões:

- **Implementação automática**. Ao contrário de um modelo de replicação ativo-ativo, não é necessário para uma infraestrutura dispendiosas e complexa na região secundária. Ao ativar a replicação, a recuperação de sites cria automaticamente os recursos necessários na região de destino, com base nas definições de região de origem.
- **Controlar regiões**. Com a recuperação de Site, pode replicar a qualquer região para qualquer região dentro de um continente. Compare-as com o armazenamento georredundante de acesso de leitura, o que replica de forma assíncrona entre padrão [emparelhado regiões](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) apenas. Armazenamento georredundante com acesso de leitura fornece acesso só de leitura aos dados na região de destino.
- **Automatizada replicação**. Recuperação de sites fornece automatizada replicação contínua. Ativação pós-falha e a reativação pós-falha podem ser acionadas com um único clique.
- **RTO e RPO**. Recuperação de site tira partido da infraestrutura de rede do Azure que liga as regiões para manter o RTO e RPO muito baixa.
- **Testar**. Pode executar simulações de recuperação após desastre com as ativações pós-falha de teste a pedido, como e quando for necessárias, sem afetar as cargas de trabalho de produção ou a replicação em curso.
- **Planos de recuperação**. Pode utilizar os planos de recuperação para orquestrar a ativação pós-falha e a reativação pós-falha da aplicação completa em execução em várias VMs. A funcionalidade do plano de recuperação tem integração de primeira classe avançada com runbooks de automatização do Azure.


## <a name="deployment-summary"></a>Resumo de implementação

Eis um resumo de que precisa de fazer para configurar a replicação de VMs entre regiões do Azure:

1. Crie um cofre dos Serviços de Recuperação. O Cofre contém definições de configuração e orquestra a replicação.
2. Ative a replicação para as VMs do Azure.
3. Execute uma ativação pós-falha de teste para se certificar de que tudo está a funcionar conforme esperado.

>[!IMPORTANT]
>
> Pode verificar o [matriz de suporte para a replicação de VM do Azure](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> Para obter informações sobre como configurar a conectividade de saída de rede necessária para as VMs do Azure para replicação de recuperação de sites, consulte o [rede documento de orientação](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Antes de começar

* A conta de utilizador do Azure tem de ter determinados [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para ativar a replicação da máquina virtual do Azure.
* Deve ser ativada a sua subscrição do Azure para criar as VMs na localização de destino que pretende utilizar como a região de recuperação após desastre. Contacte o suporte para ativar a quota necessária.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Recomendamos que crie o Cofre dos serviços de recuperação na localização onde pretende que as VMs para replicar. Por exemplo, se a localização de destino é o Centro E.U.A., crie o Cofre na **EUA Central**.

## <a name="enable-replication"></a>Ativar a replicação

No **cofres dos serviços de recuperação**, clique no nome do cofre. No cofre, clique em de **+ replicar** botão.

### <a name="step-1-configure-the-source"></a>Passo 1. Configurar a origem
1. No **origem**, selecione **Azure - pré-visualização**.
2. No **localização de origem**, selecione a origem de região do Azure onde as VMs estão a ser executados.
3. Selecione o modelo de implementação das suas VMs: **Resource Manager** ou **clássico**.
4. Selecione o **grupo de recursos de origem** para as VMs do Gestor de recursos ou **serviço em nuvem** para VMs clássicas.

    ![Configurar a origem](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>Passo 2. Selecione as máquinas virtuais

* Selecione as VMs que pretende replicar e, em seguida, clique em **OK**.

    ![Selecione as VMs](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>Passo 3. Configurar definições

1. Para substituir as predefinições de destino e especifique as definições da sua escolha, clique em **personalizar**. Para obter mais informações, consulte [Personalizar recursos de destino](site-recovery-replicate-azure-to-azure.md##customize-target-resources).

    ![Configurar definições](./media/site-recovery-azure-to-azure/settings.png)


2. Por predefinição, a recuperação de sites cria uma política de replicação que tem instantâneos consistentes com aplicação todas as 4 horas e mantém pontos de recuperação durante 24 horas. Para criar uma política com diferentes definições, clique em **personalizar** junto a **política de replicação**.

    ![Personalizar a política](./media/site-recovery-azure-to-azure/customize-policy.png)

3. Para começar a aprovisionar os recursos de destino, clique em **criar recursos de destino**. Aprovisionamento leva um minuto ou. Não feche o painel durante o aprovisionamento ou é necessário começar de novo.

4. Para acionar a replicação da VM selecionada, clique em **ativar a replicação**.

5. Pode controlar o progresso do **ativar a proteção** da tarefa no **definições** > **tarefas** > **tarefas de recuperação de Site**.

6. No **definições** > **itens replicados**, pode ver o estado de VMs e o progresso da replicação inicial. Clique na VM para desagregar as respetivas definições.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Depois de configurar tudo, execute uma ativação pós-falha de teste para se certificar de que tudo está a funcionar conforme esperado:

1. Para efetuar a ativação pós-falha de um único computador, no **definições** > **itens replicados**, clique na VM **+ ativação pós-falha de teste** ícone.

2. Para efetuar a ativação pós-falha de um plano de recuperação, na **definições** > **planos de recuperação**, faça duplo clique o plano **ativação pós-falha de teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md). 

3. No **ativação pós-falha de teste**, selecione a rede virtual do Azure de destino para as VMs do Azure estão ligadas após a ocorrência da ativação pós-falha.

4. Para iniciar a ativação pós-falha, clique em **OK**. Para acompanhar o progresso, clique na VM para abrir as respetivas propriedades. Ou pode clicar no **ativação pós-falha de teste** tarefa no nome do cofre > **definições** > **tarefas** > **astarefasderecuperaçãodesites**.

5. Após a ativação pós-falha, a máquina do Azure de réplica é apresentada no portal do Azure > **máquinas virtuais**. Certifique-se de que a VM é o tamanho adequado, tenha ligado à rede pretendida, e que está em execução.

6. Para eliminar as VMs que foram criadas durante a ativação pós-falha de teste, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação ou item replicado. No **notas**, registar e guardar todas as observações associadas à ativação pós-falha de teste. 

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre as ativações pós-falha de teste.


## <a name="next-steps"></a>Passos seguintes

Depois de testar a implementação:

- [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de ativações pós-falha e como executá-las.
- Saiba mais sobre [planos de recuperação a utilizar](site-recovery-create-recovery-plans.md) para reduzir o RTO.
