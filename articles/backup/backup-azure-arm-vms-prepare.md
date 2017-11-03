---
title: "Cópia de segurança do Azure: Preparar a cópia de segurança de máquinas virtuais | Microsoft Docs"
description: "Certifique-se de que o seu ambiente está preparado para fazer uma cópia de segurança de máquinas virtuais no Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "cópias de segurança; cópia de segurança;"
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/3/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 7ee2e42e05fb4866d32c24b0d4c788b0197970ad
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Preparar o seu ambiente para fazer cópias de segurança de máquinas virtuais implementadas com o Resource Manager
> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modelo clássico](backup-azure-vms-prepare.md)
>
>

Este artigo fornece os passos para preparar o ambiente para fazer uma cópia de segurança de uma máquina de virtual (VM) implementadas no Resource Manager. Os passos apresentados nos procedimentos utilizam o portal do Azure.  

O serviço de cópia de segurança do Azure tem dois tipos de cofres (cópia de segurança cofres e os cofres dos serviços de recuperação) para proteger as suas VMs. Um cofre de cópia de segurança protege VMs implementadas utilizando o modelo de implementação clássica. Protege um cofre dos serviços de recuperação **VMs ambos implementadas clássico ou implementadas no Resource Manager**. Tem de utilizar um cofre dos serviços de recuperação para proteger uma VM implementadas no Resource Manager.

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Consulte [preparar o ambiente para fazer cópias de segurança de máquinas virtuais do Azure](backup-azure-vms-prepare.md) para obter detalhes sobre como trabalhar com a implementação clássica modelo VMs.
>
>

Antes de poder proteger ou fazer uma cópia de segurança de uma máquina de virtual (VM) implementadas no Resource Manager, certifique-se que estes pré-requisitos existem:

* Criar um cofre dos serviços de recuperação (ou identificar um cofre de serviços de recuperação existente) *na mesma localização que a VM*.
* Selecionar um cenário, definir a política de cópia de segurança e definir os itens para proteger.
* Verifique a instalação do agente da VM na máquina virtual.
* Verifique a conectividade de rede
* Para VMs com Linux, no caso de que pretende personalizar o seu ambiente de cópia de segurança para a aplicação consistente cópias de segurança volte a seguir a [passos para configurar scripts anterior ao instantâneo e posterior ao instantâneo](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

Se conhecer estas condições já existem no seu ambiente, em seguida, avance para o [cópia de segurança do artigo VMs](backup-azure-vms.md). Se precisar de configurar ou verificar, qualquer um destes pré-requisitos, este artigo orienta-o pelos passos para preparar desse pré-requisito.

##<a name="supported-operating-system-for-backup"></a>Sistema operativo suportado para cópia de segurança
 * **Linux**: o Azure Backup suporta [uma lista de distribuições apoiadas pelo Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), exceto Core OS Linux. _Outras Bring-Your-proprietário-as distribuições do Linux também podem funcionar desde que o agente VM está disponível na máquina virtual e suporte para o Python existe. No entanto, não podemos apoia esses distribuições para cópia de segurança._
 * **Windows Server**: não são suportadas as versões mais antigas do que o Windows Server 2008 R2.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitações ao fazer cópias de segurança e restauro de uma VM
Antes de preparar o seu ambiente, volte a compreender as limitações.

* Não é suportada a cópia de segurança de máquinas virtuais com mais de 16 discos de dados.
* Não é suportada a cópia de segurança das máquinas virtuais com dados tamanhos de disco superiores a 1023GB.
* Não é suportada a cópia de segurança de máquinas virtuais com um endereço IP reservado e nenhum ponto final de definidos.
* Cópia de segurança de VMs encriptada utilizando apenas BEK não é suportada. Cópia de segurança de VMs com Linux encriptados utilizando a encriptação de LUKS não é suportada.
* Cópia de segurança de VMs que contém Volumes(CSV) partilhado de Cluster ou escala terminar a configuração do servidor de ficheiros não é recomendada, que necessitam que envolvem todas as VMs incluídas na configuração do cluster durante a tarefa de instantâneo. A consistência multi VM não suporta a cópia de segurança do Azure. 
* Dados de cópia de segurança não incluem unidades de rede montado ligadas à VM.
* A substituição de uma máquina virtual existente durante o restauro não é suportada. Se tentar restaurar a VM quando existe a VM, a operação de restauro irá falhar.
* Não são suportados por várias regiões cópia de segurança e restauro.
* Pode efetuar cópias de segurança de máquinas virtuais em todas as regiões públicas do Azure (consulte o [lista de verificação](https://azure.microsoft.com/regions/#services) de regiões suportadas). Se a região que procura não é suportado atualmente, não serão apresentados na lista pendente durante a criação do cofre.
* Restaurar um controlador de domínio (DC) VM que faz parte de uma configuração de várias DC é suportada apenas através do PowerShell. Leia mais sobre [restaurar um controlador de domínio do DC várias](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Máquinas virtuais que têm as seguintes configurações de rede especiais o restauro é suportado apenas através do PowerShell. VMs criadas utilizando o fluxo de trabalho de restauro na IU não terá estas configurações de rede após a conclusão da operação de restauro. Para obter mais informações, consulte [restaurar VMs com configurações de rede especiais](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Máquinas virtuais em configuração de Balanceador de carga (interna e externa)
  * Máquinas virtuais com vários endereços IP reservados
  * Máquinas virtuais com vários adaptadores de rede

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Criar um cofre dos Serviços de Recuperação para uma VM
Um cofre dos serviços de recuperação é uma entidade que armazena as cópias de segurança e os pontos de recuperação que foram criados ao longo do tempo. O Cofre de serviços de recuperação também contém as políticas de cópia de segurança associadas a máquinas virtuais protegidas.

Para criar um cofre dos serviços de recuperação:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação**. À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Clique no botão Procurar e o tipo dos serviços de recuperação. Quando vir a opção de cofre dos serviços de recuperação, clique para abrir o painel do cofre dos serviços de recuperação.](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    É apresentada a lista de cofres dos serviços de recuperação.
3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 5 da Criação do cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.
5. Clique em **Subscrição** para ver a lista de subscrições disponíveis. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Apenas haverá várias escolhas se a sua conta organizacional estiver associada a várias subscrições do Azure.
6. Clique em **Grupo de recursos** para ver a lista de Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para mais informações mais completas sobre os grupos de Recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Clique em **Localização** para selecionar a região geográfica do cofre. O cofre **tem de** estar na mesma região que as máquinas virtuais que pretende proteger.

   > [!IMPORTANT]
   > Se não tiver a certeza de qual é a localização em que se encontra a VM, feche a caixa de diálogo de criação do cofre e aceda à lista de Máquinas Virtuais no portal. Se tiver máquinas virtuais em várias regiões, terá de criar um cofre dos Serviços de Recuperação em cada região. Crie o cofre na primeira localização antes de ir para a localização seguinte. Não é necessário especificar contas de armazenamento para armazenar os dados de cópia de segurança – o cofre dos Serviços de Recuperação e o serviço de Backup do Azure processam isto automaticamente.
   >
   >

8. Clique em **Criar**. Pode demorar algum tempo até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita no portal. Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação. Se não vir o cofre, clique em **atualizar** para

    ![Lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Agora que criou o cofre, saiba como configurar a replicação de armazenamento.

## <a name="set-storage-replication"></a>Definir Replicação de Armazenamento
A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por predefinição, o seu cofre tem um armazenamento georredundante. Deixe a opção definida para armazenamento georredundante se for a sua cópia de segurança primária. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável.

Para editar a definição de replicação de armazenamento:

1. No **cofres dos serviços de recuperação** painel, selecione o cofre.
    Ao clicar em seu Cofre, o painel de definições (*que tem o nome do cofre na parte superior*) e é aberto o painel de detalhes do cofre.

    ![Escolha o seu Cofre da lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. No **definições** painel, utilize o controlo de deslize vertical para desloque para baixo até o **gerir** secção. Clique em **infraestrutura de cópia de segurança** para abrir o painel. No **geral** secção clique **configuração de cópia de segurança** para abrir o painel. No painel **Configuração de Cópia de Segurança**, escolha a opção de replicação de armazenamento para o cofre. Por predefinição, o seu cofre tem um armazenamento georredundante. Se alterar o tipo de replicação de armazenamento, clique em **guardar**.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/full-blade.png)

     Se estiver a utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o armazenamento georredundante. Se estiver a utilizar o Azure como um ponto final de armazenamento de cópia de segurança não primário, em seguida, escolha armazenamento localmente redundante. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).
    Após escolher a opção de armazenamento para o cofre, está pronto para associar a VM com o cofre. Para começar a associação, detete e registe as máquinas virtuais do Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selecione um objetivo da cópia de segurança, configure a política e defina os itens a proteger
Antes de registar uma VM com um cofre, execute o processo de deteção para se certificar de que as novas máquinas virtuais que foram adicionadas à subscrição são identificadas. O processo consulta o Azure para obter a lista de máquinas virtuais na subscrição, juntamente com informações adicionais, tais como a região e o nome do serviço em nuvem. No portal do Azure, o cenário refere-se ao que vai introduzir no cofre dos Serviços de Recuperação. A política é a agenda da frequência e de quando os pontos de recuperação são obtidos. A política também inclui o período de retenção para os pontos de recuperação.

1. Se já tiver um cofre dos Serviços de Recuperação aberto, avance para o passo 2. Se não tiver um cofre dos serviços de recuperação, abrir, em seguida, abra o [portal do Azure](https://portal.azure.com/) e no Hub menu, clique em **mais serviços**.

   * Na lista de recursos, escreva **Serviços de Recuperação**.
   * À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

     ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

     É apresentada a lista dos cofres dos Serviços de Recuperação. Se não houver nenhuma cofres na sua subscrição, esta lista estará vazia.

    ![Vista da lista dos cofres dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   * Na lista de cofres dos serviços de recuperação, selecione um cofre para abrir o dashboard.

     O painel de definições e o dashboard do cofre para o Cofre escolhido, é aberto.

     ![Abrir painel do cofre](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. No menu do dashboard do cofre, clique em **Cópia de Segurança** para abrir o painel Cópia de Segurança.

    ![Abrir o painel Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

    São abertos os painéis Cópia de Segurança e Objetivo da Cópia de Segurança.

    ![Abrir o painel Cenário](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. No painel objetivo de cópia de segurança, defina **onde está a carga de trabalho em execução** para o Azure e **que itens pretende cópia de segurança** a Máquina Virtual, em seguida, clique em **OK**.

    A extensão da VM é registada no cofre. O painel Objetivo da Cópia de Segurança fecha-se e o painel **Política de cópias de segurança** abre-se.

    ![Abrir o painel Cenário](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. No painel Política de cópias de segurança, selecione a política de cópias de segurança que pretende aplicar ao cofre.

    ![Selecionar política de cópia de segurança](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Os detalhes da política predefinida estão listados no menu pendente. Se pretende criar uma nova política, selecione **Criar Novo** no menu pendente. Para obter instruções sobre como definir uma política de cópia de segurança, consulte o artigo [Definir uma política de cópia de segurança](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Clique em **OK** para associar a política de cópias de segurança ao cofre.

    O painel Política de cópias de segurança fecha-se e o painel **Selecionar máquinas virtuais** abre-se.
5. No painel **Selecionar máquinas virtuais**, escolha as máquinas virtuais a associar à política especificada e clique em **OK**.

    ![Selecionar a carga de trabalho](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    A máquina virtual selecionada é validada. Se não vir as máquinas virtuais que esperava, verifique se existe na mesma localização que o Cofre dos serviços de recuperação do Azure e já não estão protegidos no Cofre de outro. A localização do cofre dos Serviços de Recuperação é apresentada no dashboard do cofre.

6. Agora que definiu todas as definições do cofre, no painel Cópia de Segurança, clique em **Ativar Cópia de Segurança**. Isto implementa a política no cofre e as VMs. Não cria o ponto de recuperação inicial da máquina virtual.

    ![Ativar Backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de ativar com êxito a cópia de segurança, a sua política de cópias de segurança vai ser executada de acordo com o agendado. Se pretende gerar uma tarefa de cópia de segurança a pedido para cópia de segurança de máquinas virtuais, ver [acionar a tarefa de cópia de segurança](./backup-azure-arm-vms.md#triggering-the-backup-job).

Se tiver problemas ao registar a máquina virtual, consulte as seguintes informações sobre como instalar o agente da VM e conectividade de rede. Provavelmente, não terá as seguintes informações se estiver a proteger máquinas virtuais criadas no Azure. No entanto se tiver migrado máquinas virtuais no Azure, não se esqueça de que instalou corretamente o agente da VM e que a máquina virtual pode comunicar com a rede virtual.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalar o Agente da VM na máquina virtual
O Agente da VM do Azure tem de estar instalado na máquina virtual do Azure para que a extensão da Cópia de Segurança funcione. Se a VM foi criada a partir da galeria do Azure, em seguida, o agente da VM já se encontra presente na máquina virtual. Esta informação é fornecida para situações em que são *não* através de uma VM criada a partir da galeria do Azure - por exemplo migrou uma VM a partir de um datacenter no local. Nesse caso, o agente da VM tem de ser instalado para poder proteger a máquina virtual. Saiba mais sobre o [agente da VM](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux).

Se tiver problemas a efetuar a cópia de segurança da VM do Azure, verifique se o Agente da VM do Azure está corretamente instalado na máquina virtual (consulte a tabela abaixo). A tabela seguinte fornece informações adicionais sobre o Agente da VM para as VMs do Windows e Linux.

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalar o Agente da VM |Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necessita de privilégios de Administrador para concluir o processo de instalação. |<li> Instalar a versão mais recente [agente Linux](../virtual-machines/linux/agent-user-guide.md). Necessita de privilégios de Administrador para concluir o processo de instalação. Recomendamos que instale o agente do seu repositório de distribuição. Iremos **não é recomendada a** instalar agente de VM com Linux diretamente a partir do github.  |
| Atualizar o Agente da VM |A atualização do Agente da VM é tão simples como reinstalar os [binários do Agente da VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o agente da VM está a ser atualizado. |Siga as instruções para [atualizar o Agente da VM do Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Recomendamos a atualizar o agente do seu repositório de distribuição. Iremos **não é recomendada a** atualizar agente de VM com Linux diretamente a partir do github.<br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o Agente da VM está a ser atualizado. |
| A validar a instalação do Agente da VM |<li>Navegue até à pasta *C:\WindowsAzure\Packages* na VM do Azure. <li>Deve encontrar o ficheiro WaAppAgent.exe presente.<li> Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O campo da Versão do Produto deve ser 2.6.1198.718 ou superior. |N/D |

### <a name="backup-extension"></a>Extensão da cópia de segurança
Assim que o Agente da VM estiver instalado na máquina virtual, o serviço de Backup do Azure instala a extensão da cópia de segurança no Agente da VM. O serviço de cópia de segurança do Azure atualiza de forma totalmente integrada e patches a extensão de cópia de segurança.

A extensão de cópia de segurança é instalada pelo serviço de Cópia de Segurança quer a VM esteja ou não em execução. Uma VM em execução fornece a maior possibilidade de obter um ponto de recuperação consistente com aplicações. No entanto, o serviço Azure Backup continuará a criar cópias de segurança da VM mesmo se estiver desligado e se não for possível instalar a extensão. Este processo é conhecido como VM Offline. Neste caso, o ponto de recuperação será *consistente com a falha*.

## <a name="network-connectivity"></a>Conectividade de rede
Para gerir os instantâneos VM, a extensão de cópia de segurança necessita de conectividade para os endereços IP públicos do Azure. Sem o acesso correta à Internet, o tempo limite de pedidos de HTTP da máquina virtual e a operação de cópia de segurança falhará. Se a sua implementação tem restrições de acesso no local (através de um grupo de segurança de rede (NSG), por exemplo), em seguida, escolha uma destas opções para fornecer um caminho de simples para o tráfego de cópia de segurança:

* [Lista branca de IP do datacenter do Azure intervalos](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -consulte o artigo para obter instruções sobre como a lista branca os endereços IP.
* Implemente um servidor de proxy HTTP para o encaminhamento de tráfego.

Ao decidir qual a opção a utilizar, são os compromissos entre capacidade de gestão, o controlo granular e o custo.

| Opção | Vantagens | Desvantagens |
| --- | --- | --- |
| Intervalos de IP da lista branca |Sem custos adicionais.<br><br>Para abrir o acesso num NSG, utilize o <i>conjunto AzureNetworkSecurityRule</i> cmdlet. |Complexo de gerir como a estando afetadas alteram os intervalos de IP ao longo do tempo.<br><br>Fornece acesso a totalidade do Azure e não apenas o armazenamento. |
| Proxy HTTP |Controlo granular no proxy através de URLs de armazenamento permitido.<br>Único ponto de acesso à Internet às VMs.<br>Não sujeitos a alterações do endereço IP do Azure. |Custos adicionais para executar uma VM com o software de proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Intervalos de IP da lista branca de datacenter do Azure
* A lista de permissões os intervalos IP do datacenter do Azure, consulte o [Web site Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) para obter detalhes sobre os intervalos de IP e instruções.
* Pode utilizar tags de serviço para permitir ligações ao armazenamento de utilização de região específica [etiquetas de serviço](../virtual-network/security-overview.md#service-tags). Certifique-se de que regra que permite o acesso à conta de armazenamento está a ter prioridade superior à regra bloquear o acesso à internet. 

  ![NSG com etiquetas de armazenamento para uma região](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Etiquetas de armazenamento estão disponíveis apenas em regiões específicas que estão em pré-visualização. Para obter a lista de regiões, consulte [etiquetas do serviço de armazenamento](../virtual-network/security-overview.md#service-tags)

### <a name="using-an-http-proxy-for-vm-backups"></a>Utilizar um proxy HTTP para cópias de segurança VM
Ao fazer cópias de segurança de uma VM, a extensão de cópia de segurança na VM envia os comandos de gestão do instantâneo ao Storage do Azure para utilizar uma API de HTTPS. Encaminhar o tráfego de extensão de cópia de segurança através do proxy HTTP, uma vez que é o único componente configurado para acesso à Internet pública.

> [!NOTE]
> Não há nenhuma recomendação para o software de proxy que deve ser utilizada. Certifique-se de que escolha um proxy que é compatível com os passos de configuração abaixo.
>
>

A imagem de exemplo abaixo mostra os passos de três configuração necessárias para utilizar um proxy HTTP:

* VM de aplicação encaminha todo o tráfego HTTP vinculado a Internet pública através de VM de Proxy.
* Proxy VM permite o tráfego de entrada de VMs na rede virtual.
* O grupo de segurança de rede (NSG) com o nome de bloqueio NSF tem um segurança regra que permite saído tráfego de Internet de VM de Proxy.

![NSG com diagrama de implementação do proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Para utilizar um proxy HTTP para comunicar com a Internet pública, siga estes passos:

#### <a name="step-1-configure-outgoing-network-connections"></a>Passo 1. Configurar ligações de rede de saída
###### <a name="for-windows-machines"></a>Para máquinas do Windows
Isto irá configurar a configuração do servidor proxy para a conta de sistema Local.

1. Transferir [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Execute o seguinte comando na linha de comandos elevada,

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Irá abrir a janela do internet explorer.
3. Aceda a ferramentas -> Opções da Internet -> ligações -> definições de LAN.
4. Verifique as definições de proxy para a conta de sistema. Definir o IP de Proxy e a porta.
5. Feche o Internet Explorer.

Isto irá configurar uma configuração de proxy a nível de máquina e será utilizado para enviar tráfego HTTP/HTTPS.

Se tiver a configuração de um servidor proxy numa conta de utilizador atual (não uma conta de sistema Local), utilize o seguinte script para aplicá-las para SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Se observar "(407) a autenticação de Proxy necessário" no registo de servidor proxy, verifique a que sua autenticação está configurada corretamente.
>
>

###### <a name="for-linux-machines"></a>Para computadores Linux
Adicione a seguinte linha para o ```/etc/environment``` ficheiro:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adicione as seguintes linhas à ```/etc/waagent.conf``` ficheiro:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Passo 2. Permitir ligações de entrada no servidor de proxy:
1. No servidor de proxy, abra a Firewall do Windows. É a forma mais fácil de aceder a firewall para procurar a Firewall do Windows com segurança avançada.

    ![Abrir a Firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. Na caixa de diálogo a Firewall do Windows, clique com botão direito **regras de entrada** e clique em **nova regra...** .

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-02.png)
3. No **novo Assistente de regras de entrada**, escolha o **personalizada** opção para o **tipo de regra** e clique em **seguinte**.
4. Na página para selecionar o **programa**, escolha **todos os programas** e clique em **seguinte**.
5. No **protocolo e portas** página, introduza as seguintes informações e clique em **seguinte**:

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-03.png)

   * para *tipo de protocolo* escolha *TCP*
   * para *Porta Local* escolha *portas específicas*, no campo abaixo, especifique o ```<Proxy Port>``` que foi configurado.
   * para *portas remotas* selecione *todas as portas*

     Para o resto do assistente, clique em até ao fim e atribua um nome esta regra.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Passo 3. Adicione uma regra de exceção para o NSG:
Numa linha de comandos do Azure PowerShell, introduza o seguinte comando:

O comando seguinte adiciona uma exceção para o NSG. Esta exceção permite tráfego TCP a partir de qualquer porta no 10.0.0.5 para qualquer endereço Internet na porta 80 (HTTP) ou 443 (HTTPS). Se necessitar de uma porta específica na Internet pública, lembre-se de que adicione essa porta para o ```-DestinationPortRange``` bem.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*Estes passos utilizam valores e nomes específicos para este exemplo. Utilize os nomes e valores para a sua implementação quando os introduzir, ou cortando e colando detalhes para o seu código.*

Agora que já sabe que tem conectividade de rede, está pronto para criar cópias de segurança a VM. Consulte [cópia de segurança VMs implementadas no Resource Manager](backup-azure-arm-vms.md).

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passos seguintes
Agora que tem de preparar o ambiente para fazer cópias de segurança a VM, o próximo passo lógico é criar uma cópia de segurança. O planeamento artigo fornece informações mais detalhadas sobre a cópia de segurança de VMs.

* [Fazer uma cópia de segurança de máquinas virtuais](backup-azure-vms.md)
* [Planear a infraestrutura de cópia de segurança de VM](backup-azure-vms-introduction.md)
* [Gerir cópias de segurança da máquina virtual](backup-azure-manage-vms.md)
