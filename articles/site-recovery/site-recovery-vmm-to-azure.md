---
title: Replicar VMs de Hyper-V nas clouds do VMM para o| Microsoft Docs
description: "Orquestrar a replicação, a ativação pós-falha e a recuperação de VMs de Hyper-V geridas em clouds do VMM do System Center para o Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 958b61f5de732a882e0a2682b8dd4e18504a6ae7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Replicar máquinas virtuais de Hyper-V em nuvens do VMM para o Azure com o Site Recovery no portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-azure.md)
> * [Azure clássico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell clássico](site-recovery-deploy-with-powershell.md)


Este artigo descreve como replicar máquinas virtuais de Hyper-V no local geridas em clouds do System Center VMM para o Azure ao utilizar o serviço do [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Depois de ler este artigo, publique comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Se quiser migrar máquinas para o Azure (sem ativação pós-falha), saiba mais [neste artigo](site-recovery-migrate-to-azure.md).


## <a name="deployment-steps"></a>Passos da implementação

Siga o artigo para concluir estes passos da implementação:


1. [Saiba mais](site-recovery-components.md) sobre a arquitetura desta implementação. Além disso, [veja](site-recovery-hyper-v-azure-architecture.md) como funciona a replicação de Hyper-V no Site Recovery.
2. Verifique os pré-requisitos e as limitações.
3. Configure uma rede do Azure e contas de armazenamento.
4. Prepare o servidor VMM no local e os anfitriões de Hyper-V.
5. Crie um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e orquestra a replicação.
6. Especifique as definições da origem. Registe o servidor VMM no cofre. Instale o Azure Site Recovery Provider no servidor VMM e o agente dos Serviços de Recuperação da Microsoft nos anfitriões de Hyper-V.
7. Configure as definições do destino e da replicação.
8. Ativa a replicação nas VMs.
9. Execute uma ativação pós-falha de teste, para confirmar que está tudo a funcionar conforme o previsto.



## <a name="prerequisites"></a>Pré-requisitos


**Requisito de suporte** | **Detalhes**
--- | ---
**Azure** | Saiba mais sobre os [requisitos do Azure](site-recovery-prereq.md#azure-requirements).
**Servidores no local** | [Saiba mais](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure) sobre os requisitos para o servidor VMM no local e os anfitriões de Hyper-V.
**VMs de Hyper-V no local** | As VMs que pretender replicar devem executar um [sistema operativo suportado](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs do Azure** | O servidor VMM precisa de acesso a estes URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se tiver regras de firewall baseadas no endereço IP, verifique que permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP da região do Azure da sua subscrição e para a região E.U.A. Oeste (utilizada para Controlo de Acesso e Gestão de Identidades).


## <a name="prepare-for-deployment"></a>Preparar para a implementação
Para se preparar para a implementação, é necessário:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual as VMs do Azure serão localizadas após a ativação pós-falha.
2. [Configurar uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para dados replicados.
3. [Preparar o servidor VMM](#prepare-the-vmm-server) para a implementação de Recuperação de Sites.
4. Preparar o mapeamento da rede. Configure redes de modo a poder configurar o mapeamento da rede durante a implementação da Recuperação de Sites.

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure
Necessita de uma rede do Azure à qual as VMs do Azure criadas depois da ativação pós-falha se liguem.

* A rede deve estar mesma localização que o cofre dos Serviços de Recuperação.
* Consoante o modelo de recursos que pretende utilizar nas VMs do Azure com ativação pós-falha, configure a rede Azure no [modo Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* Recomendamos que configure uma rede antes de começar. Caso contrário, terá de fazê-lo durante a implementação da Recuperação de Sites.
As redes Azure utilizadas pelo Site Recovery não podem ser [movidas](../azure-resource-manager/resource-group-move-resources.md) dentro de grupos de recursos nas mesmas ou em diferentes subscrições.

### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure
* Precisa de uma conta de armazenamento do Azure standard/premium para guardar os dados replicados no Azure. O [Armazenamento premium](../storage/common/storage-premium-storage.md) é utilizado para máquinas virtuais que precisam de um desempenho de E/S elevado consistentemente e baixa latência para alojar cargas de trabalho de E/S intensivo. Se quiser utilizar uma conta de armazenamento premium para armazenar os dados replicados, também precisa de uma conta de armazenamento standard para armazenar os registos de replicações que capturam as alterações que são feitas continuamente aos dados no local. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação.
* Consoante o modelo de recursos que pretende utilizar para efetuar a ativação pós-falha nas VMs do Azure, configure uma conta no [modo Resource Manager](../storage/common/storage-create-storage-account.md) ou no [modo clássico](../storage/common/storage-create-storage-account.md).
* Recomendamos que configure uma conta antes de começar. Caso contrário, terá de fazê-lo durante a implementação da Recuperação de Sites.
- Tenha em consideração que as contas de armazenamento utilizadas do Site Recovery não podem ser [movidas](../azure-resource-manager/resource-group-move-resources.md) dentro de grupos de recursos nas mesmas ou em diferentes subscrições.

### <a name="prepare-the-vmm-server"></a>Preparar o servidor VMM
* Certifique-se de que o servidor VMM está em conformidade com os [pré-requisitos](#prerequisites).
* Durante a implementação da Recuperação de Sites, pode especificar que todas as nuvens num servidor VMM estão disponíveis no portal do Azure. Se pretende que apenas determinadas nuvens sejam apresentadas no portal, pode ativar essa definição na nuvem na consola do administrador VMM.

### <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede
Tem de configurar o mapeamento da rede durante a implementação do Site Recovery. O mapeamento da rede faz o mapeamento entre as redes de VM do VMM de origem e as redes do Azure de destino para permitir o seguinte:

* As máquinas que efetuem a ativação pós-falha na mesma rede podem ligar-se entre si, mesmo que não tenham ativação pós-falha da mesma forma ou estejam no mesmo plano de recuperação.
* Se um gateway de rede está configurado na rede de destino do Azure, as máquinas virtuais do Azure podem ligar-se a máquinas virtuais no local.
* Para configurar o mapeamento da rede, precisa do seguinte:

  * Certifique-se de que as VMs no servidor de anfitrião Hyper-V de origem estão ligadas a uma rede VM do VMM. Essa rede deve ser ligada a uma rede lógica que está associada à nuvem.
  * Um rede de Azure conforme descrito [acima](#set-up-an-azure-network)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Monitorização + Gestão** > **Backup e Site Recovery (OMS)**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. [Crie um grupo de recursos](../azure-resource-manager/resource-group-template-deploy-portal.md) ou selecione um existente. Selecione uma região do Azure. As máquinas serão replicadas para esta região. Para verificar as regiões suportadas veja Disponibilidade Geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Se pretender aceder rapidamente ao cofre a partir do Dashboard, clique em **Afixar ao dashboard** > **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault.png)

O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e no painel principal **Cofres dos Serviços de Recuperação**.


## <a name="select-the-protection-goal"></a>Selecionar o objetivo de proteção

Selecione o que pretende replicar e para onde.

1. Em **Cofres dos Serviços de Recuperação**, selecione o cofre.
2. Em **Introdução**, clique em **Site Recovery** > **Preparar a Infraestrutura** > **Objetivo de proteção**.

    ![Selecione os objetivos](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. Em **Objetivo de proteção**, selecione **Para o Azure** e, em seguida, selecione **Sim, com o Hyper-V**. Selecione **Sim** para confirmar que está a utilizar o VMM para gerir anfitriões Hyper-V e o site de recuperação. Em seguida, clique em **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Instale o Fornecedor do Azure Site Recovery no servidor VMM e registe o servidor no cofre. Instale o agente do Serviços de Recuperação do Azure nos anfitriões Hyper-V

1. Clique em **Preparar a Infraestrutura** > **Origem**.

    ![Configurar a origem](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Em **Preparar a origem**, clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar a origem](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Em **Adicionar Servidor**, verifique se o **Servidor do System Center VMM** aparece em **Tipo de servidor** e se o servidor VMM cumpre os [pré-requisitos e os requisitos de URLs](#prerequisites).
4. Transfira o ficheiro de instalação do Fornecedor do Azure Site Recovery.
5. Transfira a chave de registo. Precisará disto quando executar a configuração. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/site-recovery-vmm-to-azure/set-source3.png)


## <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. Execute o ficheiro de configuração do Fornecedor no servidor VMM.
2. Em **Microsoft Update**, pode optar pelas atualizações de modo a que as atualizações do Provider sejam instaladas de acordo com a sua política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique a localização de instalação do Fornecedor predefinida e clique em **Instalar**.

    ![Localização de instalação](./media/site-recovery-vmm-to-azure/provider2.png)
4. Quando a instalação for concluída, clique em **Registar** para registar o servidor VMM no cofre.
5. Na página **Definições do Cofre**, clique em **Procurar** para selecionar o ficheiro de chave do cofre. Especifique a subscrição do Azure Site Recovery e o nome do cofre.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. Em **Ligação à Internet**, especifique a forma como o Fornecedor em execução no servidor VMM estabelece ligação à Recuperação de Sites através da Internet.

   * Se pretender que o Provider se ligue diretamente, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
   * Se o seu proxy existente precisar de autenticação ou se quiser utilizar um proxy personalizado, selecione **Ligar ao Azure Site Recovery com um servidor proxy**.
   * Se utilizar um proxy personalizado, especifique o endereço, a porta e as credenciais.
   * Se estiver a utilizar um proxy, já deverá ter permitido os URLs descritos em [pré-requisitos](#on-premises-prerequisites).
   * Se utilizar um proxy personalizado, será automaticamente criada uma conta RunAs do VMM (DRAProxyAccount) com as credenciais de proxy especificadas. Configure o servidor proxy para que esta conta possa autenticar-se com êxito. As definições de conta RunAs do VMM podem ser modificadas na consola do VMM. Em **Definições**, expanda **Segurança** > **Contas Run As** e modifique a palavra-passe de DRAProxyAccount. Terá de reiniciar o serviço VMM para que esta definição surta efeito.

     ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. Aceite ou modifique a localização de um certificado SSL que é gerado automaticamente para a encriptação de dados. Este certificado é utilizado se ativar a encriptação de dados para uma nuvem protegida pelo Azure no portal do Azure Site Recovery. Mantenha este certificado num local seguro. Quando executar uma ativação pós-falha para o Azure, precisará das mesmas para desencriptar, caso a encriptação de dados esteja ativada.

    > [!NOTE]
    > Recomenda-se que utilize a capacidade de encriptação que o Azure fornece para encriptar os dados inativos, em vez de utilizar a opção de encriptação de dados disponibilizada pelo Azure Site Recovery. A capacidade de encriptação fornecida pelo Azure pode ser ativada numa conta de armazenamento e ajuda a obter um desempenho melhor, uma vez que é o armazenamento do Azure que trata da encriptação/desencriptação.
    > [Saiba mais sobre a encriptação do serviço de Armazenamento do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).
    
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Numa configuração de cluster, especifique o nome da função de cluster do VMM.
9. Ative **Sincronizar metadados de nuvem** se pretender sincronizar os metadados de todas as nuvens no servidor VMM com o cofre. Esta ação só deverá ocorrer uma vez em cada servidor. Se não quiser sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades de nuvem na consola do VMM. Clique em **Registar** para concluir o processo.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. Inicia-se o registo. Após a conclusão do registo, o servidor é apresentado na **Infraestrutura do Site Recovery** > **Servidores VMM**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Instale o agente dos Serviços de Recuperação do Azure nos anfitriões Hyper-V

1. Após ter configurado o Fornecedor, tem de transferir o ficheiro de instalação para o agente dos Serviços de Recuperação do Azure. Execute a configuração em cada servidor Hyper-V na nuvem VMM.

    ![Sites Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. Em **Verificação de Pré-requisitos**, clique em **Seguinte**. Os pré-requisitos em falta serão instalados automaticamente.

    ![Pré-requisitos do Agente dos Serviços de Recuperação](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. Em **Definições da Instalação**, aceite ou modifique a localização da instalação e a localização da cache. Pode configurar a cache numa unidade que tem, pelo menos, 5 GB de armazenamento disponível, mas recomenda-se uma unidade de cache com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
4. Após a conclusão da instalação, clique em **Fechar** para concluir.

    ![Registar o Agente MARS](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

### <a name="command-line-installation"></a>Instalação com linha de comandos
Pode instalar o Agente dos Serviços de Recuperação do Microsoft Azure a partir da linha de comandos utilizando o comando seguinte:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configure o acesso de proxy da Internet para Recuperação de Sites a partir de anfitriões Hyper-V

O agente dos Serviços de Recuperação em execução em anfitriões Hyper-V tem acesso à Internet para o Azure para a replicação de VM. Se está a aceder à Internet através de um proxy, configure-a da seguinte forma:

1. Abra o snap-in MMC da Cópia de Segurança do Microsoft Azure no anfitrião Hyper-V. Por predefinição, está disponível um atalho para o Microsoft Azure Backup no ambiente de trabalho ou em C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. No separador **Configuração de Proxy**, especifique as informações do servidor proxy.

    ![Registar o Agente MARS](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. Confirme que o agente consegue contactar os URLs descritos nos [pré-requisitos](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino
Especifique a conta de armazenamento do Azure a ser utilizada para replicação e a rede Azure ao qual as VMs do Azure estabelecerão ligação após a ativação pós-falha.

1. Clique em **Preparar infraestrutura** > **Destino**, selecione a subscrição e o grupo de recursos em que pretende criar máquinas virtuais de ativação pós-falha. Selecione o modelo de implementação que pretende utilizar no Azure (clássica ou gestão de recursos) para a máquinas virtuais de ativação pós-falha.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/compatible-storage.png)

3. Se ainda não criou uma conta de armazenamento e quiser criar uma com o Resource Manager, clique em **+Conta de armazenamento**, para fazê-lo inline.  No painel **Criar conta de armazenamento**, especifique um nome de conta, o tipo, a subscrição e a localização. A conta tem de ter a mesma localização que o cofre de Serviços de Recuperação.

   ![Armazenamento](./media/site-recovery-vmm-to-azure/gs-createstorage.png)


   * Se quiser criar uma conta de armazenamento com o modelo clássico, pode fazê-lo no portal do Azure. [Saiba mais](../storage/common/storage-create-storage-account.md)
   * Se estiver a utilizar uma conta de armazenamento premium para os dados replicados, configure uma conta de armazenamento standard adicional para armazenar os registos de replicação que capturam as alterações em curso dos dados no local.
5. Se ainda não criou uma rede do Azure e pretende criar uma com o Resource Manager, clique em **+Rede**, para fazê-lo inline. No painel **Criar rede virtual**, especifique um nome de rede, o intervalo de endereços, os detalhes da sub-rede, a subscrição e a localização. A rede tem de ter a mesma localização que o cofre de Serviços de Recuperação.

   ![Rede](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   Se quiser criar uma rede com o modelo clássico, pode fazê-lo no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

* [Leia](#prepare-for-network-mapping) uma rápida descrição geral do que faz o mapeamento da rede.
* Confirme que as máquinas virtuais no servidor VMM estão ligadas a uma rede de VM e que criou, pelo menos, uma rede virtual do Azure. Várias redes VM podem ser mapeadas para uma única rede Azure.

Configure o mapeamento da seguinte forma:

1. Em **Infraestrutura do Site Recovery** > **Mapeamentos da Rede** > **Mapeamento da Rede**, clique no ícone **+ Mapeamento da Rede**.

    ![Mapeamento da rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. Em **Adicionar mapeamento da rede**, selecione o servidor VMM como a origem e o **Azure** como o destino.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. Em **Rede de origem**, selecione a rede VM de origem no local que pretende mapear para a lista associada ao servidor VMM.
5. Em **Rede de destino**, selecione a rede do Azure na qual as VMs da réplica do Azure serão localizadas quando forem criadas. Em seguida, clique em **OK**.

    ![Mapeamento da rede](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Veja a seguir o que acontece quando começa o mapeamento da rede:

* As VMs existentes na rede VM de origem estão ligadas à rede de destino quando o mapeamento é iniciado. As novas VMs ligadas à rede VM de origem aparecem ligadas à rede do Azure mapeada quando ocorrer a replicação.
* Se modificar um mapeamento de rede existente, as máquinas virtuais de réplica ligam-se com as definições novas.
* Se a rede de destino tiver várias sub-redes e um dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica liga-se a essa sub-rede de destino após a ativação pós-falha.
* Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual liga-se à primeira sub-rede da rede.

## <a name="configure-replication-settings"></a>Configurar as definições de replicação
1. Para criar uma nova política de replicação, clique em **Preparar a infraestrutura** > **Definições de Replicação** > **+Criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique a frequência com que pretende replicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).

    > [!NOTE]
    >  Ao replicar para o armazenamento premium, a frequência de 30 segundos não é suportada. A limitação é determinada pelo número de instantâneos por blob (cem) que o armazenamento premium suporta. [Saiba mais](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. As máquinas protegidas podem ser recuperadas para qualquer ponto nessa janela.
5. Em **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência (1 a 12 horas) com que os pontos de recuperação que contêm os instantâneos consistentes com aplicações serão criados. O Hyper-V utiliza dois tipos de instantâneos – um instantâneo padrão que fornece um instantâneo incremental de toda a máquina virtual e um instantâneo consistente com aplicações que cria um instantâneo pontual dos dados de aplicação no interior da máquina virtual. Os instantâneos consistentes com aplicações utilizam o Serviço de Cópia de Sombra de Volumes (VSS) para se certificar de que as aplicações estão num estado consistente quando se obtém o instantâneo. Tenha em atenção que se ativar instantâneos consistentes com aplicações, afetará o desempenho das aplicações em execução nas máquinas virtuais de origem. Certifique-se de que o valor que define é menor que o número de pontos de recuperação adicionais que está a configurar.
6. Em **Hora de início da replicação inicial**, especifique quando deve começar a replicação inicial. A replicação ocorre através da sua largura de banda de Internet, pelo que poderá querer agendá-la fora do horário de trabalho.
7. Em **Encriptar dados armazenados no Azure**, especifique se pretende encriptar os dados REST no armazenamento do Azure. Em seguida, clique em **OK**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. Quando cria uma nova política, automaticamente fica associada à nuvem do VMM. Clique em **OK**. Pode associar clouds adicionais do VMM (e as VMs) com esta política de replicação em **Replicação** > nome da política > **Associar Cloud do VMM**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="capacity-planning"></a>Planeamento de capacidade

Agora que tem a infraestrutura básica configurada, considere o planeamento da capacidade e averigue se precisa de recursos adicionais.

O Site Recovery disponibiliza um planeador de capacidade para o ajudar a alocar os recursos corretos ao seu ambiente de origem, aos componentes do Site Recovery, às redes e ao armazenamento. Pode executar o planeador no modo rápido para obter estimativas com base num número médio de VMs, discos e armazenamento, ou no modo detalhado no qual poderá especificar números para o nível da carga de trabalho. Antes de começar:

* Recolher informações sobre o ambiente de replicação, incluindo VMs, discos por VMs e armazenamento por disco.
* Calcular a taxa de alteração (renovação) diária para os dados replicados. Pode utilizar o [ Capacity Planner para as Réplicas do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) que o ajudar nesta tarefa.

1. Clique em **Transferir** para transferir a ferramenta e execute-a. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2. Quando terminar, selecione **Sim**, em **Executou o Capacity Planner**?

   ![Planeamento de capacidade](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

   Saiba mais sobre como [controlar a largura de banda de rede](#network-bandwidth-considerations)




## <a name="enable-replication"></a>Ativar a replicação

Antes de começar, certifique-se de que a sua conta de utilizador do Azure tem as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessárias para permitir a replicação de uma máquina virtual nova para o Azure.

Agora, ative a replicação da seguinte forma:

1. Clique em **Passo 2: Replicar aplicação** > **Origem**. Depois de ativar a replicação pela primeira vez, clique em **+Replicar**, no cofre, para ativar a replicação em máquinas adicionais.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. No painel **Origem**, selecione o servidor VMM e a nuvem na qual estão localizados os anfitriões de Hyper-V. Em seguida, clique em **OK**.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. Em **Destino**, selecione a subscrição, o modelo de implementação de ativação pós-falha e a conta de armazenamento que está a utilizar para os dados replicados.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. Clique na conta de armazenamento que pretende utilizar. Se quiser utilizar uma conta de armazenamento diferente das que tem, pode [criar uma](#set-up-an-azure-storage-account). Se estiver a utilizar uma conta de armazenamento premium para os dados replicados, tem de selecionar uma conta de armazenamento standard adicional para armazenar os registos de replicações que capturam as alterações que são feitas continuamente aos dados no local. Para criar uma conta de armazenamento com o modelo do Resource Manager, clique em **Criar nova**. Se quiser criar uma conta de armazenamento com o modelo clássico, pode fazê-lo [no portal do Azure](../storage/common/storage-create-storage-account.md). Em seguida, clique em **OK**.
5. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. Se quiser utilizar uma rede de armazenamento diferente das que tem, pode [criar uma](#set-up-an-azure-network). Para criar uma rede com o modelo do Resource Manager, clique em **Criar nova**. Se quiser criar uma rede com o modelo clássico, pode fazê-lo no [portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.
6. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication5.png)

7. Em **Propriedades** > **Configurar propriedades**, selecione o sistema operativo para as VMs selecionadas e o disco do sistema operativo.

    - Certifique-se de que o nome da VM do Azure (nome de destino) está em conformidade com os [requisitos de máquinas virtuais do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - Por predefinição, são selecionados todos os discos da VM para replicação, mas se quiser excluir algum disco, pode removê-lo.

        - Talvez tenha de remover discos para reduzir a largura de banda da replicação. Por exemplo, talvez não queira replicar discos com dados temporários ou dados que são atualizados sempre que uma máquina ou aplicação é reiniciada (como pagefile.sys ou Microsoft SQL Server tempdb). Pode impedir que o disco seja replicado ao desmarcá-lo.
        - Só os discos básicos podem ser excluídos. Não pode excluir discos de SO.
        - Recomendamos que não exclua discos dinâmicos. O Site Recovery não consegue identificar se um disco rígido virtual dentro de uma VM de convidado é básico ou dinâmico. Se todos os discos de volumes dinâmicos dependentes não forem excluídos, o disco dinâmico protegido aparecerá como um disco com falha quando é feita a ativação pós-falha da VM e os dados no mesmo não estarão acessíveis.
        - Após a replicação estar ativada, não pode adicionar ou remover discos para replicação. Se pretende adicionar ou excluir um disco, tem de desativar a proteção da VM e, em seguida, reativá-la.
        - Não é possível fazer a reativação pós-falha nos discos que forem criados manualmente no Azure. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois diretamente na VM do Azure, apenas três discos em que foi realizada a ativação pós falha realizarão a reativação pós-falha do Azure para o Hyper-V. Não pode incluir discos criados manualmente na reativação pós-falha ou em replicação inversa do Hyper-V para o Azure.
        - Se excluir um disco necessário para o funcionamento de uma aplicação, após a ativação pós-falha para o Azure, tem de criá-lo manualmente no Azure, para que a aplicação replicada possa ser executada. Em alternativa, pode integrar a automatização do Azure num plano de recuperação e criar o disco durante a ativação pós-falha da máquina.

    Clique em **OK** para guardar as alterações. Pode definir as propriedades adicionais mais tarde.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

8. Em **Definições de replicação** > **Configurar as definições de replicação**, selecione a política de replicação que pretende aplicar para as máquinas virtuais protegidas. Em seguida, clique em **OK**. Pode modificar a política de replicação em **Políticas de replicação** > nome da política > **Editar Definições**. As alterações que aplicar são utilizadas para as máquinas que já estão a replicar e para as novas máquinas.

   ![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Pode controlar o progresso da tarefa **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

### <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades da VM

Recomendamos que verifique as propriedades da máquina de origem. Lembre-se de que o nome da VM do Azure deve estar em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Em **Itens Protegidos**, clique em **tens Replicados** e selecione a máquina para ver os respetivos detalhes.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. Em **Propriedades**, pode ver as informações de replicação e de ativação pós-falha da VM.

    ![Ativar a replicação](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. Em **Computação e Rede** > **Propriedades de computação**, pode especificar o nome e o tamanho do destino da VM do Azure. Altere o nome para estar em conformidade com os [Requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements), se necessário. Também pode ver e modificar as informações sobre a rede de destino, a sub-rede e o endereço IP atribuído à VM do Azure.
Tenha em atenção que:

   * Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação falhará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.
   * O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

     * Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
     * Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
     * Por exemplo, se uma máquina de origem tem duas placas de rede e o tamanho da máquina de destino suporta quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.     
     * Se a VM tiver vários adaptadores de rede, todos serão ligados à mesma rede.

     ![Ativar a replicação](./media/site-recovery-vmm-to-azure/test-failover4.png)

4. Em **Discos**, pode ver o sistema operativo e os discos de dados na VM que será replicada.

#### <a name="managed-disks"></a>Managed disks

Em **Computação e Rede** > **Propriedades de computação**, pode configurar a definição “Utilizar discos geridos” como “Sim” na VM, se quiser anexar discos geridos à máquina que está a ser migrada para o Azure. Os discos geridos simplificam a gestão de discos para VMs IaaS do Azure, ao gerirem as contas de armazenamento associadas aos discos das VMs. [Saiba mais sobre os discos geridos](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - Os discos geridos são criados e anexados à máquina virtual apenas numa ativação pós-falha para o Azure. Ao ativar a proteção, os dados das máquinas no local continuam a ser replicados para as contas de armazenamento.
   Os discos geridos só podem ser criados para máquinas virtuais implementadas com o modelo de implementação Resource Manager.  

  > [!NOTE]
  > Atualmente, a ativação pós-falha do Azure para o ambiente de Hyper-V no local não é suportada em máquinas com discos geridos. Defina "Utilizar discos geridos” como "Sim" apenas se quiser migrar esta máquina para o Azure.

   - Ao definir “Utilizar discos geridos” como “Sim”, só os conjuntos de disponibilidade no grupo de recursos que tenham “Utilizar discos geridos” definidos como “Sim” estarão disponíveis para seleção. Isto deve-se ao facto de as máquinas virtuais com discos geridos só poderem fazer parte de conjuntos de disponibilidade que tenham a propriedade “Utilizar discos geridos” definida como “Sim”. Confirme que cria conjuntos de disponibilidade com a propriedade “Utilizar discos geridos” definida de acordo com a sua intenção de utilizar discos geridos na ativação pós-falha.  Do mesmo modo, ao definir “Utilizar discos geridos” como “Não”, só os conjuntos de disponibilidade no grupo de recursos que tenham a propriedade “Utilizar discos geridos” definida como “Não” estarão disponíveis para seleção. [Saiba mais sobre os discos geridos e os conjuntos de disponibilidade](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > Se a conta de armazenamento utilizada para a replicação tiver sido encriptada com a Encriptação do Serviço de Armazenamento em algum momento, a criação de discos geridos durante a ativação pós-falha vai falhar. Pode definir “Utilizar discos geridos” como “Não” e repetir a ativação pós-falha ou desativar a proteção da máquina virtual e protegê-la numa conta de armazenamento que não tenha tido a Encriptação do Serviço de Armazenamento ativada em nenhum momento.
  > [Saiba mais sobre a Encriptação do Serviço de Armazenamento e os discos geridos](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>Testar a implementação

Para testar a implementação pode executar uma ativação pós-falha de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.

### <a name="before-you-start"></a>Antes de começar

 - Se pretender ligar-se a VMs do Azure com RDP após a ativação pós-falha, saiba mais sobre como [preparar a ligação](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para testar completamente,tem de copiar o Active Directory e o DNS no seu ambiente de teste. [Saiba mais](site-recovery-active-directory.md#test-failover-considerations).

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. Para fazer a ativação pós-falha numa única VM, em **Itens Replicados**, clique na VM > **+ Ativação Pós-Falha de Teste**.
2. Para efetuar a ativação pós-falha de um plano de recuperação, em **Planos de Recuperação**, clique com o botão direito do rato no plano > **Ativação Pós-Falha de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).
3. Em **Ativação Pós-Falha de Teste**, selecione a rede do Azure à qual as VMs do Azure se vão ligar depois de feita a ativação pós-falha.
4. Clique em **OK** para iniciar a ativação pós-falha. Pode controlar o progresso clicando na VM para abrir as respetivas propriedades ou na tarefa **Ativação Pós-Falha de Teste** em **Tarefas do Site Recovery**.
5. Depois de concluída a ativação pós-falha, deverá também conseguir ver a máquina do Azure de réplica no Portal do Azure > **Máquinas Virtuais**. Confirme que a VM tem um tamanho adequado, que está ligada à rede certa e que está em execução.
6. Se tiver preparado para as ligações após a ativação pós-falha, deverá ser capaz de ligar à VM do Azure.
7. Quando tiver terminado, clique em **Ativação pós-falha de teste de limpeza** no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste. Este procedimento eliminará as máquinas virtuais criadas durante a ativação pós-falha de teste.

Para obter mais detalhes, veja o artigo [Test failover to Azure](site-recovery-test-failover-to-azure.md) (Testar a ativação pós-falha para o Azure).

## <a name="monitor-the-deployment"></a>Monitorizar a implementação

Veja como pode monitorizar as definições de configuração, o estado e o estado de funcionamento da implementação do Site Recovery:

1. Clique no nome do cofre para aceder ao dashboard **Essentials**. Neste dashboard, pode ver as tarefas de Recuperação de Sites, o estado de replicação, os planos de recuperação, o estado de funcionamento do servidor e os eventos.  Pode personalizar **Essentials** para mostrar os mosaicos e os esquemas que são mais úteis para si, incluindo o estado de outros cofres do Site Recovery e do Backup.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)
2. Em **Estado de Funcionamento**, pode monitorizar problemas nos servidores do site (servidores VMM ou de configuração) e os eventos gerados pelo Site Recovery nas últimas 24 horas.
3. Nos mosaicos **Itens Replicados**, **Planos de Recuperação** e **Tarefas do Site Recovery**, pode gerir e monitorizar a replicação. Pode ver os pormenores das tarefas em **Tarefas** > **Tarefas do Site Recovery**.

## <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Instalação de linha de comandos para o Azure Site Recovery Provider

O Azure Site Recovery Provider pode ser instalado a partir da linha de comandos. Este método pode ser utilizado para instalar o Fornecedor num Server Core do Windows Server 2012 R2.

1. Transfira o ficheiro de instalação do Fornecedor e a chave de registo para uma pasta. Por exemplo: C:\ASR.
2. Numa linha de comandos elevada, execute estes comandos para extrair o instalador de Fornecedor com estes comandos:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i
4. Em seguida, execute estes comandos para registar o servidor no cofre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Em que:

* **/Credentials**: é o parâmetro obrigatório que especifica onde se encontra o ficheiro da chave de registo.  
* **/ FriendlyName**: é o parâmetro obrigatório do nome do servidor anfitrião Hyper-V que é apresentado no portal do Azure Site Recovery.
* * **/ /Encryptionenabled**: é o parâmetro opcional quando está a replicar VMs Hyper-V em nuvens VMM para o Azure. Especifique se pretende encriptar máquinas virtuais no Azure (encriptação de inativos). Certifique-se de que o nome do ficheiro tem uma extensão **.pfx**. A encriptação está desativada por predefinição.

    > [!NOTE]
    > Recomenda-se que utilize a capacidade de encriptação que o Azure fornece para encriptar os dados inativos, em vez de utilizar a opção de encriptação de dados (opção EncryptionEnabled) disponibilizada pelo Azure Site Recovery. A capacidade de encriptação fornecida pelo Azure pode ser ativada numa conta de armazenamento e ajuda a obter um desempenho melhor, uma vez que é o armazenamento do Azure que trata da encriptação/  
    > desencriptação.
    > [Saiba mais sobre a encriptação do Serviço de armazenamento no Azure](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).
    
* **/proxyAddress**: é o parâmetro opcional que especifica o endereço do servidor proxy.
* **/proxyport**: é o parâmetro opcional que especifica a porta do servidor proxy.
* **/proxyUsername**: é o parâmetro opcional que especifica o nome de utilizador do proxy (caso o proxy necessite de autenticação).
* **/proxyPassword**: é o parâmetro opcional que especifica a palavra-passe para se autenticar com o servidor proxy (caso o proxy necessite de autenticação).


### <a name="network-bandwidth-considerations"></a>Considerações sobre a largura de banda de rede
Pode utilizar a ferramenta Capacity Planner para calcular a largura de banda que precisa para a replicação (replicação inicial e posteriormente delta). Para controlar a utilização da largura de banda da replicação, tem algumas opções:

* **Limitar largura de banda**: o tráfego do Hyper-V que é replicado para um site secundário passa através de um anfitrião Hyper-V específico. Pode limitar a largura de banda no servidor de anfitrião.
* **Otimizar a largura de banda**: pode influenciar a largura de banda utilizada na replicação através de um par de chaves de registo.

#### <a name="throttle-bandwidth"></a>Limitar largura de banda
1. Abra o snap-in MMC da Cópia de Segurança do Microsoft Azure no servidor de anfitrião Hyper-V. Por predefinição, está disponível um atalho para o Microsoft Azure Backup no ambiente de trabalho ou em C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. No separador **Limitação**, selecione **Ativar a limitação de utilização da largura de banda de Internet para operações de cópia de segurança** e defina os limites das horas de trabalho e de descanso. Os intervalos válidos variam entre 512 Kbps e 102 Mbps por segundo.

    ![Limitar largura de banda](./media/site-recovery-vmm-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que não é necessária nenhuma limitação.

#### <a name="influence-network-bandwidth"></a>Influência da largura de banda de rede
O valor de registo **UploadThreadsPerVM** controla o número de processos utilizados na transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede utilizada na replicação. O valor de registo **DownloadThreadsPerVM** especifica o número de processos utilizados para a transferência de dados durante a reativação pós-falha.

1. No registo, navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

   * Modifique o valor **UploadThreadsPerVM** (ou crie a chave, se não existir) para processos de controlo utilizados para replicação de discos.
   * Modifique o valor **DownloadThreadsPerVM** (ou crie a chave, se não existir) para processos de controlo utilizados para reativação pós-falha desde o Azure.
2. O valor predefinido é 4. Numa rede “sobreaprovisionada”, os valores predefinidos destas chaves de registo devem ser alterados. O valor máximo é 32. Monitorize o tráfego para otimizar o valor.

## <a name="next-steps"></a>Passos seguintes

Quando a replicação inicial estiver concluída e tiver testado a implementação, pode invocar ativações pós-falha sempre que for necessário. [Saiba mais](site-recovery-failover.md) sobre os diferentes tipos de ativações pós-falha e como executá-las.
