---
title: "Replicar aplicações (VMware para o Azure) | Microsoft Docs"
description: "Este artigo descreve como configurar a replicação de máquinas virtuais em execução no VMware para o Azure."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/29/2017
ms.author: asgang
ms.openlocfilehash: 028aa0f23c3a7c98c4801d9e306c5dcfa35aab80
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="replicate-applications-running-on-vmware-virtual-machines-to-azure"></a>Replicar as aplicações em execução em máquinas virtuais VMware para Azure



Este artigo descreve como configurar a replicação de máquinas virtuais (VMs) em execução no VMware para o Azure.
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem:

1.  [Configurar o ambiente de origem no local](site-recovery-set-up-vmware-to-azure.md).
2.  [Configurar o ambiente de destino no Azure](site-recovery-prepare-target-vmware-to-azure.md).


## <a name="enable-replication"></a>Ativar a replicação
### <a name="before-you-start"></a>Antes de começar
Quando replicar máquinas virtuais VMware:

* A conta de utilizador do Azure tem de ter determinados [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para ativar a replicação de uma nova máquina virtual para o Azure.
* VMs de VMware são detetadas a cada 15 minutos. Pode demorar 15 minutos ou mais para serem apresentadas no portal do Azure após a deteção. Da mesma forma, deteção pode demorar 15 minutos ou mais quando adicionar um anfitrião de servidor ou vSphere vCenter novo.
* Alterações de ambiente na máquina virtual (tais como a instalação de ferramentas do VMware) podem demorar 15 minutos ou mais atualizados no portal.
* Pode verificar a última vez detetada para VMs de VMware no **último contacto em** campo para o anfitrião de vSphere/servidor vCenter, no **servidores de configuração** página.
* Para adicionar máquinas para replicação sem aguardar a deteção agendada, realce o servidor de configuração (não clique nele) e clique em de **atualizar** botão.
* Ao ativar a replicação, se a máquina está preparada, o servidor de processos instala automaticamente o serviço de mobilidade no mesmo.


### <a name="enable-replication-as-follows"></a>Ativar a replicação da seguinte forma

1. Clique em **Passo 2: Replicar aplicação** > **Origem**. Depois de ativar a replicação pela primeira vez, clique em **+Replicar**, no cofre, para ativar a replicação em máquinas adicionais.
2. No **origem** página > **origem**, selecione o servidor de configuração.
3. No **máquina tipo**, selecione **máquinas virtuais** ou **máquinas físicas**.
4. No **vCenter/vSphere hipervisor**, selecione o servidor vCenter que gere o anfitrião do Vcenter ou selecione o anfitrião. Esta definição não é relevante se estiver a replicar máquinas físicas.
5. Selecione o servidor de processos, que será o nome do servidor de configuração se ainda não criou quaisquer servidores de processos adicionais. Em seguida, clique em **OK**.

    ![Ativar a origem de replicação](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. No **destino**, selecione a subscrição e o grupo de recursos onde pretende criar as máquinas virtuais de efetuada a ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure (clássico ou do Azure Resource Manager) para as máquinas virtuais de efetuada a ativação pós-falha.

7. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 

    > [!NOTE]

    >   * Pode selecionar uma conta de armazenamento standard ou premium. Se selecionar uma conta de premium, tem de especificar uma conta de armazenamento padrão adicional para os registos de replicação em curso. Contas têm de ser na mesma região que o Cofre dos serviços de recuperação.
    >   * Se pretender utilizar uma conta de armazenamento diferente, pode [criar um](../storage/common/storage-create-storage-account.md). Para criar uma conta de armazenamento utilizando o Gestor de recursos, clique em **criar nova**. Se pretender criar uma conta de armazenamento utilizando o modelo clássico, fazê-lo no portal do Azure.

8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure estabelecerão uma ligação quando se puserem em marcha após a ativação pós-falha. A rede tem de estar na mesma região que o cofre de Serviços de Recuperação. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. Se não tiver uma rede, terá de [criar um](#set-up-an-azure-network). Para criar uma rede utilizando o Gestor de recursos, clique em **criar nova**. Se pretender criar uma rede utilizando o modelo clássico, fazê-lo [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável e, em seguida, clique em **OK**.

    ![Ativar a definição de destino de replicação](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. No **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.

    ![Ativar replicação selecione máquinas de virtuais](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. No **propriedades** > **configurar propriedades**, selecione a conta utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.  
11. Por predefinição, todos os discos são replicados. Para excluir discos de replicação, clique em **todos os discos** e desmarque todos os discos que não pretende replicar.  Em seguida, clique em **OK**. Pode definir as propriedades adicionais mais tarde. [Saiba mais](site-recovery-exclude-disk.md) sobre excluindo discos.

    ![Ativar replicação configurar propriedades](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. No **as definições de replicação** > **configurar as definições de replicação**, certifique-se de que a política de replicação correto está selecionada. Pode modificar as definições de política de replicação no **definições** > **políticas de replicação** > (nome da política) > **editar definições de**. As alterações que aplicar a uma política aplicam-se também a replicação e de novas máquinas.
13. Ativar **a consistência Multi VM** se pretender recolher máquinas para um grupo de replicação. Especifique um nome para o grupo e, em seguida, clique em **OK**. 

    > [!NOTE]

    >    * Computadores num grupo de replicação replicar em conjunto e tem partilhado pontos de recuperação consistentes com falhas e consistentes da aplicação quando efetuar a ativação pós-falha.
    >    * Reunir VMs e servidores físicos, para que estes espelhar as cargas de trabalho. Ativar a consistência de várias VMS pode afetar o desempenho da carga de trabalho. Utilize apenas se máquinas a executar a mesma carga de trabalho e necessitar de consistência.

    ![Ativar a replicação](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Clique em **ativar a replicação**. Pode controlar o progresso do **ativar proteção** da tarefa no **definições** > **tarefas** > **tarefas de recuperação de Site**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

> [!NOTE]
> Se a máquina está preparada para a instalação de push, o componente de serviço de mobilidade é instalado quando a proteção está ativada. Depois do componente está instalado no computador, uma tarefa de proteção é iniciado e falha. Após a falha, terá de reiniciar manualmente cada máquina. Após o reinício, a tarefa de proteção começa novamente e ocorre a replicação inicial.
>
>

## <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Em seguida, verifique as propriedades da máquina de origem. Lembre-se de que o nome de VM do Azure tem de estar em conformidade com [requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Clique em **definições** > **replicado itens** > e, em seguida, selecione a máquina. O **Essentials** página mostra informações sobre as definições da máquina e de estado.
2. Em **Propriedades**, pode ver as informações de replicação e de ativação pós-falha da VM.
3. Em **Computação e Rede** > **Propriedades de computação**, pode especificar o nome e o tamanho do destino da VM do Azure. Altere o nome para estar em conformidade com os requisitos do Azure, se necessário.

    ![Propriedades da rede e de computação](./media/site-recovery-vmware-to-azure/vmproperties.png)

4.  Pode selecionar um [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) do que uma máquina passa a fazer parte de uma ativação pós-falha post. Pode alterar esta definição de qualquer altura antes da ativação pós-falha. Após a ativação pós-falha, se migrar a máquina a um grupo de recursos diferente, as definições de proteção para esse quebra de máquina.
5. Pode selecionar um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se a máquina deve fazer parte de uma ativação pós-falha post. Enquanto estiver a selecionar um conjunto de disponibilidade, tenha em atenção que:

    * São listados apenas os conjuntos de disponibilidade que pertençam ao grupo de recursos especificado.  
    * Máquinas com redes virtuais diferentes, não podem ser uma parte do mesmo conjunto de disponibilidade.
    * Apenas máquinas virtuais que o mesmo tamanho pode ser uma parte de um conjunto de disponibilidade.
5. Também pode ver e adicionar informações sobre a rede de destino, a sub-rede e o endereço IP atribuído à VM do Azure.
6. No **discos**, pode ver o sistema operativo e os discos de dados na VM para ser replicado.

### <a name="network-adapters-and-ip-addressing"></a>Adaptadores de rede e o endereçamento IP

- Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina de efetuada a ativação pós-falha utiliza DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação pós-falha não funciona. Se o endereço está disponível na rede de ativação pós-falha de teste, o mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste.
- O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:
    - Se o número de adaptadores de rede na máquina de origem é menor ou igual ao número de adaptadores permitidos para o tamanho da máquina de destino, o destino tem o mesmo número de adaptadores como origem.
    - Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
    Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino suporta quatro, a máquina de destino tem dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suporta um, o computador de destino tem apenas um adaptador.
    - Se a máquina virtual tem vários adaptadores de rede, todos os ligam à mesma rede. Além disso, o primeiro um mostrado na lista torna-se a *predefinido* adaptador de rede na máquina virtual do Azure.

### <a name="azure-hybrid-use-benefit"></a>Benefício de utilização de híbridos do Azure

Os clientes do Software Assurance da Microsoft podem utilizar a vantagem de utilizar híbrida do Azure para guardar no licenciamento de custos para máquinas de Windows Server que são migradas para o Azure ou para utilizar o Azure para recuperação após desastre. Se estiver elegível para utilizar o Azure híbrida utilizar beneficiar, pode especificar que a máquina virtual atribuída desta vantagem é um que Azure Site Recovery cria se houver uma ativação pós-falha. Para efetuar este procedimento:
- Aceda à secção de propriedades de computação e rede da máquina virtual replicada.
- Responda à pergunta que lhe pergunta se tiver uma licença de servidor do Windows que faz com que é elegível para o benefício de utilização de híbrida do Azure.
- Selecione a caixa de verificação para confirmar que tem uma licença do Windows Server elegível com Software Assurance, que pode utilizar para aplicar a vantagem de utilizar híbrido no computador que será criada em ativação pós-falha.
- Guarde as definições para a máquina replicada.

Saiba mais sobre [benefício de utilização do Azure híbrida](https://aka.ms/azure-hybrid-use-benefit-pricing).

## <a name="common-issues"></a>Problemas comuns

* Cada disco deve ser inferior a 1 TB de tamanho.
* O disco de SO deve ser um disco básico e não um disco dinâmico.
* Para 2/UEFI-ativa máquinas virtuais de geração, a família do sistema operativo deve ser o Windows e o disco de arranque deve ser inferior a 300 GB.

## <a name="next-steps"></a>Passos seguintes

Depois da proteção está concluída e a máquina atingiu um estado protegido, pode tentar uma [ativação pós-falha](site-recovery-failover.md) para verificar se a aplicação é apresentada no Azure ou não.

Se pretender desativar a proteção, saiba como [limpar as definições de registo e proteção](site-recovery-manage-registration-and-protection.md).
