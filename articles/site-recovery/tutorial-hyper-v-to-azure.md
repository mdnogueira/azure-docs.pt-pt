---
title: "Configurar a recuperação após desastre para as VMs de Hyper-V no local para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre de VMs de Hyper-V no local para o Azure com o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 4d43fb03ce1c54a47315b8c3a5c83ec2082bcab9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação de desastre de VMs de Hyper-V no local para Azure

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre, gerir e da orquestração de replicação, ativação pós-falha e a reativação pós-falha de máquinas no local e máquinas de virtuais (VMs) do Azure.

Este tutorial mostra como configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure. O tutorial é relevante para as VMs de Hyper-V que são geridos em nuvens do System Center Virtual Machine Manager (VMM) e que não estão. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar os pré-requisitos do Azure e no local
> * Criar um cofre dos serviços de recuperação para a recuperação de sites 
> * Configurar a origem e ambientes de replicação de destino 
> * Configurar o mapeamento de rede (se o Hyper-V é gerido pelo VMM do System Center)
> * Criar uma política de replicação
> * Ativar a replicação para uma VM


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Certifique-se de que compreende o [arquitetura do cenário e componentes](concepts-hyper-v-to-azure-architecture.md).
- Reveja o [suportar requisitos](site-recovery-support-matrix-to-azure.md) para todos os componentes.
- Certifique-se de que estão em conformidade com as VMs que pretende replicar [requisitos de VM do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Para descobrir o planeamento de capacidade:
    - Utilize o [ferramenta do Hyper-V Capacity Planner](http://aka.ms/asr-capacity-planner-excel)para descobrir a taxa de alteração.
    - Utilize o [Planeador de capacidade de recuperação de Site](http://aka.ms/asr-capacity-planner-excel) para analisar o seu ambiente de origem, estimar a largura de banda e requisitos de destino.
- Prepare o Azure. Precisa de uma subscrição do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- Prepare anfitriões de Hyper-V no local e servidores do VMM, se forem relevantes.





### <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

Obter um Microsoft [conta do Azure](http://azure.microsoft.com/).

- Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre [preços da recuperação de Site](site-recovery-faq.md#pricing)e obter [detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Saber que [regiões são suportadas](https://azure.microsoft.com/pricing/details/site-recovery/) para a recuperação de Site.

### <a name="verify-azure-account-permissions"></a>Verifique as permissões de conta do Azure

Certifique-se a que sua conta do Azure tem as permissões necessita de replicar as VMs.

- Reveja o [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) terá de replicar máquinas para o Azure
- Certifique-se e modificar [acesso baseado em funções](../active-directory/role-based-access-control-configure.md) permissões. 


### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configurar um [rede Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- As VMs do Azure são colocadas nesta rede quando são criados após a ativação pós-falha.
- A rede deve estar mesma localização que o cofre dos Serviços de Recuperação.


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Configurar um [conta do storage do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Recuperação de site são replicados máquinas no local para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após a ocorrência da ativação pós-falha.
- A conta de armazenamento tem de ser na mesma região que o Cofre dos serviços de recuperação.
- A conta de armazenamento pode ser padrão ou [premium](../virtual-machines/windows/premium-storage.md).
- Se configurar uma conta de premium, precisa de uma conta standard adicional para dados de registo.

### <a name="prepare-hyper-v-hosts"></a>Preparar anfitriões Hyper-V

1. Certifique-se de que cumpram de anfitriões Hyper-V [suportar requisitos](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Certifique-se de que anfitriões podem aceder a estes URLs:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Quaisquer regras de firewall baseadas no endereço IP devem permitir a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita intervalos de endereços IP para a região do Azure da sua subscrição e para EUA oeste (utilizado para gestão de identidade e controlo de acesso).

### <a name="prepare-vmm-servers"></a>Preparar servidores VMM

Se os anfitriões de Hyper-V são geridos pelo VMM, terá de preparar o servidor do VMM no local. 

- Certifique-se de que o servidor VMM cumpre [suportar requisitos](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Certifique-se de que o servidor VMM tem, pelo menos, uma nuvem, com um ou mais grupos de anfitriões. O anfitrião de Hyper-V no qual as VMs estão em execução deve estar localizado na nuvem.
- O servidor do VMM necessita de acesso à internet, com o acesso aos seguintes URLs:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Quaisquer regras de firewall baseadas no endereço IP devem permitir a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita os intervalos de endereços IP da região do Azure da sua subscrição e para a região E.U.A. Oeste (utilizada para Controlo de Acesso e Gestão de Identidades).
- Prepare o servidor VMM para o mapeamento da rede.


#### <a name="prepare-vmm-for-network-mapping"></a>Preparar o VMM para o mapeamento da rede

Se estiver a utilizar o VMM, [mapeamento da rede](site-recovery-network-mapping.md) mapeia entre as redes VM do VMM no local e redes virtuais do Azure. Mapeamento assegura que as VMs do Azure estão ligadas à rede à direita quando são criados após a ativação pós-falha.

Prepare VMM mapeamento da rede da seguinte forma:

1. Certifique-se de que tem um [rede lógica de VMM](https://docs.microsoft.com/system-center/vmm/network-logical) que tiver associado à nuvem em que estão localizados os anfitriões de Hyper-V.
2. Certifique-se de que tem um [rede VM](https://docs.microsoft.com/system-center/vmm/network-virtual) ligado à rede lógica.
3. Ligar as VMs para a rede VM.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Selecione um objetivo de proteção

Selecione o que a replicação e onde pretende replicar para.

1. No cofre, clique em **recuperação de Site** > **preparar infraestrutura** > **objetivo de proteção**.
2. No **objetivo de proteção**, selecione **para o Azure**> **Sim, com o Hyper-V**. 
    - Se os anfitriões Hyper-V não são geridas pelo VMM, selecione **não** para confirmar que não está a utilizar o VMM.
    - Se os anfitriões são geridos em nuvens VMM, selecione **Sim**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando configurar o ambiente de origem, pode instalar o fornecedor do Azure Site Recovery e agente dos serviços de recuperação do Azure e registar servidores no local no cofre. 

1. No **preparar infraestrutura**, clique em **origem**. 
    - Se não estiver a utilizar o VMM, clique em **+ Site Hyper-V**e especifique um nome de site. Em seguida, clique em **+ servidor Hyper-V**e adicione um anfitrião ou cluster para o site.
    - Se estiver a utilizar o VMM, na **preparar a origem**, clique em **+ VMM** para adicionar um servidor VMM. No **Adicionar servidor**, verifique se **servidor VMM do System Center** aparece no **tipo de servidor**.
2. Transferir os componentes do fornecedor e agente, dependendo do seu ambiente.
    - Para o Hyper-V apenas, transfira o ficheiro de instalação do fornecedor. Execute o ficheiro em cada anfitrião de Hyper-V para instalar o fornecedor e o agente.
        
        ![Fornecedor sem o VMM](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Para o Hyper-V com o VMM, transfira o fornecedor e o agente separadamente. Execute a instalação do fornecedor no servidor do VMM. Execute a instalação do agente em cada anfitrião de Hyper-V.
    
        ![O fornecedor e agente com o VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Transferir a chave de registo do cofre. Isto tem quando executar a configuração do fornecedor. A chave é válida durante cinco dias depois de gerá-la.

### <a name="install-components"></a>Instalar componentes do

Instale os componentes como summmarized na tabela. 

**Componente** | **Detalhes** | **Hyper-V apenas** | **Hyper-V com o VMM**
--- | --- | --- | ---
**Fornecedor do Azure Site Recovery** | Orquestra a replicação para o Azure | Instalar em cada anfitrião de Hyper-V | Instalar no servidor do VMM
**Agente de serviços de recuperação** | Processa a replicação de dados | Instalar em cada anfitrião de Hyper-V | Instalar no anfitrião Hyper-V


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Instale o fornecedor no Hyper-V sem o VMM

Instale o fornecedor em cada anfitrião de Hyper-V adicionadas para o site Hyper-V. Se estiver a instalar num cluster de Hyper-V, execute a configuração em cada nó de cluster. Instalar e registar cada nó de cluster do Hyper-V assegura que as VMs estão protegidas, mesmo que o se migrar entre nós.

1. Em **Microsoft Update**, pode optar pelas atualizações de modo a que as atualizações do Provider sejam instaladas de acordo com a sua política do Microsoft Update.
2. Em **Instalação**, aceite ou modifique a localização de instalação do Fornecedor predefinida e clique em **Instalar**.
4. No **as definições do cofre**, clique em **procurar** para selecionar o ficheiro de chave do cofre que transferiu. Especifique a subscrição do Azure Site Recovery, o nome do cofre e o site Hyper-V ao qual pertence o servidor de Hyper-V.
5. No **as definições de Proxy**, especifique como o fornecedor em execução no servidor do VMM ou anfitrião Hyper-V se liga a recuperação de sites através da internet.
    * Para uma ligação direta, selecione **ligar diretamente ao Azure Site Recovery sem um proxy**.
    * Para um proxy, selecione **ligar ao Azure Site Recovery com um servidor proxy**. Especifique o endereço do proxy, porta e credenciais, se necessário.
6. Depois do servidor está registado no cofre, clique em **concluir**.

Os metadados do servidor de Hyper-V são obtidos pelo Azure Site Recovery e o servidor é apresentado no **infraestrutura de recuperação de Site** > **anfitriões Hyper-V**.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Instale o agente dos serviços de recuperação no anfitrião de Hyper-V sem o VMM

Se estiver a utilizar o VMM na sua implementação, execute a configuração do agente de serviços de recuperação em cada anfitrião de Hyper-V.

1. O Assistente de configuração > **verificação de pré-requisitos**, clique em **seguinte**. Os pré-requisitos em falta serão instalados automaticamente.

    ![Pré-requisitos do Agente dos Serviços de Recuperação](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. Em **Definições da Instalação**, aceite ou modifique a localização da instalação e a localização da cache. A unidade de cache necessita de, pelo menos, 5 GB de armazenamento. Recomendamos que uma unidade com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
4. No **instalação**, quando a instalação estiver concluída, clique em **fechar** para concluir o assistente.

##### <a name="set-up-internet-access-via-a-proxy"></a>Configurar o acesso à internet através de um proxy

O agente dos serviços de recuperação no anfitrião Hyper-V necessita de acesso à internet para o Azure para replicação de VM. Se está a aceder à Internet através de um proxy, configure-a da seguinte forma:

1. Abra o snap-in MMC da Cópia de Segurança do Microsoft Azure no anfitrião Hyper-V. Por predefinição, um atalho para cópia de segurança do Microsoft Azure está disponível no ambiente de trabalho, ou em c:\Programas\Microsoft c:\Programas\Microsoft Azure Recovery Services agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. No **configuração de Proxy** separador, especifique as informações do proxy.

    ![Registar o Agente MARS](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Verifique se o agente pode contactar o [necessários URLs](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Instale o fornecedor no servidor do VMM (Hyper-V com o VMM)

1. Em **Microsoft Update**, pode optar pelas atualizações de modo a que as atualizações do Provider sejam instaladas de acordo com a sua política do Microsoft Update.
2. Em **Instalação**, aceite ou modifique a localização de instalação do Fornecedor predefinida e clique em **Instalar**. 
3. Agora registe o servidor VMM no cofre. No **as definições do cofre**, clique em **procurar** para selecionar o ficheiro de chave do cofre que transferiu. Especifique a subscrição do Azure Site Recovery e o nome do cofre.

    ![Registo do servidor](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. No **as definições de Proxy**, especifique como o fornecedor em execução no servidor do VMM ou anfitrião Hyper-V se liga a recuperação de sites através da internet.

    * Para uma ligação direta, selecione **ligar diretamente ao Azure Site Recovery sem um proxy**.
    * Para um proxy, selecione **ligar ao Azure Site Recovery com um servidor proxy**. Especifique o endereço do proxy, porta e credenciais, se necessário.
    - Uma conta RunAs do VMM (DRAProxyAccount) é automaticamente criada utilizando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta possa autenticar-se com êxito. As definições da conta RunAs podem ser modificadas na consola do VMM > **definições** > **segurança** > **contas Run as**. Reinicie o serviço do VMM ao atualizar as alterações.
5. No **encriptação de dados**, especifique se pretende encriptados todos os dados enviados para o Azure. Se selecionar esta opção que emite um certificado para recuperação de sites. Terá este certificado para desencriptar os dados mais tarde.
6. No **servidor VMM**, especifique um nome amigável para identificar o servidor VMM no cofre. Numa configuração de cluster, especifique o nome da função de cluster do VMM. No **sincronizar metadados da nuvem com o Cofre**, selecione de que pretende sincronizar os metadados para todas as nuvens no servidor do VMM com o cofre. Esta ação só deverá ocorrer uma vez em cada servidor. Se não quiser sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades de nuvem na consola do VMM.

Após a conclusão de registo, os metadados do servidor são obtidos pelo Azure Site Recovery e o servidor VMM é apresentado na **infraestrutura de recuperação de Site**.






## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e certifique-se de recursos de destino. 

1. Clique em **preparar a infraestrutura** > **destino**.
2. Selecione a subscrição e o grupo de recursos no qual serão criadas as VMs do Azure após a ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure para as VMs.

3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping-with-vmm"></a>Configurar o mapeamento de rede (com o VMM)

Se estiver a utilizar o VMM, configure o mapeamento da rede.

1. Em **Infraestrutura do Site Recovery** > **Mapeamentos da Rede** > **Mapeamento da Rede**, clique no ícone **+ Mapeamento da Rede**.
2. No **adicionar mapeamento da rede**, selecione o servidor do VMM de origem. Selecione **Azure** como destino.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. No **rede de origem**, selecione a rede VM de origem no local.
5. No **rede de destino**, selecione a rede Azure no qual as VMs da réplica do Azure estarão localizada quando são criados após a ativação pós-falha. Em seguida, clique em **OK**.

    ![Mapeamento da rede](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique em **preparar a infraestrutura** > **as definições de replicação** > **+ criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique a frequência com que pretende replicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).

    > [!NOTE]
    >  Ao replicar para o armazenamento premium, a frequência de 30 segundos não é suportada. A limitação é determinada pelo número de instantâneos por blob (cem) que o armazenamento premium suporta. [Saiba mais](../virtual-machines/windows/premium-storage.md#snapshots-and-copy-blob).

4. No **retenção do ponto de recuperação**, especifique a forma como a janela de retenção será a duração (em horas) para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto nessa janela.
5. Em **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência (1 a 12 horas) com que os pontos de recuperação que contêm os instantâneos consistentes com aplicações serão criados. Hyper-V utiliza dois tipos de instantâneos:
    - **Instantâneo padrão**: Fornece um instantâneo incremental de toda a máquina virtual.
    - **Instantâneos consistentes com aplicação**: tira um instantâneo de ponto no tempo dos dados de aplicação dentro da VM. Serviço de cópia de sombra de volume (VSS) assegura que aplicações estão num estado consistente quando se obtém o instantâneo. Ativar instantâneos consistentes com aplicações afeta o desempenho de aplicações em VMs de origem. Defina um valor que é inferior ao número de pontos de recuperação adicionais que configura.
6. Em **Hora de início da replicação inicial**, especifique quando deve começar a replicação inicial. Replicação ocorre através da sua largura de banda de internet, pelo que poderá querer agendá-la fora do horário de pico.
7. Em **Encriptar dados armazenados no Azure**, especifique se pretende encriptar os dados REST no armazenamento do Azure. Em seguida, clique em **OK**.

    ![Política de replicação](./media/tutorial-hyper-v-to-azure/replication-policy.png)


Quando cria uma nova política está associado automaticamente com a nuvem VMM ou o site Hyper-V.

## <a name="enable-replication"></a>Ativar a replicação


1. Clique em **replicar aplicação** > **origem**. 
2. No **origem**, selecione o servidor/nuvem do site/VMM Hyper-V. Em seguida, clique em **OK**.
3. No **destino**, certifique-se do Azure como o destino, a subscrição do cofre e o modelo que pretende utilizar no Azure após a ativação pós-falha.
4. Selecione a conta de armazenamento para dados replicados e a rede do Azure na qual as VMs do Azure estarão localizadas após a ativação pós-falha.
5. No **máquinas virtuais** > **selecione**, selecione as VMs que pretende replicar. Em seguida, clique em **OK**.

 Pode controlar o progresso do **ativar proteção** ação no **tarefas** > **as tarefas de recuperação de Site**. Depois do **finalizar proteção** tarefa é concluída, a replicação inicial está concluída e a máquina virtual está preparada para ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
[Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
