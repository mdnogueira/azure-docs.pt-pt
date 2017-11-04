---
title: "Ativação pós-falha e falhar fazer uma cópia de VMs de VMware e servidores físicos replicados para o Azure com a recuperação de Site | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha de VMs de VMware e servidores físicos para o Azure e haja reativação para o site no local, com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 28a14a9b28dfe9c2014add9b9f691bce6ba91a4c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Ativação pós-falha e falhar fazer uma cópia de VMs de VMware e servidores físicos replicados para o Azure

Este tutorial descreve como efetuar a ativação pós-falha de uma VM de VMware para o Azure. Depois de ter a efetuar a ativação pós-falha, falhar ao seu site no local quando está disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Certifique-se de que as propriedades de VM de VMware para verificar em conformidade com os requisitos do Azure
> * Executar uma ativação pós-falha para o Azure
> * Criar um servidor de processos e o servidor de destino mestre para reativação pós-falha
> * Proteja as VMs do Azure para o site no local
> * Efetuar a ativação pós-falha do Azure no local
> * Proteja VMs no local, para iniciar a replicação novamente para o Azure

Este é o quinto tutorial uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware no local](tutorial-prepare-on-premises-vmware.md)
3. [Configurar a recuperação após desastre](tutorial-vmware-to-azure.md)
4. [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>A preparar para ativação pós-falha e a reativação pós-falha

Certifique-se de que existem não existem instantâneos da VM. A VM no local é desativada durante só.
Isto ajuda a assegurar a consistência de dados durante a replicação. Não ative a VM após a conclusão da só. Utilize o mesmo servidor de destino mestre para voltar a proteger um grupo de replicação. Se utilizar um servidor de destino mestre diferente para voltar a proteger um grupo de replicação, o servidor não é possível fornecer um ponto de comuns no tempo.

Ativação pós-falha e a reativação pós-falha tem quatro fases:

1. **Efetuar a ativação pós-falha para o Azure**: falhar máquinas do site no local para o Azure.
2. **Proteja as VMs do Azure**: Proteja as VMs do Azure, para que possam iniciar replicar as VMs de VMware no local.
3. **Efetuar a ativação pós-falha no local**: executar uma ativação pós-falha, para efetuar a reativação a partir do Azure.
4. **Reproteção no local VMs**: depois de dados falhou novamente, voltar a proteger as VMs no local que falharam ao, para que possam iniciar a replicação para o Azure.

## <a name="verify-vm-properties"></a>Verifique as propriedades VM

Verifique as propriedades da VM e certifique-se de que a VM está em conformidade com [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. No **itens protegidos**, clique em **itens replicados** > VM.

2. No **replicados item** painel, existe um resumo das informações de VM, o estado de funcionamento, e pontos de recuperação mais recente disponível. Clique em **propriedades** para ver mais detalhes.

3. No **computação e rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md), e [geridas as definições do disco](#managed-disk-considerations)

4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure estarão localizada após a ativação pós-falha e o endereço IP que será atribuído ao mesmo.

5. No **discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. No **definições** > **replicado itens** clique na VM > **ativação pós-falha**.

2. No **ativação pós-falha** selecionar um **ponto de recuperação** para efetuar a ativação pós-falha. Pode utilizar uma das seguintes opções:
   - **Mais recente** (predefinição): esta opção primeiro processa todos os dados enviados para a recuperação de sites. Fornece o RPO mais baixo (objetivo de ponto de recuperação) porque a VM do Azure criadas após ativação pós-falha tem todos os dados que foi replicados para o Site de recuperação quando a ativação pós-falha foi acionada.
   - **Mais recentes processados**: esta opção a ativação pós-falha da VM para o ponto de recuperação mais recente, processado pelo Site Recovery. Esta opção fornece um RTO baixa (objetivo de tempo de recuperação), porque nenhum tempo é gasto a processar dados não processados.
   - **Mais recente consistentes da aplicação**: esta opção a ativação pós-falha da VM para o ponto mais recente recuperação consistentes da aplicação processada pela recuperação de sites.
   - **Personalizada**: especificar um ponto de recuperação.

3. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** tentar efetuar um encerramento de máquinas virtuais de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo se falha de encerramento. Pode seguir o progresso de ativação pós-falha no **tarefas** página.

4. Se preparou ligar à VM do Azure, ligar a validá-lo após a ativação pós-falha.

5. Depois de verificar, **consolidar** a ativação pós-falha. Isto elimina todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancele uma ativação pós-falha em curso**: antes de iniciar a ativação pós-falha, a replicação de VM está parada.
> Se cancelar uma ativação pós-falha em curso, deixa de ativação pós-falha, mas a VM não irá replicar novamente.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. É possível que repare já tempos de ativação pós-falha para servidores físicos, VMware Linux máquinas, VMs de VMware que não tenham o permite o serviço DHCP e as VMs de VMware que não têm os seguintes controladores de arranque de teste: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Criar um servidor de processos no Azure

O servidor de processos recebe dados de VM do Azure e envia-a para o site no local. Uma rede de latência baixa é necessária entre o servidor de processos e a VM protegida.

- Para fins de teste, se tiver uma ligação ExpressRoute do Azure, pode utilizar o servidor de processos no local que é automaticamente instalado no servidor de configuração.
- Se tiver uma ligação VPN ou se estiver a executar a reativação pós-falha num ambiente de produção, tem de configurar uma VM do Azure como um servidor de processos baseado no Azure para reativação pós-falha.
- Para configurar um servidor de processos no Azure, siga as instruções em [neste artigo](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Por predefinição, o servidor de destino mestre é executado no servidor de configuração no local. Para efeitos deste tutorial, estamos a utilizar o mestre de predefinição. O servidor de destino mestre recebe dados de reativação pós-falha.

Se a VM num anfitrião ESXi que é gerido por um vCenter server, o servidor de destino principal tem de ter acesso ao arquivo de dados da VM (VMDK), para escrever os dados replicados para os discos da VM. Certifique-se de que o arquivo de dados VM está montado no anfitrião de destino principal, com acesso de leitura/escrita.

Se a VM estiver num ESXi que não é gerido por um vCenter server, o serviço de recuperação de sites cria uma nova VM durante só. A VM é criada no anfitrião do ESX no qual criar o destino principal.
O disco rígido da VM tem de ser um arquivo de dados está acessível para o anfitrião no qual o servidor de destino mestre está em execução.

Se a VM não utilize o vCenter, deve efetuar a deteção do anfitrião no qual o servidor de destino mestre está em execução, antes de pode voltar a proteger a máquina. Isto é verdadeiro para falhar novamente físicas demasiado. Outra opção, se a VM no local, é eliminá-lo antes de efetuar uma reativação pós-falha. Reativação pós-falha, em seguida, cria uma nova VM no mesmo anfitrião, como o anfitrião do ESX de destino principal. Quando falhar novamente para uma localização alternativa, os dados são recuperados para o mesmo arquivo de dados e o mesmo anfitrião ESX que foi utilizado pelo servidor de destino mestre no local.

Não é possível utilizar o armazenamento vMotion no servidor de destino principal. Se o fizer, reativação pós-falha poderá não funciona, porque os discos não estão disponíveis. Exclua os servidores de destino principal da sua lista de vMotion.

## <a name="reprotect-azure-vms"></a>Proteja as VMs do Azure

Este procedimento presumes que a VM no local não está disponível e está a proteger novamente para uma localização alternativa.

1. No **definições** > **replicado itens**, com o botão direito a VM que foi efetuada a ativação pós-falha > **voltar a proteger**.
2. No **voltar a proteger**, certifique-se de que **do Azure para o local**, está selecionado.
3. Especifique o servidor de destino principal no local e o servidor de processos.

4. No **arquivo de dados**, selecione o arquivo de dados de destino principal ao qual pretende recuperar o discos no local. Utilize esta opção quando a VM no local foi eliminada e tem de criar novos discos. Este definições é ignorada se os discos já existem, mas tem de especificar um valor.
5. Selecione a unidade de retenção de destino principal. A política de reativação pós-falha é selecionada automaticamente.
6. Clique em **OK** para começar a só. Uma tarefa começa a replicar a máquina virtual do Azure para o site no local. Pode controlar o progresso no **tarefas** separador.

> [!NOTE]
> Se pretender recuperar a VM do Azure existente no local VM, Monte o arquivo de dados da máquina de virtual no local com acesso de leitura/escrita, no anfitrião ESXi de servidor de destino mestre.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Executar uma ativação pós-falha do Azure no local

Replicar para o local, é utilizada uma política de reativação pós-falha. Esta política é criada automaticamente quando criou uma política de replicação para replicação no Azure:

- A política é automaticamente associada ao servidor de configuração.
- Não é possível modificar a política.
- Os valores de política são:
    - Limiar RPO = 15 minutos
    - Retenção do ponto de recuperação = 24 horas
    - Frequência de instantâneos consistentes da aplicação = 60 minutos

Execute a ativação pós-falha da seguinte forma:

1. No **itens replicados** página, clique com o botão direito a máquina > **ativação pós-falha não planeada**.
2. No **confirmar ativação pós-falha**, certifique-se de que a direção de ativação pós-falha do Azure.

3. Selecione o ponto de recuperação que pretende utilizar para a ativação pós-falha. Um ponto de recuperação consistentes da aplicação ocorre antes do ponto mais recente no tempo e irá fazer com que alguma perda de dados. Quando é executada a ativação pós-falha, a recuperação de sites encerra as VMs do Azure e arranca a VM no local. Não existe irá ser alguns períodos de inatividade, por isso, escolha um período de tempo adequado.
4. A máquina com o botão direito e clique em **consolidar**. Isto aciona uma tarefa que remove as VMs do Azure.
5. Certifique-se de que as VMs do Azure ter sido encerradas conforme esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Proteja máquinas no local para o Azure

Dados devem agora estar novamente no seu site no local, mas não está a replicar para o Azure. Pode iniciar a replicação para o Azure novamente da seguinte forma:

1. No cofre > **definições** > **itens replicados**, selecione de falhas de cópia de segurança VMs que ocorreu uma falha anterior e clique em **voltar a proteger**.
2. Selecione o servidor de processo que é utilizado para enviar os dados replicados para o Azure e clique em **OK**.

Depois do que estiver concluído, a VM é replicado para o Azure e pode executar uma ativação pós-falha conforme necessário.
