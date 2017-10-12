---
title: "Replicar máquinas virtuais Hyper-V em nuvens VMM para o Azure | Microsoft Docs"
description: "Este artigo descreve como replicar máquinas virtuais Hyper-V em anfitriões Hyper-V localizados em nuvens VMM do System Center para o Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9d526a1f-0d8e-46ec-83eb-7ea762271db5
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/23/2017
ms.author: raynew
ms.openlocfilehash: ac0931a71a2814723380256fc5326fc431c82f2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Replicar máquinas virtuais Hyper-V em nuvens VMM para o Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Portal clássico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Clássica](site-recovery-deploy-with-powershell.md)
>
>

O serviço do Azure Site Recovery contribui para a estratégia da continuidade do seu negócio e recuperação após desastre (BCDR) através da orquestração de replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas para o Azure ou para um datacenter secundário no local. Para uma descrição geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Descrição geral
Este artigo descreve como implementar a Recuperação de Sites para replicar máquinas virtuais Hyper-V em servidores anfitrião Hyper-V que se encontram em nuvens privadas do VMM para o Azure.

O artigo inclui os pré-requisitos para o cenário e mostra-lhe como configurar um cofre de Recuperação de Sites, obter o Fornecedor do Azure Site Recovery instalado no servidor VMM de origem, registar o servidor no cofre, adicionar uma conta de armazenamento do Azure, instalar o agente do Azure Recovery Services nos servidores de anfitrião Hyper-V, configurar definições de proteção de nuvens do VMM que serão aplicadas a todas as máquinas virtuais protegidas e, desse modo, ativar a proteção para todas essas máquinas virtuais. Termine testando a ativação pós-falha para certificar-se de que tudo está a funcionar conforme esperado.

Publique comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitetura
![Arquitetura](./media/site-recovery-vmm-to-azure-classic/topology.png)

* O Fornecedor do Azure Site Recovery está instalado no VMM durante a implementação da Recuperação de Sites e o servidor VMM está registado no cofre de Recuperação de Sites. O Fornecedor comunica com a Recuperação de Sites para gerir a orquestração da replicação.
* O agente do Azure Recovery Services está instalado nos servidores anfitrião Hyper-V durante a implementação da Recuperação de Sites. Processa a replicação de dados para o armazenamento do Azure.

## <a name="azure-prerequisites"></a>Pré-requisitos do Azure
Eis o que necessita no Azure.

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Conta do Azure** |Precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Sites. |
| **Armazenamento do Azure** |Precisará de uma conta de armazenamento do Azure para armazenar dados replicados. Os dados replicados são guardados no armazenamento do Azure e as VMs do Azure são aceleradas quando ocorre a ativação pós-falha. <br/><br/>Precisará de uma [conta de armazenamento georredundante padrão](../storage/common/storage-redundancy.md#geo-redundant-storage). A conta tem de estar na mesma região que o serviço Site Recovery e estar associado à mesma subscrição. Tenha em atenção que a replicação para contas de armazenamento Premium não é atualmente suportada e não deve ser utilizada.<br/><br/>[Leia sobre](../storage/common/storage-introduction.md) o armazenamento do Azure. |
| **Rede do Azure** |Precisará de uma rede virtual do Azure à qual se ligarão as VMs do Azure quando ocorrer a ativação pós-falha. A rede virtual do Azure tem de estar na mesma região que o cofre de Recuperação de Sites. |

## <a name="on-premises-prerequisites"></a>Pré-requisitos no local
Eis o que precisa no ambiente no local.

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **VMM** |Precisará de, pelo menos, um servidor VMM implementado como um servidor autónomo físico ou virtual ou como um cluster virtual. <br/><br/>O servidor VMM deve executar o System Center 2012 R2 com as atualizações acumulativas mais recentes.<br/><br/>Precisará de, pelo menos, uma nuvem configurada no servidor VMM.<br/><br/>A nuvem de origem que pretende proteger tem de conter um ou mais grupos de anfitriões VMM.<br/><br/>Saiba mais sobre como configurar as nuvens do VMM em [Instruções: criar nuvens privadas com o VMM do System Center 2012 SP1](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blogue de Keith Mayer. |
| **Hyper-V** |Precisará de um ou mais servidores anfitrião Hyper-V ou clusters na nuvem VMM. O servidor de anfitrião tem de ter uma ou várias VMs. <br/><br/>O servidor Hyper-V tem de ser executado, pelo menos, no **Windows Server 2012 R2** com a função Hyper-V ou no **Microsoft Hyper-V Server 2012 R2** com as atualizações mais recentes instaladas.<br/><br/>Qualquer servidor Hyper-V que contenha VMs que pretende proteger tem de estar localizado numa nuvem VMM.<br/><br/>Se estiver a executar Hyper-V num cluster, tenha em atenção que o mediador de clusters não é criado automaticamente se tiver um cluster com base no endereço IP estático. Precisará de configurar manualmente o mediador de clusters. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) na entrada de blogue de Aidan Finn. |
| **Máquinas protegidas** | As VMs que pretende proteger devem estar em conformidade com os [Requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). |

## <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento da rede
Quando protege máquinas virtuais no mapeamento da rede do Azure, mapeia entre as redes VM no servidor VMM de origem e as redes do Azure de destino para ativar o seguinte:

* Todas as máquinas que fazem a ativação pós-falha na mesma rede podem ligar-se entre si, independentemente do plano de recuperação onde se encontram.
* Se um gateway de rede está configurado na rede de destino do Azure, as máquinas virtuais podem ligar-se a outras máquinas virtuais no local.
* Se não configurar o mapeamento da rede, apenas as máquinas virtuais com ativação pós-falha no plano de recuperação conseguirão ligar-se entre si após a ativação pós-falha para o Azure.

Se pretender implementar o mapeamento da rede precisará do seguinte:

* As máquinas virtuais que pretende proteger no servidor VMM de origem devem estar ligadas a uma rede VM. Essa rede deve ser ligada a uma rede lógica que está associada à nuvem.
* Uma rede do Azure para a qual as máquinas virtuais replicadas podem ligar após a ativação pós-falha. Selecionará esta rede no momento da ativação pós-falha. A rede deve estar na mesma região que a sua subscrição do Azure Site Recovery.


Preparar redes no VMM:

   * [Configure as redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
   * [Configure as redes VM](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Passo 1: Criar um cofre de Recuperação de Sites
1. Inicie sessão no [Portal de Gestão](https://portal.azure.com) do servidor VMM que pretende registar.
2. Clique em **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Recuperação de Sites**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, introduza um nome amigável para identificar o cofre.
5. Em **Localização**, selecione a região geográfica do cofre. Para verificar as regiões suportadas, veja Disponibilidade Geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **Criar cofre**.

    ![Novo Cofre](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Verifique a barra de estado para confirmar se o cofre foi criado com êxito. O cofre será listado como **Ativo** na página principal dos Serviços de Recuperação.

## <a name="step-2-generate-a-vault-registration-key"></a>Passo 2: Gerar uma chave de registo do cofre
Gere uma chave de registo no cofre. Depois de transferir o Fornecedor do Azure Site Recovery e instalá-lo no servidor VMM, utilizará esta chave para registar o servidor VMM no cofre.

1. Na página **Serviços de Recuperação**, clique no cofre para abrir a página Início Rápido. O Início Rápido também pode ser aberto em qualquer altura utilizando o ícone.

    ![Ícone de Início Rápido](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)
2. Na lista pendente, selecione **Entre um site do VMM no local e o Microsoft Azure**.
3. Em **Preparar Servidores VMM**, clique em **Gerar ficheiro de chave de registo**. O ficheiro da chave é gerado automaticamente e é válido durante 5 dias após ser gerado. Se não estiver a aceder ao portal do Azure a partir do servidor VMM, precisará de copiar este ficheiro para o servidor.

    ![Chave de registo](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Passo 3: Instalar o Fornecedor do Azure Site Recovery
1. Em **Início Rápido** > **Preparar servidores VMM**, clique em **Transferir Fornecedor do Microsoft Azure Site Recovery para instalar nos servidores VMM** para obter a versão mais recente do ficheiro de instalação do Fornecedor.
2. Execute este ficheiro no servidor VMM de origem.

   > [!NOTE]
   > Se o VMM for implementado num cluster e estiver a instalar o Fornecedor pela primeira vez, instale-o num nó ativo e conclua a instalação para registar o servidor VMM no cofre. Em seguida, instale o Fornecedor nos outros nós. Tenha em atenção que, se estiver a atualizar o Fornecedor terá de atualizar todos os nós, uma vez que todos devem executar a mesma versão do Fornecedor.
   >
   >
3. O Instalador faz uma verificação dos pré-requisitos e pede permissão para parar o serviço VMM para iniciar a configuração do Fornecedor. O serviço VMM será reiniciado automaticamente após a conclusão da configuração. Se estiver a instalar num VMM cluster, será solicitado para parar a função de Cluster.
4. Em **Microsoft Update** pode optar por atualizações. Com esta definição ativada, as atualizações do Fornecedor serão instaladas de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)
5. A localização de instalação do Fornecedor está definida como **<SystemDrive>\Programas\Microsoft System Center 2012 R2\Virtual Machine Manager\bin**. Clique em **Instalar**.

   ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)
6. Depois de instalado o Fornecedor, clique em **Registar** para registar o servidor no cofre.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)
7. Em **Nome do cofre**, verifique o nome do cofre no qual o servidor será registado. Clique em *Seguinte*.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)
8. Em **Ligação à Internet**, especifique a forma como o Fornecedor em execução no servidor VMM estabelece ligação à Internet. Selecione **Ligar com as definições de proxy existentes** para utilizar as definições predefinidas de ligação à Internet configuradas no servidor.

    ![Definições da Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

   * Se pretender utilizar um proxy personalizado, deve configurá-lo antes de instalar o Fornecedor. Quando configurar as definições de proxy personalizado, será executado um teste para verificar a ligação proxy.
   * Se utilizar um proxy personalizado ou se o proxy predefinido exigir autenticação, precisará de introduzir os detalhes do proxy, incluindo o endereço de proxy e a porta.
   * Os seguintes URLs devem ser acessíveis a partir do servidor VMM e dos anfitriões Hyper-V
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * Permita os endereços IP descritos em [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443). Também deverá incluir numa lista de permissões os intervalos de IP da região do Azure que pretende utilizar e dos E.U.A. Oeste.
   * Se utilizar um proxy personalizado será automaticamente criada uma conta RunAs do VMM (DRAProxyAccount) utilizando as credenciais de proxy especificado. Configure o servidor proxy para que esta conta possa autenticar-se com êxito. As definições de conta RunAs do VMM podem ser modificadas na consola do VMM. Para tal, abra a área de trabalho **Definições**, expanda **Segurança**, clique em **Contas Run As** e, em seguida, modifique a palavra-passe para DRAProxyAccount. Terá de reiniciar o serviço VMM para que esta definição surta efeito.
9. Em **Chave de Registo**, selecione a chave que transferiu a partir do Azure Site Recovery e copiou para o servidor VMM.
10. A definição da encriptação é apenas utilizada quando replicar VMs Hyper-V em nuvens VMM para o Azure. Não será utilizada se estiver a replicar para um site secundário.
11. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Numa configuração de cluster, especifique o nome da função de cluster do VMM.
12. Em **Sincronizar metadados da nuvem**, selecione se pretende sincronizar ou não os metadados de todas as nuvens no servidor VMM com o cofre. Esta ação só deverá ocorrer uma vez em cada servidor. Se não quiser sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades de nuvem na consola do VMM.
13. Clique em **Seguinte** para concluir o processo. Após o registo, os metadados do servidor VMM são obtidos pelo Azure Site Recovery. O servidor é apresentado no separador **Servidores VMM**, na página **Servidores** do cofre.

    ![Última página](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Após o registo, os metadados do servidor VMM são obtidos pelo Azure Site Recovery. O servidor é apresentado no separador **Servidores VMM**, na página **Servidores** do cofre.

### <a name="command-line-installation"></a>Instalação com linha de comandos
O Fornecedor do Azure Site Recovery também pode ser instalado utilizando a seguinte linha de comandos. Este método pode ser utilizado para instalar o Fornecedor num Server Core do Windows Server 2012 R2.

1. Transfira o ficheiro de instalação do Fornecedor e a chave de registo para uma pasta. Por exemplo: C:\ASR.
2. Pare o serviço do System Center Virtual Machine Manager
3. Numa linha de comandos elevada, extraia o instalador de Fornecedor com estes comandos:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Instale o fornecedor da seguinte forma:

        C:\ASR> setupdr.exe /i
5. Registe o Fornecedor da seguinte forma:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Onde os parâmetros são os seguintes:

* **/Credentials**: é o parâmetro obrigatório que especifica a localização em que se encontra o ficheiro de chave de registo  
* **/ FriendlyName**: é o parâmetro obrigatório para o nome do servidor de anfitrião Hyper-V que é apresentado no portal do Azure Site Recovery.
* **/ EncryptionEnabled**: é o parâmetro opcional para especificar se pretende encriptar as máquinas virtuais no Azure (na encriptação de inativos). O nome do ficheiro deve ter uma extensão **.pfx**.
* **/proxyAddress**: é o parâmetro opcional que especifica o endereço do servidor proxy.
* **/proxyAddress**: é o parâmetro opcional que especifica a porta do servidor proxy.
* **/proxyUsername**: é o parâmetro opcional que especifica o nome de utilizador de proxy.
* **/proxyPassword**: é o parâmetro opcional que especifica a palavra-passe do proxy.  

## <a name="step-4-create-an-azure-storage-account"></a>Passo 4: Criar uma conta de armazenamento do Azure
1. Se não tiver uma conta de armazenamento do Azure, clique em **Adicionar uma Conta de Armazenamento do Azure** para criar uma.
2. Crie uma conta com georreplicação ativada. A conta tem de estar na mesma região que o serviço do Azure Site Recovery e estar associado à mesma subscrição.

    ![Conta de armazenamento](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [!NOTE]
> A [migração de contas de armazenamento](../azure-resource-manager/resource-group-move-resources.md) através de grupos de recursos na mesma subscrição ou em várias subscrições não é suportada em contas de armazenamento utilizadas para implementar o Site Recovery.
>
>

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Passo 5: Instalar o Agente dos Serviços de Recuperação do Azure
Instale o Agente do Serviços de Recuperação do Azure em cada servidor de anfitrião Hyper-V na nuvem VMM.

1. Clique em **Início Rápido** > **Transferir o Agente do Azure Site Recovery Services e instalar em anfitriões** para obter a versão mais recente do ficheiro de instalação do agente.

    ![Instalar o Agente dos Serviços de Recuperação](./media/site-recovery-vmm-to-azure-classic/install-agent.png)
2. Execute o ficheiro de instalação em cada servidor de anfitrião Hyper-V.
3. Na página **Verificação de Pré-requisitos**, clique em **Seguinte**. Os pré-requisitos em falta serão instalados automaticamente.

    ![Pré-requisitos do Agente dos Serviços de Recuperação](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)
4. Na página **Definições de Instalação**, especifique onde pretende instalar o agente e selecione a localização da cache na qual os metadados de cópia de segurança serão instalados. Em seguida, clique em **Instalar**.
5. Depois de concluída a instalação, clique em **Fechar** para concluir o assistente.

    ![Registar o Agente MARS](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Instalação com linha de comandos
Também pode instalar o Agente dos Serviços de Recuperação do Microsoft Azure a partir da linha de comandos utilizando este comando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Passo 6: Configurar definições de proteção na nuvem
Depois do servidor VMM estar registado, pode configurar as definições de proteção na nuvem. Ativou a opção **Sincronizar dados de nuvem com o cofre** quando instalou o Fornecedor, por isso, todas as nuvens no servidor VMM aparecerão no separador <b>Itens Protegidos</b> no cofre.

![Nuvem Publicada](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Na página Início Rápido, clique em **Configurar a proteção de nuvens VMM**.
2. No separador **Itens Protegidos**, clique na nuvem que pretende configurar e vá para o separador **Configuração**.
3. Em **Destino**, selecione **Azure**.
4. Em **Conta de Armazenamento**, selecione a conta de armazenamento do Azure que utiliza para replicação.
5. Defina **Encriptar dados armazenados** como **Desativar**. Esta definição especifica que os dados devem ser encriptados durante a replicação entre o site no local e o Azure.
6. Em **Copiar frequência** deixe o valor predefinido. Este valor especifica a frequência com que devem ser sincronizadas as localizações de origem e de destino.
7. Em **Manter pontos de recuperação para**, deixe o valor predefinido. Com um valor predefinido de zero, o último ponto de recuperação para uma máquina virtual principal é o único que é armazenado num servidor de anfitrião de réplica.
8. Em **Frequência de instantâneos consistentes com aplicações**, deixe o valor predefinido. Este valor especifica a frequência de criar instantâneos. Os instantâneos utilizam o Serviço de Cópia de Sombra de Volumes (VSS) para se certificar de que as aplicações estão num estado consistente quando se obtém o instantâneo.  Se definir um valor, certifique-se de que é menor que o número de pontos de recuperação adicionais que está a configurar.
9. Em **Hora de início da replicação**, especifique quando deve começar a replicação inicial dos dados no Azure. Será utilizado o fuso horário do servidor de anfitrião Hyper-V. Recomendamos que agende a replicação inicial fora das horas de ponta.

    ![Definições de replicação de nuvem](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Depois de guardar as definições, será criada uma tarefa que pode ser monitorizada no separador **Tarefas**. Todos os servidores anfitrião Hyper-V na nuvem de origem do VMM serão configurados para replicação.

Depois de guardar, podem ser modificadas de definições de nuvem no separador **Configurar**. Para modificar a localização de destino ou a conta de armazenamento de destino, terá de remover a configuração da nuvem e, em seguida, reconfigurar a nuvem. Tenha em atenção que se alterar a conta de armazenamento, a alteração só se aplica às máquinas virtuais que estão ativadas para proteção após a modificação da conta de armazenamento. As máquinas virtuais existentes não são migradas para a nova conta de armazenamento.

## <a name="step-7-configure-network-mapping"></a>Passo 7: Configurar o mapeamento da rede
Antes de iniciar o mapeamento da rede, certifique-se que as máquinas virtuais no servidor VMM de origem estão ligadas a uma rede VM. Além disso, crie uma ou mais redes virtuais Azure. Tenha em atenção que várias redes VM podem ser mapeadas para uma única rede Azure.

1. Na página Início Rápido, clique em **Mapear redes**.
2. No separador **Redes**, em **Localização de origem**, selecione o servidor VMM de origem. Em **Localização de destino**, selecione Azure.
3. Em redes de **Origem**, é apresentada uma lista de redes VM associadas ao servidor VMM. Em redes de **Destino**, é apresentada uma lista de redes Azure associadas à subscrição.
4. Selecione a rede VM de origem e clique em **Mapear**.
5. Na página **Selecionar uma Rede de Destino**, selecione a rede de destino do Azure que pretende utilizar.
6. Clique na marca de verificação para concluir o processo de mapeamento.

    ![Definições de replicação de nuvem](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Depois de guardar as definições, é iniciada uma tarefa para controlar o progresso do mapeamento, que pode ser monitorizado no separador Tarefas. Todas as máquinas virtuais de réplica existentes que correspondam à rede VM de origem serão ligadas às redes de destino do Azure. As novas máquinas virtuais que estão ligadas à rede VM de origem aparecerão ligadas à rede do Azure mapeada após a replicação. Se modificar um mapeamento existente com uma nova rede, as máquinas virtuais de réplica serão ligadas utilizando as novas definições.

Tenha em atenção que se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede onde está localizada a máquina virtual de origem, a máquina virtual de réplica será ligada a essa sub-rede de destino após a ativação pós-falha. Se não existir nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será ligada à primeira sub-rede da rede.

> [!NOTE]
> A [migração de redes](../azure-resource-manager/resource-group-move-resources.md) através de grupos de recursos na mesma subscrição ou em várias subscrições não é suportada em redes utilizadas para implementar o Site Recovery.
>
>

## <a name="step-8-enable-protection-for-virtual-machines"></a>Passo 8: Ativar a proteção para máquinas virtuais
Depois de configurar corretamente os servidores, as nuvens e as redes, pode ativar a proteção para máquinas virtuais na nuvem. Tenha em atenção o seguinte:

* As máquinas virtuais têm de cumprir os [Requisitos Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
* Para ativar a proteção, o sistema operativo e as propriedades de disco do sistema operativo têm de ser definidos para a máquina virtual. Quando cria uma máquina virtual no VMM utilizando um modelo de máquina virtual, pode definir a propriedade. Também pode definir estas propriedades para as máquinas virtuais existentes nos separadores **Geral** e **Configuração de Hardware** das propriedades da máquina virtual. Se não definir estas propriedades no VMM, poderá configurá-las no portal do Azure Site Recovery.

    ![Criar a máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modificar as propriedades da máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)

1. Para ativar a proteção, no separador **Virtual Machines** na nuvem em que a máquina virtual se encontra, clique em **Ativar proteção** > **Adicionar máquinas virtuais**.
2. Na lista de máquinas virtuais na nuvem, selecione aquela que pretende proteger.

    ![Ativar a proteção da máquina virtual](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Controle o progresso da ação **Ativar proteção**, no separador **Tarefas**, incluindo a replicação inicial. Depois da tarefa **Finalizar Proteção** ser executada, a máquina virtual está preparada para ativação pós-falha. Depois da proteção estar ativada e as máquinas virtuais serem replicadas, poderá visualizá-las no Azure.

    ![Tarefa de proteção da máquina virtual](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

1. Verifique as propriedades da máquina virtual e modifique conforme necessário.

    ![Verificar as máquinas virtuais](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)
2. No separador**Configurar** das propriedades da máquina virtual, pode modificar as seguintes propriedades de rede.

* **Número de adaptadores de rede na máquina virtual de destino** – o número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino. Verifique as [especificações de tamanho das máquinas virtuais](../virtual-machines/linux/sizes.md) para obter o número de adaptadores suportados pelo tamanho da máquina virtual. Quando modificar o tamanho de uma máquina virtual e guardar as definições, o número dos adaptadores de rede será alterado quando abrir a página **Configurar** na próxima vez. O número de adaptadores de rede de máquinas virtuais de destino é o número mínimo de placas de rede na máquina virtual de origem e o número máximo de adaptadores de rede suportados pelo tamanho da máquina virtual escolhido, da seguinte forma:

  * Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
  * Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
  * Por exemplo, se uma máquina de origem tiver duas placas de rede e o tamanho da máquina de destino suportar quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.     
* **Rede da máquina virtual destino** – a rede para a qual a máquina virtual estabelece ligação é determinada pelo mapeamento da rede da máquina virtual de origem. Se a máquina virtual de origem tiver mais do que uma placa de rede e as redes de origem estiverem mapeadas para diferentes redes no destino, terá de escolher entre uma das redes de destino.
* **Sub-rede de cada placa de rede** – para cada placa de rede, pode selecionar a sub-rede para a qual a máquina virtual ligará se ocorrer alguma falha na ativação pós-falha.
* **Endereço IP de destino** – se a placa de rede da máquina virtual de origem estiver configurada para utilizar um endereço IP estático, pode fornecer o endereço IP para a máquina virtual de destino. Utilize esta funcionalidade para manter o endereço IP de uma máquina virtual de origem após uma ativação pós-falha. Se não for fornecido nenhum endereço IP, qualquer endereço IP disponível é atribuído à placa de rede no momento da ativação pós-falha. Se for especificado o endereço IP de destino, mas já estiver a ser utilizado por outra máquina virtual em execução no Azure, falhará a ativação pós-falha.  

    ![Modificar as propriedades da rede](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

> [!NOTE]
> As máquinas virtuais Linux com endereço IP estático não são suportadas.
>
>

## <a name="test-the-deployment"></a>Testar a implementação
Para testar a implementação, pode executar uma ativação pós-falha de teste para uma única máquina virtual ou criar um plano de recuperação que consiste em várias máquinas virtuais e executar para o plano uma ativação pós-falha de teste.  

A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada. Tenha em atenção que:

* Se pretender ligar à máquina virtual no Azure utilizando o Ambiente de Trabalho Remoto após a ativação pós-falha, deverá ativar a Ligação de Ambiente Remoto na máquina virtual antes de executar a ativação pós-falha de teste.
* Após a ativação pós-falha, utilize um endereço IP público para ligar à VM do Azure com o Ambiente de Trabalho Remoto. Se pretender efetuar este procedimento, certifique-se de que não tem quaisquer políticas de domínio a impedir a ligação a uma máquina virtual utilizando um endereço público.

> [!NOTE]
> Para obter o melhor desempenho quando houver uma ativação pós-falha para o Azure, certifique-se de que instalou o agente do Azure na VM. Isto fornece um arranque mais rápido e ajuda na resolução de problemas. Transfira o [agente do Linux](https://github.com/Azure/WALinuxAgent) ou o [agente do Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
1. No separador **Planos de Recuperação**, adicione um novo plano. Especifique um nome, **VMM** no **Tipo de origem** e o servidor VMM de origem em **Origem**. O destino será o Azure.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)
2. Na página **Selecionar Virtual Machines**, selecione as máquinas virtuais a adicionar ao plano de recuperação. Estas máquinas virtuais são adicionadas ao grupo predefinido do plano de recuperação – Grupo 1. Foi testado um máximo de 100 máquinas virtuais num plano de recuperação simples.

* Se pretender verificar as propriedades da máquina virtual antes de adicioná-las ao plano, clique na máquina virtual na página de propriedades da nuvem em que está localizada. Também pode configurar as propriedades da máquina virtual na consola do VMM.
* Todas as máquinas virtuais apresentadas foram ativadas para proteção. A lista inclui ambas as máquinas virtuais que estão ativadas para proteção e onde a replicação inicial foi concluída, assim como as que estão ativadas para proteção com replicação inicial pendente. Apenas as máquinas virtuais com replicação inicial concluída podem realizar a ativação pós-falha como parte de um plano de recuperação.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Depois de ter sido criado um plano de recuperação, este aparece no separador **Planos de Recuperação**. Também pode adicionar [Runbooks de Automatização do Azure](site-recovery-runbook-automation.md) ao plano de recuperação para automatizar ações durante a ativação pós-falha.

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
Existem duas formas de executar uma ativação pós-falha de teste para o Azure.

* **Testar a ativação pós-falha sem uma rede Azure** – este tipo de ativação pós-falha de teste verifica se a máquina virtual é apresentada corretamente no Azure. A máquina virtual não será ligada a nenhuma rede Azure após a ativação pós-falha.
* **Testar a ativação pós-falha com uma rede Azure** – este tipo de ativação pós-falha verifica se o ambiente de replicação completo é mostrado conforme esperado e se as máquinas virtuais com ativação pós-falha serão ligadas à rede de destino do Azure especificada. Para o processamento da sub-rede e para fins de teste da ativação pós-falha, a sub-rede da máquina virtual de teste será determinada com base na sub-rede da máquina virtual de réplica. Isto é diferente da replicação normal em que a sub-rede da máquina virtual de réplica se baseia na sub-rede da máquina virtual de origem.

Se pretender executar uma ativação pós-falha de teste para uma máquina virtual ativada para a proteção do Azure sem especificar uma rede de destino do Azure, não precisa de preparar nada. Para executar uma ativação pós-falha de teste com uma rede de destino do Azure, terá de criar uma nova rede do Azure isolada da sua rede de produção do Azure (comportamento predefinido quando cria uma nova rede no Azure). Veja como [Executar uma ativação pós-falha de teste](site-recovery-failover.md) para obter mais detalhes.

Terá também de configurar a infraestrutura para a máquina virtual replicada funcionar conforme esperado. Por exemplo, uma máquina virtual com o Controlador de Domínio e o DNS pode ser replicada para o Azure utilizando o Azure Site Recovery e pode ser criada na rede de teste com Ativação Pós-falha de Teste. Veja a secção [Considerações sobre a ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.

Para executar um teste de ativação pós-falha, efetue o seguinte:

1. No separador**Planos de Recuperação**, selecione o plano e clique em **Ativação Pós-falha de Teste**.
2. Na página **Confirmar Ativação Pós-falha de Teste**, selecione **Nenhuma** ou uma rede de Azure específica.  Tenha em atenção que se selecionou Nenhuma, a ativação pós-falha de teste verificará se a máquina virtual replicou corretamente para o Azure mas não verifica a configuração da rede de replicação.

    ![Sem rede](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)
3. Se a encriptação de dados estiver ativada para a nuvem, em **Chave de Encriptação** selecione o certificado que foi emitido durante a instalação do Fornecedor no servidor VMM, quando ativar a opção para ativar a encriptação de dados para uma nuvem.
4. No separador **Tarefas**, pode controlar o progresso de ativação pós-falha. Também deve ser capaz de ver a réplica de teste da máquina virtual no portal do Azure. Se tiver configurado para aceder a máquinas virtuais a partir da rede no local, pode iniciar uma ligação de Ambiente de Trabalho Remoto para a máquina virtual.
5. Quando a ativação pós-falha atingir a fase **Teste completo**, clique em **Concluir Teste** para concluir. Pode desagregar o separador **Tarefa** para controlar o estado e o progresso de ativação pós-falha e para realizar qualquer ação necessária.
6. Após a ativação pós-falha, pode ver a réplica de teste da máquina virtual no portal do Azure. Se tiver configurado para aceder a máquinas virtuais a partir da rede no local, pode iniciar uma ligação de Ambiente de Trabalho Remoto para a máquina virtual. Faça o seguinte:

   1. Verifique se as máquinas virtuais iniciam corretamente.
   2. Se pretender ligar à máquina virtual no Azure utilizando o Ambiente de Trabalho Remoto após a ativação pós-falha, deverá ativar a Ligação de Ambiente Remoto na máquina virtual antes de executar a ativação pós-falha de teste. Também tem de adicionar um ponto final RDP na máquina virtual. Pode tirar partido dos [Runbooks de Automatização do Azure](site-recovery-runbook-automation.md) para esse fim.
   3. Depois da ativação pós-falha, se utilizar um endereço IP público para ligar à máquina virtual no Azure utilizando o Ambiente de Trabalho Remoto, certifique-se de que não tem qualquer política de domínio a impedir a ligação a uma máquina virtual utilizando um endereço público.
7. Depois do teste ter concluído, efetue o seguinte:

   * Clique em **A ativação pós-falha de teste está concluída**. Limpe o ambiente de teste para se desligar automaticamente e eliminar as máquinas virtuais de teste.
   * Clique em **Notas** para registar e guardar todas as observações associadas à ativação pós-falha de teste.


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Configurar planos de recuperação](site-recovery-create-recovery-plans.md) e [Ativação pós-falha](site-recovery-failover.md).
