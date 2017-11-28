---
title: "Ativação pós-falha na recuperação de Site | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre a ativação pós-falha para o Azure ou num datacenter secundário."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: pratshar
ms.openlocfilehash: 160457fdad57cd947077aeb3a4ed85fd2a2849d8
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="failover-in-site-recovery"></a>Reativação pós-falha na Recuperação de Sites
Este artigo descreve como a ativação pós-falha máquinas de virtuais e físicos servidores protegidos pela recuperação de sites.

## <a name="prerequisites"></a>Pré-requisitos
1. Antes de efetuar uma ativação pós-falha, efetue um [ativação pós-falha de teste](site-recovery-test-failover-to-azure.md) para se certificar de que tudo está a funcionar conforme esperado.
1. [Preparar a rede](site-recovery-network-design.md) na localização de destino antes de efetuar uma ativação pós-falha.  

Utilize a tabela seguinte para saber sobre as opções de ativação pós-falha fornecidas pelo Azure Site Recovery. Estas opções também estão listadas para cenários de ativação pós-falha diferentes.

| Cenário | Requisito da recuperação da aplicação | Fluxo de trabalho do Hyper-V | Fluxo de trabalho para VMware
|---|--|--|--|
|Ativação pós-falha planeada devido a um período de indisponibilidade futura do Centro de dados| Perda de dados para a aplicação quando é efetuada uma atividade planeada| Para o Hyper-V, a ASR replica os dados com uma frequência de cópia especificado pelo utilizador. Ativação pós-falha planeada é utilizada para substituir a frequência e replicar as alterações finais antes de uma ativação pós-falha é iniciada. <br/> <br/> 1.    Planear uma janela de manutenção de acordo com o processo de gestão de alteração do seu negócio. <br/><br/> 2 notificar os utilizadores do período de indisponibilidade futura. <br/><br/> 3. Coloca offline a aplicação destinada ao utilizador.<br/><br/>4. Inicie a ativação pós-falha planeada através do portal de ASR. As máquinas no local é automaticamente o encerramento.<br/><br/>Perda de dados de aplicações eficazes = 0 <br/><br/>Um diário de alterações de pontos de recuperação também é fornecido numa janela de retenção para um utilizador que pretende utilizar um ponto de recuperação mais antigo. (retenção de 24 horas para Hyper-V).| Para VMware, ASR replica dados continuamente utilizando CDP. Ativação pós-falha fornece-o utilizador a opção de ativação pós-falha para os dados mais recentes (incluindo post aplicação encerramento)<br/><br/> 1. Planear uma janela de manutenção de acordo com o processo de gestão de alterações <br/><br/>2 notificar os utilizadores do período de indisponibilidade futura <br/><br/>3.  Coloca offline a aplicação destinada ao utilizador. <br/><br/>4.  Inicie uma ativação de pós-falha planeada através do portal ASR do ponto mais recente, depois da aplicação está offline. Utilize a opção "Ativação pós-falha não planeada" no portal e selecione o ponto mais recente para ativação pós-falha. As máquinas no local é automaticamente o encerramento.<br/><br/>Perda de dados de aplicações eficazes = 0 <br/><br/>Um diário de alterações de pontos de recuperação numa janela de retenção é fornecido para um cliente que pretende utilizar um ponto de recuperação mais antigo. (72 horas de retenção para VMware).
|Ativação pós-falha devido a um período de indisponibilidade do Centro de dados não planeada (natural ou desastre IT) | Perda mínima de dados para a aplicação | 1. iniciar o plano BCP da organização <br/><br/>2. Inicie ativação pós-falha não planeada através do portal ASR para a versão mais recente ou um ponto da janela de retenção (diário).| 1. Inicie o plano BCP da organização. <br/><br/>2.  Inicie ativação pós-falha não planeada através do portal ASR para a versão mais recente ou um ponto da janela de retenção (diário).


## <a name="run-a-failover"></a>Executar uma ativação pós-falha
Este procedimento descreve como executar uma ativação pós-falha para um [plano de recuperação](site-recovery-create-recovery-plans.md). Em alternativa pode executar a ativação pós-falha para uma única máquina virtual ou um servidor físico a partir de **replicado itens** página


![Ativação pós-falha](./media/site-recovery-failover/Failover.png)

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **ativação pós-falha**
2. No **ativação pós-falha** ecrã, selecione um **ponto de recuperação** a ativação pós-falha. Pode utilizar uma das seguintes opções:
    1.  **Mais recente** (predefinição): esta opção inicia a tarefa ao primeiro processar todos os dados que foi enviados para o serviço de recuperação de Site. Processamento de dados cria um ponto de recuperação para cada máquina virtual. Este ponto de recuperação é utilizado pela máquina virtual durante a ativação pós-falha. Esta opção fornece o RPO mais baixo (objetivo de ponto de recuperação) que a máquina virtual criada após ativação pós-falha tem todos os dados que foram replicada para o serviço de recuperação de Site quando a ativação pós-falha foi acionada.
    1.  **Mais recentes processados**: esta opção se falhar a todas as máquinas virtuais do plano de recuperação do ponto de recuperação mais recente que já tenha sido processada pelo serviço de recuperação de sites. Quando estão a fazer ativação pós-falha de teste de uma máquina virtual, o carimbo de hora do último ponto de recuperação processados também é apresentado. Se estão a fazer a ativação pós-falha de um plano de recuperação, pode ir para a máquina virtual individual e observe **pontos de recuperação mais recente** mosaico para obter estas informações. Como não tempo é gasto a processar os dados não processados, esta opção fornece uma opção de ativação pós-falha baixa RTO (objetivo de tempo de recuperação).
    1.  **Mais recente consistentes da aplicação**: esta opção se falhar a todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação consistente da aplicação que já tenha sido processado pelo serviço de recuperação de sites. Quando estão a fazer ativação pós-falha de teste de uma máquina virtual, o carimbo de hora do último consistentes da aplicação do ponto de recuperação também é apresentado. Se estão a fazer a ativação pós-falha de um plano de recuperação, pode ir para a máquina virtual individual e observe **pontos de recuperação mais recente** mosaico para obter estas informações.
    1.  **Mais recente várias VMS processados**: esta opção só está disponível para planos de recuperação que têm, pelo menos, uma máquina virtual com consistência de várias VMS no. Máquinas virtuais que fazem parte de uma grupo de replicação ativação pós-falha para a recuperação mais recente comuns várias VMS consistente ponto. Outra ativação pós-falha de máquinas virtuais para as respetivas último processados ponto de recuperação.  
    1.  **Mais recente várias VMS consistentes da aplicação**: esta opção só está disponível para planos de recuperação que tenha, pelo menos, uma máquina virtual com ON de consistência de várias VMS. Máquinas virtuais que fazem parte de uma grupo de replicação ativação pós-falha para a recuperação mais recente comuns várias VMS consistentes com aplicações do ponto. Outra ativação pós-falha de máquinas virtuais ao respetivo ponto de recuperação consistentes com aplicações mais recente.
    1.  **Personalizada**: Se estiver a efetuar ativação pós-falha de teste de uma máquina virtual, em seguida, pode utilizar esta opção para ativação pós-falha para um ponto de recuperação específico.

    > [!NOTE]
    > A opção de escolher um ponto de recuperação só está disponível quando estiver a efetuar a ativação pós-falha para o Azure.
    >
    >


1. Se algumas das máquinas virtuais no plano de recuperação foram a ativação pós-falha numa execução anterior e agora as máquinas virtuais estão ativas na localização de origem e destino, pode utilizar **mudar a direção** opção decidir a direção na qual deve ocorrer a ativação pós-falha.
1. Se estiver a efetuar a ativação pós-falha para o Azure e a encriptação de dados é ativada para a nuvem (aplica-se apenas quando proteger máquinas virtuais Hyper-v de um servidor VMM), na **chave de encriptação** selecionar o certificado que foi emitido quando ativar a encriptação de dados durante a configuração no servidor do VMM.
1. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que a recuperação de sites para tentar efetuar um encerramento de máquinas virtuais de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo se falha de encerramento.  

    > [!NOTE]
    > Se as máquinas virtuais de Hyper-v estiverem protegidas, a opção de encerramento tenta também sincronizar os dados no local que tenham ainda não foram enviados para o serviço antes de acionar a ativação pós-falha.
    >
    >

1. Pode seguir o progresso de ativação pós-falha no **tarefas** página. Mesmo se ocorrerem erros durante uma ativação pós-falha não planeada, o plano de recuperação é executada até estar concluída.
1. Após a ativação pós-falha, valide a máquina virtual através do registo mesmo. Se pretender voltar a outro ponto de recuperação para a máquina virtual, em seguida, pode utilizar **alterar o ponto de recuperação** opção.
1. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **consolidar** a ativação pós-falha. Consolidação elimina todos os pontos de recuperação disponíveis com o serviço e **alterar o ponto de recuperação** opção deixará de estar disponível.

## <a name="planned-failover"></a>Ativação pós-falha planeada
Máquinas virtuais/servidores físicos protegidos através da recuperação de Site também suporte **a ativação pós-falha planeada**. Ativação pós-falha planeada é uma zero opção do dados perda ativação pós-falha. Quando é acionada uma ativação pós-falha planeada, primeiro são encerrar as máquinas virtuais de origem, os dados mais recentes são sincronizados e, em seguida, é acionada uma ativação pós-falha.

> [!NOTE]
> Quando a ativação pós-falha Hyper-v máquinas virtuais a partir de um local site para outro site no local, para voltar para o site primário no local tem de primeiro **replicar inversamente** a máquina virtual de volta para o site primário e, em seguida, acione uma ativação pós-falha. Se a máquina virtual primária não está disponível, em seguida, antes de começar a **replicar inversamente** tiver de restaurar a máquina virtual a partir de uma cópia de segurança.   
>
>

## <a name="failover-job"></a>Tarefa de ativação pós-falha

![Ativação pós-falha](./media/site-recovery-failover/FailoverJob.png)

Quando é acionada uma ativação pós-falha, envolve os seguintes passos:

1. Verificação de pré-requisitos: este passo garante que todas as condições necessárias para a ativação pós-falha são cumpridas
1. Ativação pós-falha: Este passo processa os dados e torna pronto para que uma máquina virtual do Azure podem ser criada fora do mesmo. Se tiver escolhido **mais recente** ponto de recuperação, este passo cria um ponto de recuperação dos dados que foi enviados para o serviço.
1. Iniciar: Este passo cria uma máquina virtual do Azure com os dados processados no passo anterior.

> [!WARNING]
> **Não cancele um em curso ativação pós-falha**: antes de iniciar a ativação pós-falha, a replicação da máquina virtual está parada. Se lhe **Cancelar** uma tarefa de progresso, deixa de ativação pós-falha, mas a máquina virtual não será iniciado replicar. Não é possível iniciar a replicação novamente.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tempo decorrido para a ativação pós-falha para o Azure

Em certos casos, a ativação pós-falha de máquinas virtuais requer um passo intermédio extra que normalmente demora cerca de 8 a 10 minutos a concluir. Nos seguintes casos, o tempo decorrido para a ativação pós-falha será maior do que o habitual:

* Máquinas virtuais VMware com o serviço de mobilidade da versão mais antiga do que 9.8
* Servidores físicos 
* Máquinas virtuais Linux de VMware
* Máquinas virtuais de Hyper-V protegidas como servidores físicos
* Máquinas virtuais VMware onde seguintes controladores não estão presentes como controladores de arranque 
    * storvsc 
    * VMBus 
    * storflt 
    * intelide 
    * ATAPI
* Máquinas virtuais VMware que não tenham o serviço DHCP ativado independentemente se estão a utilizar DHCP ou estático de endereços IP

Em todos os outros casos este passo intermédio não é necessário e o tempo decorrido para a ativação pós-falha é significativamente inferior. 





## <a name="using-scripts-in-failover"></a>Utilizar scripts na ativação pós-falha
Poderá automatizar determinadas ações ao efetuar uma ativação pós-falha. Pode utilizar scripts ou [runbooks de automatização do Azure](site-recovery-runbook-automation.md) no [planos de recuperação](site-recovery-create-recovery-plans.md) fazê-lo.

## <a name="other-considerations"></a>Outras considerações
* **Letra de unidade** — para manter a letra de unidade em máquinas virtuais após ativação pós-falha, pode definir o **SAN política** para a máquina virtual para **OnlineAll**. [Leia mais](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Passos seguintes

> [!WARNING]
> Depois de ter a executar a ativação pós-falha de máquinas virtuais e o Centro de dados no local está disponível, deve [ **Proteja** ](site-recovery-how-to-reprotect.md) fazer uma cópia de máquinas virtuais VMware para o Centro de dados no local.

Utilize [ **a ativação pós-falha planeada** ](site-recovery-failback-from-azure-to-hyper-v.md) opção para **reativação pós-falha** máquinas de virtuais de Hyper-v no local a partir do Azure.

Se tiverem falhado através de Hyper-v máquina virtual para outro local Centro de dados gerido por um servidor VMM e o Centro de dados primária está disponível, em seguida, utilize **a replicação inversa** opção para iniciar a replicação para o Centro de dados principal.
