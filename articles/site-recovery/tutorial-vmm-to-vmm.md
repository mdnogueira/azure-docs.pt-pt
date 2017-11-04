---
title: "Configurar a recuperação após desastre para as VMs de Hyper-V entre os sites no local com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação após desastre para as VMs de Hyper-V entre os sites no local com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 65eda71c-3ca3-41bc-b02d-00fecc1557d7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: raynew
ms.openlocfilehash: 1647e9d69da3e991bec4e00b3a1083a254fa9550
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Configurar a recuperação após desastre para as VMs de Hyper-V para o site secundário no local

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre, gerir e da orquestração de replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial mostra como configurar a recuperação de desastres para um site secundário, para VMs de Hyper-V no local geridas em nuvens do System Center Virtual Machine Manager (VMM). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Preparar servidores do VMM no local e anfitriões Hyper-V
> * Criar um cofre dos serviços de recuperação para a recuperação de sites 
> * Configurar a origem e ambientes de replicação de destino. 
> * Configurar o mapeamento de rede (se o Hyper-V é gerido pelo VMM do System Center)
> * Criar uma política de replicação
> * Ativar a replicação para uma VM

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Reveja o [arquitetura do cenário e componentes](concepts-hyper-v-to-secondary-architecture.md).
- Reveja o [suportar requisitos](site-recovery-support-matrix-to-sec-site.md) para todos os componentes.
- Certifique-se de que servidores do VMM e anfitriões Hyper-V estão em conformidade com [suportar requisitos](site-recovery-support-matrix-to-sec-site.md).
- Verifique se as VMs que pretende replicar cumprem [replicado suporte máquina](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- Prepare servidores VMM para o mapeamento da rede.

### <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede

[Mapeamento da rede](site-recovery-network-mapping.md) maps no local redes de VM do VMM em nuvens de origem e de destino. Mapeamento faz o seguinte:

- Liga-se as VMs para redes VM de destino adequada após a ativação pós-falha. 
- As VMs da réplica, coloca forma ideal em servidores de anfitrião de Hyper-V de destino. 
- Se não configurar o mapeamento da rede, as VMs de réplica não será ligada a uma rede VM após a ativação pós-falha.

Prepare VMM da seguinte forma:

1. Certifique-se de que tem [redes lógicas o VMM](https://docs.microsoft.com/system-center/vmm/network-logical) nos servidores do VMM de origem e de destino.
    - A rede lógica no servidor de origem deve ser associada com a nuvem de origem no qual estão localizados os anfitriões de Hyper-V.
    - A rede lógica no servidor de destino deve ser associada com a nuvem de destino.
1. Certifique-se de que tem [redes VM](https://docs.microsoft.com/system-center/vmm/network-virtual) nos servidores do VMM de origem e de destino. Redes VM devem ser ligadas à rede lógica em cada localização.
2. Ligar as VMs nos anfitriões de Hyper-V de origem à rede VM de origem. 


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Escolha um objetivo de proteção

Selecione o que pretende replicar e para onde pretende que seja replicado.

1. Clique em **recuperação de Site** > **passo 1: preparar a infraestrutura** > **objetivo de proteção**.
2. Selecione **ao site de recuperação**e selecione **Sim, com o Hyper-V**.
3. Selecione **Sim** para indicar que está a utilizar o VMM para gerir os anfitriões Hyper-V.
4. Selecione **Sim** se tiver um servidor secundário do VMM. Se estiver a implementar a replicação entre nuvens num único servidor VMM, clique em **não**. Em seguida, clique em **OK**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Instalar o fornecedor do Azure Site Recovery nos servidores do VMM, detetar e registar os servidores no cofre.

1. Clique em **Preparar a Infraestrutura** > **Origem**.
2. Em **Preparar a origem**, clique em **+ VMM** para adicionar um servidor VMM.
3. No **Adicionar servidor**, verifique se **servidor VMM do System Center** aparece no **tipo de servidor**.
4. Transfira o ficheiro de instalação do Fornecedor do Azure Site Recovery.
5. Transfira a chave de registo. Isto tem quando instalar o fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/tutorial-vmm-to-vmm/source-settings.png)

6. Instale o fornecedor em cada servidor VMM. Não precisa de instalar qualquer coisa explicitamente em anfitriões Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Instalar o fornecedor do Azure Site Recovery

1. Execute o fornecedor de ficheiro de configuração em cada servidor VMM. Se o VMM for implementado num cluster, instale a primeira da seguinte forma:
    -  Instale o fornecedor no nó ativo e conclua a instalação para registar o servidor VMM no cofre.
    - Em seguida, instale o fornecedor nos outros nós. Todos os nós de cluster devem executar a mesma versão do fornecedor.
2. A configuração executa algumas verificações de pré-requisitos e pede permissão para parar o serviço do VMM. O serviço VMM será reiniciado automaticamente após a conclusão do programa de configuração. Se instalar num VMM cluster, está a pedido para parar a função de Cluster.
3. No **Microsoft Update**, pode optar por especificar que as atualizações do fornecedor são instaladas de acordo com a política do Microsoft Update.
4. No **instalação**, aceite ou modifique a localização de instalação predefinida e clique em **instalar**.
5. Depois de concluída a instalação, clique em **registar** para registar o servidor no cofre.

    ![Localização de instalação](./media/tutorial-vmm-to-vmm/provider-register.png)
6. Em **Nome do cofre**, verifique o nome do cofre no qual o servidor será registado. Clique em **Seguinte**.
7. No **ligação Proxy**, especifique a forma como o fornecedor em execução no servidor VMM estabelece ligação ao Azure.
   - Pode especificar que o fornecedor deve ligar diretamente à internet ou através de um proxy. Especifique as definições de proxy conforme necessário.
   - Se utilizar um proxy, uma conta RunAs do VMM (DRAProxyAccount) é criada automaticamente, utilizando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta possa autenticar-se com êxito. As definições da conta RunAs podem ser modificadas na consola do VMM > **definições** > **segurança** > **contas Run as**.
   - Reinicie o serviço do VMM ao atualizar as alterações.
8. No **chave de registo**, selecione a chave que tenham transferido e copiado para o servidor VMM.
9. A definição de encriptação não é relevante neste cenário. 
10. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Num cluster, especifique o nome de função de cluster do VMM.
11. No **sincronizar metadados da nuvem**, selecione de que pretende sincronizar os metadados para todas as nuvens no servidor do VMM. Esta ação só deverá ocorrer uma vez em cada servidor. Se não quiser sincronizar todas as nuvens, deixe esta definição desmarcada. Pode sincronizar cada nuvem individualmente, nas propriedades de nuvem na consola do VMM.
12. Clique em **Seguinte** para concluir o processo. Após o registo, recuperação de Site obtém os metadados do servidor VMM. O servidor é apresentado no **servidores** > **servidores VMM** no cofre.
13. Depois do servidor é apresentado no cofre, no **origem** > **preparar a origem** selecione o servidor VMM e selecione a nuvem em que o anfitrião de Hyper-V se encontra. Em seguida, clique em **OK**.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione o servidor VMM de destino e a nuvem:

1. Clique em **preparar a infraestrutura** > **destino**e selecione o servidor do VMM de destino.
2. As nuvens do VMM que são sincronizadas com a recuperação de Site são apresentadas. Selecione a nuvem de destino.

   ![destino](./media/tutorial-vmm-to-vmm/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

Antes de começar, certifique-se de que todos os anfitriões através da política de tem o mesmo sistema operativo. Se existirem anfitriões com versões diferentes do Windows Server, terá de várias políticas de replicação.

1. Para criar uma nova política de replicação, clique em **Preparar a infraestrutura** > **Definições de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. O tipo de origem e de destino deve ser **Hyper-V**.
3. No **versão de anfitrião do Hyper-V**, selecione o sistema operativo que está em execução no anfitrião.
4. No **tipo de autenticação** e **porta de autenticação**, especifique a forma como o tráfego for autenticado entre os servidores de anfitrião de Hyper-V principal e de recuperação.
    - Selecione **certificado** a menos que tenha um Kerberos ambiente de trabalho. O Azure Site Recovery irá configurar automaticamente certificados para autenticação HTTPS. Não precisa de fazer nada manualmente.
    - Por predefinição, a porta 8083 e 8084 (para certificados) será aberta na Firewall do Windows nos servidores de anfitrião de Hyper-V.
    - Se selecionar **Kerberos**, será utilizada uma permissão Kerberos para autenticação mútua dos servidores de anfitrião. Kerberos só é relevante para servidores de anfitrião de Hyper-V em execução no Windows Server 2012 R2 ou posterior.
1. Em **Frequência de cópia**, especifique a frequência com que pretende replicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
2. No **retenção do ponto de recuperação**, especifique \how período de tempo (em horas) da janela de retenção será para cada ponto de recuperação. Máquinas replicadas podem ser recuperadas para qualquer ponto nessa janela.
3. No **frequência de instantâneos consistentes com aplicação**, especifique a frequência (1-12 horas) pontos de recuperação que contêm instantâneos consistentes com aplicações são criados. Hyper-V utiliza dois tipos de instantâneos:
    - **Instantâneo padrão**: Fornece um instantâneo incremental de toda a máquina virtual.
    - **Instantâneos consistentes com aplicação**: tira um instantâneo de ponto no tempo dos dados de aplicação dentro da VM. Serviço de cópia de sombra de volume (VSS) assegura que aplicações estão num estado consistente quando se obtém o instantâneo. Ativar instantâneos consistentes com aplicações, afetará o desempenho de aplicações em VMs de origem. Defina um valor que é inferior ao número de pontos de recuperação adicionais que configura.
4. No **compressão de transferência de dados**, especifique se os dados de replicação transferidos devem ser comprimidos.
5. Selecione **Eliminar réplica VM**, para especificar que a máquina virtual de réplica deve ser eliminada se desativar a proteção para a VM de origem. Se ativar esta definição, quando desativar a proteção para a VM, é removido da consola da recuperação de Site de origem, as definições de recuperação de Site para o VMM são removidas da consola do VMM e a réplica foi eliminada.
6. No **inicial método de replicação**, se está a replicar através da rede, especifique se pretende iniciar a replicação inicial ou agendá-la. Para guardar a largura de banda de rede, pode querer agendá-la fora do horário de trabalho. Em seguida, clique em **OK**.

     ![Política de replicação](./media/tutorial-vmm-to-vmm/replication-policy.png)
     
7. A nova política é automaticamente associada à nuvem VMM. No **política de replicação**, clique em **OK**. 


## <a name="enable-replication"></a>Ativar a replicação

1. Clique em **replicar aplicação** > **origem**. 
2. No **origem**, selecione o servidor VMM e a nuvem em que estão localizados os anfitriões de Hyper-V que pretende replicar. Em seguida, clique em **OK**.
3. No **destino**, certifique-se de que o servidor secundário do VMM e a nuvem.
4. No **máquinas virtuais**, selecione as VMs que pretende proteger na lista.


Pode controlar o progresso do **ativar proteção** ação no **tarefas** > **as tarefas de recuperação de Site**. Depois do **finalizar proteção** tarefa é concluída, a replicação inicial está concluída e a VM está preparada para ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes

[Executar um teste de recuperação após desastre](tutorial-dr-drill-secondary.md)
