<properties
    pageTitle="Primeira impressão: proteger VMs do Azure com o cofre de serviços de recuperação | Microsoft Azure"
    description="Proteger VMs do Azure com um cofre de serviços de recuperação. Utilize cópias de segurança de VMs implementadas no Resource Manager, VMs implementadas de forma Clássica e VMs do Armazenamento Premium para proteger os seus dados. Crie e registe um cofre dos serviços de recuperação. Registe VMs, crie uma política e proteja VMs no Azure."
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="cfreeman"
    editor=""
    keyword="backups; vm backup"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="06/03/2016"
    ms.author="markgal; jimpark"/>


# Primeira impressão: proteger VMs do Azure com um cofre de serviços de recuperação

> [AZURE.SELECTOR]
- [Primeira impressão: proteger VMs com um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md)
- [Primeira Impressão: proteger VMs do Azure com o cofre de cópia de segurança](backup-azure-vms-first-look.md)

Este tutorial guia-o através dos passos para criar um cofre dos serviços de recuperação e efetuar a cópia de segurança de uma máquina virtual do Azure (VM). Os cofres dos serviços de recuperação protegem:

- VMs implementadas pelo Azure Resource Manager
- VMs clássicas
- VMs de armazenamento standard
- VMs do Premium Storage
- VMs encriptadas com o Azure Disk Encryption, com BEK e KEK (suportado com o Powershell)

Para obter informações adicionais sobre a proteção de VMs do Premium Storage, consulte o artigo [Criar cópias de segurança e Restaurar VMs de Armazenamento Premium](backup-introduction-to-azure-backup.md#back-up-and-restore-premium-storage-vms)

>[AZURE.NOTE] Este tutorial assume que já tem uma VM na sua subscrição do Azure e que tomou medidas para permitir que o serviço de cópia de segurança aceda à VM. O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../resource-manager-deployment-model.md). Este artigo é para utilizar com o Resource Manager e as VMs implementadas no Resource Manager.

A um nível elevado, eis os passos que irá concluir.  

1. Crie um cofre dos serviços de recuperação para uma VM.
2. Utilize o portal do Azure para selecionar um Cenário, definir uma Política e identificar itens para proteger.
3. Execute a cópia de segurança inicial.



## Passo 1 – criar um cofre dos serviços de recuperação para uma VM

Um cofre dos serviços de recuperação é uma entidade que armazena todas as cópias de segurança e os pontos de recuperação que foram criados ao longo do tempo. O cofre dos serviços de recuperação também contém a política de cópias de segurança aplicada às VMs protegidas.

>[AZURE.NOTE] A criação de cópias de segurança de VMs é um processo local. Não pode efetuar a cópia de segurança de VMs de uma localização para um cofre dos serviços de recuperação noutra localização. Por isso, para cada localização do Azure com VMs para a cópia de segurança, tem de existir, pelo menos, um cofre dos serviços de recuperação nessa localização.


Para criar um cofre dos serviços de recuperação:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação**. À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação.

3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 5 da Criação do cofre dos Serviços de Recuperação](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.

5. Clique em **Subscrição** para ver a lista de subscrições disponíveis. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Apenas haverá várias escolhas se a sua conta organizacional estiver associada a várias subscrições do Azure.

6. Clique em **Grupo de recursos** para ver a lista de Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para mais informações mais completas sobre os grupos de Recursos, veja [Descrição geral do Azure Resource Manager](../resource-group-overview.md)

7. Clique em **Localização** para selecionar a região geográfica do cofre. O cofre **tem de** estar na mesma região que as máquinas virtuais que pretende proteger.

    >[AZURE.IMPORTANT] Se não tiver a certeza de qual é a localização em que se encontra a VM, feche a caixa de diálogo de criação do cofre e aceda à lista de Máquinas Virtuais no portal. Se tiver máquinas virtuais em várias regiões, terá de criar um cofre dos serviços de recuperação em cada região. Crie o cofre na primeira localização antes de ir para a localização seguinte. Não é necessário especificar contas de armazenamento para armazenar os dados de cópia de segurança – o cofre dos serviços de recuperação e o serviço de Backup do Azure processam isto automaticamente.

8. Clique em **Criar**. Pode demorar algum tempo até que o cofre dos serviços de recuperação seja criado. Monitorize as notificações de estado na área superior direita no portal. Quando o cofre for criado, aparecerá na lista de cofres dos serviços de recuperação.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)

Agora que criou o cofre, saiba como configurar a replicação de armazenamento.

### Definir Replicação de Armazenamento

A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por predefinição, o seu cofre tem um armazenamento georredundante. Deixe a opção definida para armazenamento georredundante se for a sua cópia de segurança primária. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/storage-redundancy.md#locally-redundant-storage) na [descrição geral da replicação de Armazenamento do Azure](../storage/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione o cofre para abrir o dashboard do cofre e o painel de Definições. Se o painel **Definições** não abrir, clique em **Todas as definições** no dashboard do cofre.

2. No painel **Definições**, clique em **Infraestrutura de Cópia de Segurança** > **Configuração de Cópia de Segurança** para abrir o painel **Configuração de Cópia de Segurança**. No painel **Configuração de Cópia de Segurança**, escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Após escolher a opção de armazenamento para o cofre, está pronto para associar a VM com o cofre. Para começar a associação, detete e registe as máquinas virtuais do Azure.

## Passo 2 - selecionar um objetivo de cópia de segurança, configurar a política e definir os itens para proteger

Antes de registar uma VM com um cofre, execute o processo de deteção para se certificar de que as novas máquinas virtuais que foram adicionadas à subscrição são identificadas. O processo consulta o Azure para obter a lista de máquinas virtuais na subscrição, juntamente com informações adicionais, tais como a região e o nome do serviço em nuvem. No portal do Azure, o cenário refere-se ao que vai introduzir no cofre dos Serviços de Recuperação. A política é a agenda da frequência e de quando os pontos de recuperação são obtidos. A política também inclui o período de retenção para os pontos de recuperação.

1. Se já tiver um cofre dos serviços de recuperação aberto, avance para o passo 2. Se não tiver um cofre dos serviços de recuperação aberto, mas está no portal do Azure, no menu Hub, clique em **Procurar**.

  - Na lista de recursos, escreva **Serviços de Recuperação**.
  - À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação.
  - Na lista dos cofres dos serviços de recuperação, selecione um cofre.

    O dashboard do cofre selecionado é aberto.

    ![Abrir painel do cofre](./media/backup-azure-vms-first-look-arm/vault-settings.png)

2. No menu do dashboard do cofre, clique em **Cópia de segurança** para abrir o painel Backup.

    ![Abrir o painel Backup](./media/backup-azure-vms-first-look-arm/backup-button.png)

    Quando abre o painel, o serviço de Cópia de Segurança pesquisa VMs novas na subscrição.

    ![Detetar VMs](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)

3. No painel Cópia de Segurança, clique em **Objetivo de cópia de segurança** para abrir o painel Objetivo de Cópia de Segurança.

    ![Abrir o painel Cenário](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)

4. No painel Objetivo de Cópia de Segurança, defina **Onde está a carga de trabalho em execução** para o Azure e **Pretende efetuar uma cópia de segurança de que itens** para a Máquina virtual e, em seguida, clique em **OK**.

    O painel Objetivo de Cópia de Segurança fecha-se e o painel Política de cópia de segurança abre-se.

    ![Abrir o painel Cenário](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)

5. No painel Política de cópia de segurança, selecione a política de cópia de segurança que pretende aplicar ao cofre e clique em **OK**.

    ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)

    Os detalhes da política predefinida são listados nos detalhes. Se pretende criar uma nova política, selecione **Criar Novo** no menu pendente. O menu pendente também fornece uma opção para mudar a hora quando o instantâneo é tirado para as 19:00. Para obter instruções sobre como definir uma política de cópia de segurança, consulte o artigo [Definir uma política de cópia de segurança](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Assim que clicar em **OK**, a política de cópia de segurança é associada ao cofre.

    Em seguida, escolha as VMs a associar ao cofre.

6. Escolha as máquinas virtuais a associar à política especificada e clique em **Selecionar**.

    ![Selecionar a carga de trabalho](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)

    Se não vir a VM pretendida, verifique se existe na mesma localização do Azure que o cofre dos Serviços de Recuperação.

7. Agora que definiu todas as definições para o cofre, no painel Backup, clique em **Ativar Backup** na parte inferior da página. Isto implementa a política no cofre e as VMs.

    ![Ativar Backup](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)


## Passo 3 - cópia de segurança inicial

Assim que tiver sido implementada uma política de cópia de segurança na máquina virtual, isso não significa que foi efetuada uma cópia de segurança dos dados. Por predefinição, a primeira cópia de segurança agendada (conforme foi definido na política de cópia de segurança) é a cópia de segurança inicial. Enquanto a cópia de segurança inicial não ocorrer, o Estado da Última Cópia de Segurança no painel **Tarefas de Cópia de Segurança** é apresentado como **Aviso (cópia de segurança inicial pendente)**.

![Cópia de segurança pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que a cópia de segurança inicial comece muito em breve, é recomendado que execute **Efetuar Cópia de Segurança Agora**.

Para executar **Efetuar Cópia de Segurança Agora**:

1. No dashboard do cofre, no mosaico **Cópia de Segurança**, clique em ** Virtual Machines do Azure** <br/>
    ![Ícone Definições](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    O painel **Itens de Cópia de Segurança** abre-se.

2. No painel **Itens de Cópia de Segurança**, clique com o botão direito do rato no cofre para a cópia de segurança e clique em **Efetuar cópia de segurança agora**.

    ![Ícone Definições](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    A Tarefa de cópia de segurança é acionada. <br/>

    ![Tarefa de cópia de segurança acionada](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. Para verificar se a cópia de segurança inicial foi concluída, no dashboard do cofre, no mosaico **Tarefas de Cópia de Segurança**, clique em ** Virtual Machines do Azure**.

    ![Mosaico Tarefas de cópia de segurança](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    O painel Tarefas de cópia de segurança abre-se.

4. No painel Tarefas de cópia de segurança, pode ver o estado de todas as tarefas.

    ![Mosaico Tarefas de cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Como parte da operação de cópia de segurança, o serviço de Backup do Azure emite um comando para a extensão da cópia de segurança em cada máquina virtual para esvaziar todas as escritas e tirar um instantâneo consistente.

    Quando a tarefa de cópia de segurança estiver concluída, o estado é *Concluído*.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## Instalar o Agente da VM na máquina virtual

Estas informações são fornecidas no caso de serem necessárias. O Agente da VM do Azure tem de estar instalado na máquina virtual do Azure para que a extensão da Cópia de Segurança funcione. Contudo, se a VM foi criada a partir da galeria do Azure, o Agente da VM já se encontra presente na máquina virtual. As VMs que são migradas dos datacenters no local não teriam o Agente da VM instalado. Nesse caso, o Agente da VM tem de ser instalado. Se tiver problemas a efetuar a cópia de segurança da VM do Azure, verifique se o Agente da VM do Azure está corretamente instalado na máquina virtual (consulte a tabela abaixo). Se criar uma VM personalizada, [certifique-se de que a caixa de verificação **Instalar o Agente da VM** está selecionada](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) antes do aprovisionamento da máquina virtual.

Saiba mais sobre o [Agente da VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como o instalar](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

A tabela seguinte fornece informações adicionais sobre o Agente da VM para as VMs do Windows e Linux.

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalar o Agente da VM | <li>Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necessita de privilégios de Administrador para concluir o processo de instalação. <li>[Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. | <li> Instale o [Agente Linux](https://github.com/Azure/WALinuxAgent) a partir do GitHub. Necessita de privilégios de Administrador para concluir o processo de instalação. <li> [Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |
| Atualizar o Agente da VM | A atualização do Agente da VM é tão simples como reinstalar os [binários do Agente da VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o agente da VM está a ser atualizado. | Siga as instruções para [atualizar o Agente da VM do Linux ](../virtual-machines-linux-update-agent.md). <br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o Agente da VM está a ser atualizado. |
| A validar a instalação do Agente da VM | <li>Navegue até à pasta *C:\WindowsAzure\Packages* na VM do Azure. <li>Deve encontrar o ficheiro WaAppAgent.exe presente.<li> Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O campo da Versão do Produto deve ser 2.6.1198.718 ou superior. | N/D |


### Extensão da cópia de segurança

Assim que o Agente da VM estiver instalado na máquina virtual, o serviço de Backup do Azure instala a extensão da cópia de segurança no Agente da VM. O serviço de Backup do Azure atualiza de forma totalmente integrada e aplica os patches à extensão da cópia de segurança sem intervenção adicional do utilizador.

A extensão de cópia de segurança é instalada pelo serviço de Cópia de Segurança quer a VM esteja ou não em execução. Uma VM em execução fornece a maior possibilidade de obter um ponto de recuperação consistente com aplicações. No entanto, o serviço de Backup do Azure continuará a criar cópias de segurança da VM mesmo se estiver desligado e se não for possível instalar a extensão. Este processo é conhecido como VM Offline. Neste caso, o ponto de recuperação será *consistente com a falha*.

## Informações sobre a resolução de problemas
Se tiver problemas ao realizar algumas das tarefas neste artigo, consulte o artigo [Orientação na resolução de problemas](backup-azure-vms-troubleshoot.md).


## Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).



<!--HONumber=ago16_HO4-->


