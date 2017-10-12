---
title: "Primeira impressão: proteger VMs do Azure com o cofre de serviços de recuperação | Microsoft Docs"
description: "Proteger VMs do Azure com um cofre de serviços de recuperação. Utilize cópias de segurança de VMs implementadas no Resource Manager, VMs implementadas de forma Clássica e VMs do Armazenamento Premium, VMs encriptadas, VMs em Managed Disks para proteger os seus dados. Crie e registe um cofre dos serviços de recuperação. Registe VMs, crie uma política e proteja VMs no Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keyword: backups; vm backup
ms.assetid: 45e773d6-c91f-4501-8876-ae57db517cd1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/04/2017
ms.author: markgal;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 639f008eea61b973b9d32dc734d42d5c4e93e924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vaults"></a>Fazer cópia de segurança de máquinas virtuais do Azure para cofres dos Serviços de Recuperação
> [!div class="op_single_selector"]
> * [Proteger VMs com um cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md)
> * [Proteger VMs com um cofre das cópias de segurança](backup-azure-vms-first-look.md)
>
>

Este tutorial guia-o através dos passos para criar um cofre dos serviços de recuperação e efetuar a cópia de segurança de uma máquina virtual do Azure (VM). Os cofres dos serviços de recuperação protegem:

* VMs implementadas pelo Azure Resource Manager
* VMs clássicas
* VMs de armazenamento standard
* VMs do Premium Storage
* VMs em execução em Managed Disks
* VMs encriptadas com o Azure Disk Encryption
* Cópia de segurança consistente com a aplicação das VMs Windows utilizando VMs VSS e Linux que utilizam scripts de instantâneos anteriores e instantâneos posteriores personalizados

Para obter mais informações sobre como proteger VMs de Armazenamento Premium, veja o artigo [Criar cópias de segurança e Restaurar VMs do Armazenamento Premium](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Para obter mais informações sobre o suporte para VMs de discos geridos, veja [Criar cópias de segurança e restauro de VMs em discos geridos](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Para obter mais informações sobre arquitetura de scripts anteriores e posteriores para cópias de segurança de VM Linux veja [Cópia de segurança consistente com a aplicação da VM Linux com o script anterior e o script posterior] (https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Para saber mais sobre o que pode fazer uma cópia de segurança e o que não pode, veja [aqui](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

> [!NOTE]
> Este tutorial assume que já tem uma VM na sua subscrição do Azure e que tomou medidas para permitir que o serviço de cópia de segurança aceda à VM.
>
>

[!INCLUDE [learn-about-Azure-Backup-deployment-models](../../includes/backup-deployment-models.md)]

Dependendo do número de máquinas virtuais que quer proteger, pode começar em diferentes pontos de partida. Se quiser criar uma cópia de segurança de várias máquinas virtuais numa operação, aceda ao cofre dos Serviços de Recuperação e [inicie a tarefa de criação de cópia de segurança a partir do dashboard do cofre](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Se pretender criar uma cópia de segurança de uma máquina virtual individual, pode iniciar a tarefa de criação de cópia de segurança no painel de gestão da VM.

## <a name="configure-the-backup-job-from-the-vm-management-blade"></a>Configurar a tarefa de criação de cópia de segurança no painel de gestão da VM

Utilize os passos seguintes para configurar a tarefa de criação da cópia de segurança a partir do painel de gestão da máquina virtual, no portal do Azure. Estes passos não se aplicam às máquinas virtuais no portal clássico.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Mais Serviços** e, na caixa de diálogo Filtro, escreva **Máquinas virtuais**. À medida que escreve, a lista de recursos filtra. Quando vir Máquinas Virtuais, selecione-as.

  ![No menu Hub, clique em Mais Serviços para abrir a caixa de diálogo de texto e escreva Máquinas virtuais.](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  É apresentada a lista de máquinas virtuais (VMs) na subscrição.

  ![É apresentada a lista de VMs na subscrição.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Na lista, selecione uma VM da qual pretende criar uma cópia de segurança.

  ![É apresentada a lista de VMs na subscrição.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Quando seleciona a VM, a lista de máquinas virtuais passa para a esquerda e são abertos o painel de gestão e o dashboard da máquina virtual. </br>
 ![Painel de gestão da VM](./media/backup-azure-vms-first-look-arm/vm-management-blade.png)

4. No painel de gestão da VM, na secção **Definições**, clique em **Cópia de Segurança**. </br>

  ![Opção Cópia de Segurança no painel de gestão da VM](./media/backup-azure-vms-first-look-arm/backup-option-vm-management-blade.png)

  É aberto o painel Ativar cópia de segurança.

  ![Opção Cópia de Segurança no painel de gestão da VM](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

5. No cofre dos Serviços de Recuperação, clique em **Selecionar existente** e escolha o cofre na lista pendente.

  ![Assistente para Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-blade-enable-backup.png)

  Se não existirem cofres dos Serviços de Recuperação ou quiser utilizar um novo, clique em **Criar novo** e indique o nome do cofre novo. É criado um cofre novo no mesmo grupo de recursos e na mesma localização que a máquina virtual. Se quiser criar um cofre dos Serviços de Recuperação com valores diferentes, veja a secção sobre como [criar cofres dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. Para ver os detalhes da Política de cópias de segurança, clique em **Política de cópias de segurança**.

  É aberto o painel **Política de cópias de segurança**, que mostra os detalhes da política selecionada. Se houver outras políticas, utilize o menu pendente para escolher outra. Se pretende criar uma nova política, selecione **Criar Nova** no menu pendente. Para obter instruções sobre como definir uma política de cópia de segurança, consulte o artigo [Definir uma política de cópia de segurança](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Para guardar as alterações à política de cópias de segurança e regressar ao painel Ativar cópia de segurança, clique em **OK**.

  ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new-2.png)

7. No painel Ativar cópia de segurança, clique em **Ativar Cópia de Segurança** para implementar a política. A implementação da política associa-a ao cofre e às máquinas virtuais.

  ![Botão Ativar cópia de segurança](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-button.png)

8. Pode acompanhar o progresso da configuração através das notificações que aparecem no portal. O exemplo seguinte mostra que a Implementação foi iniciada.

  ![Notificação de Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Depois de concluído o progresso, no painel de gestão da VM, clique em **Cópia de Segurança** para abrir o painel Item de Cópia de Segurança e ver os detalhes.

  ![Vista de Item de Cópia de Segurança da VM](./media/backup-azure-vms-first-look-arm/backup-item-view.png)

  Enquanto a cópia de segurança inicial não for concluída, o **Estado da última cópia de segurança** é apresentado como **Aviso (cópia de segurança inicial pendente)**. Para ver quando é que vai ocorrer a próxima tarefa de cópia de segurança agendada, em **Política de cópias de segurança**, clique no nome da política. É aberto o painel Política de Cópias de Segurança, que mostra a hora da cópia de segurança agendada.

10. Para executar uma tarefa de Cópia de Segurança e criar o ponto de recuperação inicial, no painel Cofre da Cópia de Segurança, clique em **Criar Cópia de Segurança Agora**.

  ![Clique em Criar Cópia de Segurança Agora para executar a cópia de segurança inicial](./media/backup-azure-vms-first-look-arm/backup-now.png)

  É aberto o painel Criar Cópia de Segurança Agora.

  ![mostra o painel Criar Cópia de segurança Agora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. No painel Criar Cópia de Segurança Agora, clique no ícone de calendário, utilize o controlo do calendário para selecionar o último dia em que este ponto de recuperação é mantido e clique em **Cópia de Segurança**.

  ![definir o último dia em que o ponto de recuperação de Criar Cópia de Segurança Agora é mantido](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  As notificações de implementação permitem-lhe saber se a tarefa de cópia de segurança foi acionada e que pode acompanhar o progresso da tarefa na página Tarefas de cópias de segurança.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Configurar a tarefa de cópia de segurança a partir do cofre dos Serviços de Recuperação
Para configurar a tarefa de cópia de segurança, execute os seguintes passos.  

1. Crie um cofre dos Serviços de Recuperação para uma máquina virtual.
2. Utilize o portal do Azure para selecionar um Cenário, definir uma política de Cópias de Segurança e identificar itens para proteger.
3. Execute a cópia de segurança inicial.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Criar um cofre dos Serviços de Recuperação para uma VM
Um cofre dos Serviços de Recuperação é uma entidade que armazena todas as cópias de segurança e os pontos de recuperação que foram criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém a política de cópia de segurança aplicada às VMs protegidas.

> [!NOTE]
> A criação de cópias de segurança de VMs é um processo local. Não pode efetuar a cópia de segurança de VMs de uma localização para um cofre dos Serviços de Recuperação noutra localização. Por isso, para cada localização do Azure com VMs para a cópia de segurança, tem de existir, pelo menos, um cofre dos Serviços de Recuperação nessa localização.
>
>

Para criar um cofre dos Serviços de Recuperação:

1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com/) através da sua subscrição do Azure.
2. No menu Hub, clique em **Mais Serviços** e, na caixa de diálogo Filtro, escreva **Serviços de Recuperação**. À medida que escreve, a lista de recursos filtra. Quando vir os cofres dos Serviços de Recuperação, clique nos mesmos.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Se existirem cofres dos Serviços de Recuperação na subscrição, os cofres estão listados.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 3 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.

5. Na secção **Subscrição**, utilize o menu pendente para escolher a subscrição do Azure. Se utilizar apenas uma subscrição, é apresentada essa subscrição e pode avançar para o passo seguinte. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Terá várias escolhas apenas se a sua conta organizacional estiver associada a várias subscrições do Azure.

6. Na secção **Grupo de recursos**:

    * selecione **Criar novo** se pretender criar um grupo de Recursos.
    Ou
    * selecione **Utilizar existente** e clique no menu pendente para ver a lista de Grupos de recursos disponíveis.

  Para mais informações mais completas sobre os grupos de Recursos, veja a [Azure Resource Manager overview (Descrição geral do Azure Resource Manager)](../azure-resource-manager/resource-group-overview.md).

7. Clique em **Localização** para selecionar a região geográfica do cofre. Esta escolha determina a região geográfica para onde os dados da cópia de segurança são enviados.

  > [!IMPORTANT]
  > Se não tiver a certeza de qual é a localização em que se encontra a VM, feche a caixa de diálogo de criação do cofre e aceda à lista de Máquinas Virtuais no portal. Se tiver máquinas virtuais em várias regiões, crie um cofre dos Serviços de Recuperação em cada região. Crie o cofre na primeira localização antes de ir para a localização seguinte. Não é necessário especificar as contas de armazenamento utilizadas para armazenar os dados de cópia de segurança - o cofre dos Serviços de Recuperação e o serviço do Azure Backup processam automaticamente o armazenamento.
  >

8. Na parte inferior do painel do cofre dos Serviços de Recuperação, clique em **Criar**.

    Pode demorar vários minutos até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita do portal. Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação. Se depois de vários minutos não vir o cofre, clique em **Atualizar**.

    ![Clique no botão Atualizar](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Quando vir o cofre na lista de cofres dos Serviços de Recuperação, está pronto para definir a redundância de armazenamento.

Agora que criou o cofre, saiba como configurar a replicação de armazenamento.

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento
A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por predefinição, o seu cofre tem um armazenamento georredundante. Se o cofre dos Serviços de Recuperação for a sua cópia de segurança primária, deixe a opção de replicação de armazenamento definida para armazenamento georredundante. Escolha o armazenamento localmente redundante se pretender uma opção mais barata e com menos duração. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. No painel **Cofres dos Serviços de Recuperação**, selecione o novo cofre.

  ![Selecione o novo cofre da lista de cofres dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Ao selecionar o cofre, o painel Definições (*que tem o nome do cofre na parte superior*) e o painel de detalhes do cofre abrem.

  ![Ver a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)

2. No painel de Definições do novo cofre, utilize o diapositivo vertical para deslocar para baixo para a secção Gerir e clique em **Infraestrutura de Cópia de Segurança**.
    É aberto o painel Infraestrutura de Cópia de Segurança.
3. No painel Infraestrutura de Cópia de Segurança, clique em **Configuração de Cópia de Segurança** para abrir o painel **Configuração de Cópia de Segurança**.

    ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![opções de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Por predefinição, o seu cofre tem um armazenamento georredundante. Se utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o **Georredundante**. Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) nesta [Descrição geral de redundância de armazenamento](../storage/common/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selecione um objetivo da cópia de segurança, configure a política e defina os itens a proteger
Antes de registar uma VM com um cofre, execute o processo de deteção para se certificar de que as novas máquinas virtuais que foram adicionadas à subscrição são identificadas. O processo consulta o Azure para obter a lista de máquinas virtuais na subscrição, juntamente com informações adicionais, tais como a região e o nome do serviço em nuvem. No portal do Azure, o cenário refere-se ao que vai introduzir no cofre dos Serviços de Recuperação. A política é a agenda da frequência e de quando os pontos de recuperação são obtidos. A política também inclui o período de retenção para os pontos de recuperação.

1. Se já tiver um cofre dos serviços de recuperação aberto, avance para o passo 2. Caso contrário, no menu Hub, clique em **Mais Serviços** e, na lista de recursos, escreva **Serviços de Recuperação** e clique em **Cofres dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    É apresentada a lista dos cofres dos serviços de recuperação.

    ![Vista da lista dos cofres dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Na lista dos cofres dos Serviços de Recuperação, selecione um cofre para abrir o dashboard do mesmo.

     ![Abrir painel do cofre](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. No menu do dashboard do cofre, clique em **Cópia de Segurança** para abrir o painel Cópia de Segurança.

    ![Abrir o painel Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

    São abertos os painéis Cópia de Segurança e Objetivo da Cópia de Segurança.

    ![Abrir o painel Cenário](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. No painel Objetivo da Cópia de Segurança, no menu pendente **Onde está a sua carga de trabalho a ser executada**, escolha o Azure. No menu pendente **Do que pretende criar uma cópia de segurança**, escolha Máquina virtual e clique em **OK**.

    Estas ações registam a extensão da VM no cofre. O painel Objetivo da Cópia de Segurança fecha-se e o painel **Política de cópias de segurança** abre-se.

    ![Abrir o painel Cenário](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. No painel Política de cópias de segurança, selecione a política de cópias de segurança que pretende aplicar ao cofre.

    ![Selecionar política de cópia de segurança](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Os detalhes da política predefinida estão listados no menu pendente. Se pretende criar uma nova política, selecione **Criar Nova** no menu pendente. Para obter instruções sobre como definir uma política de cópia de segurança, consulte o artigo [Definir uma política de cópia de segurança](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Clique em **OK** para associar a política de cópias de segurança ao cofre.

    O painel Política de cópias de segurança fecha-se e o painel **Selecionar máquinas virtuais** abre-se.
5. No painel **Selecionar máquinas virtuais**, escolha as máquinas virtuais a associar à política especificada e clique em **OK**.

    ![Selecionar a carga de trabalho](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    A máquina virtual selecionada é validada. Se não conseguir ver as máquinas virtuais que pretende ver, verifique se existem na mesma localização do Azure que o cofre dos Serviços de Recuperação. A localização do cofre dos Serviços de Recuperação é apresentada no dashboard do cofre.

6. Agora que definiu todas as definições do cofre, no painel Cópia de Segurança, clique em **Ativar Cópia de Segurança** para implementar a política no cofre e nas VMs. A implementação da política de cópias de segurança não cria o ponto de recuperação inicial da máquina virtual.

    ![Ativar Backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de ativar com êxito a cópia de segurança, a sua política de cópias de segurança vai ser executada de acordo com o agendado. No entanto, avance e inicie a primeira tarefa de cópia de segurança.

## <a name="initial-backup"></a>Cópia de segurança inicial
Assim que tiver sido implementada uma política de cópia de segurança na máquina virtual, isso não significa que foi efetuada uma cópia de segurança dos dados. Por predefinição, a primeira cópia de segurança agendada (conforme foi definido na política de cópia de segurança) é a cópia de segurança inicial. Enquanto a cópia de segurança inicial não ocorrer, o Estado da Última Cópia de Segurança no painel **Tarefas de Cópia de Segurança** é apresentado como **Aviso (cópia de segurança inicial pendente)**.

![Cópia de segurança pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que a cópia de segurança inicial esteja prestes a ser executada, é recomendado que execute **Fazer Cópia de Segurança Agora**.

Para executar a tarefa de cópia de segurança inicial:

1. No dashboard do cofre, clique no número em **Itens de Cópia de Segurança** ou clique no mosaico **Itens de Cópia de Segurança**. <br/>
  Ícone Definições![](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  O painel **Itens de Cópia de Segurança** abre-se.

  ![Itens de cópia de segurança](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. No painel **Itens de Cópia de Segurança**, selecione o item.

  ![Ícone Definições](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  É aberta a lista **Itens de Cópia de Segurança**. <br/>

  ![Tarefa de cópia de segurança acionada](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Na lista **Itens de Cópia de Segurança**, clique nas reticências **...** para abrir o menu Contexto.

  ![Menu Contexto](./media/backup-azure-vms-first-look-arm/context-menu.png)

  É apresentado o menu Contexto.

  ![Menu Contexto](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. No menu Contexto, clique em **Criar Cópia de Segurança Agora**.

  ![Menu Contexto](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  É aberto o painel Criar Cópia de Segurança Agora.

  ![mostra o painel Criar Cópia de segurança Agora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. No painel Criar Cópia de Segurança Agora, clique no ícone de calendário, utilize o controlo do calendário para selecionar o último dia em que este ponto de recuperação é mantido e clique em **Cópia de Segurança**.

  ![definir o último dia em que o ponto de recuperação de Criar Cópia de Segurança Agora é mantido](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  As notificações de implementação permitem-lhe saber se a tarefa de cópia de segurança foi acionada e que pode acompanhar o progresso da tarefa na página Tarefas de cópias de segurança. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

6. Para ver ou acompanhar o estado da cópia de segurança inicial, no dashboard do cofre, no mosaico **Tarefas de Cópia de Segurança**, clique **Em curso**.

  ![Mosaico Tarefas de cópia de segurança](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  O painel Tarefas de cópia de segurança abre-se.

  ![Mosaico Tarefas de cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  No painel **Tarefas de cópias de segurança**, pode ver o estado de todas as tarefas. Verifique se a tarefa de cópia de segurança da sua VM ainda está em curso ou se foi concluída. Quando uma tarefa de cópia de segurança estiver concluída, o estado é *Concluído*.

  > [!NOTE]
  > Como parte da operação de cópia de segurança, o serviço Azure Backup emite um comando para a extensão da cópia de segurança em cada VM para esvaziar todas as escritas e tirar um instantâneo consistente.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalar o Agente da VM na máquina virtual
Estas informações são fornecidas no caso de serem necessárias. O Agente da VM do Azure tem de estar instalado na máquina virtual do Azure para que a extensão da Cópia de Segurança funcione. Contudo, se a VM foi criada a partir da galeria do Azure, o Agente da VM já se encontra presente na máquina virtual. As VMs que são migradas dos datacenters no local não teriam o Agente da VM instalado. Nesse caso, o Agente da VM tem de ser instalado. Se tiver problemas a efetuar a cópia de segurança da VM do Azure, verifique se o Agente da VM do Azure está corretamente instalado na máquina virtual (veja a tabela seguinte). Se criar uma VM personalizada, [confirme que a caixa de verificação **Instalar o Agente da VM** está selecionada](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) antes de aprovisionar a máquina virtual.

Saiba mais sobre o [Agente da VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como o instalar](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A tabela seguinte fornece informações adicionais sobre o Agente da VM para as VMs do Windows e Linux.

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalar o Agente da VM |<li>Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Precisa de privilégios de Administrador para concluir a instalação. <li>[Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |<li> Instale o [Agente Linux](https://github.com/Azure/WALinuxAgent) a partir do GitHub. Precisa de privilégios de Administrador para concluir a instalação. <li> [Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |
| Atualizar o Agente da VM |A atualização do Agente da VM é tão simples como reinstalar os [binários do Agente da VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o agente da VM está a ser atualizado. |Siga as instruções para [atualizar o Agente da VM do Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o Agente da VM está a ser atualizado. |
| A validar a instalação do Agente da VM |<li>Navegue até à pasta *C:\WindowsAzure\Packages* na VM do Azure. <li>Deve encontrar o ficheiro WaAppAgent.exe presente.<li> Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O campo da Versão do Produto deve ser 2.6.1198.718 ou superior. |N/D |

### <a name="backup-extension"></a>Extensão da cópia de segurança
Assim que o Agente da VM estiver instalado na máquina virtual, o serviço de Backup do Azure instala a extensão da cópia de segurança no Agente da VM. O serviço de Backup do Azure atualiza de forma totalmente integrada e aplica os patches à extensão da cópia de segurança sem intervenção adicional do utilizador.

O serviço de Cópia de Segurança instala a extensão de cópia de segurança, mesmo se a VM não estiver em execução. Uma VM em execução fornece a maior possibilidade de obter um ponto de recuperação consistente com aplicações. No entanto, o serviço Azure Backup continuará a criar cópias de segurança da VM mesmo se estiver desligado e se não for possível instalar a extensão. Este tipo de cópia de segurança é conhecido como VM Offline, e o ponto de recuperação é *consistente com a falha*.

## <a name="troubleshooting-information"></a>Informações sobre a resolução de problemas
Se tiver problemas ao realizar algumas das tarefas neste artigo, veja a [Orientação na resolução de problemas](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Preços
O custo de fazer cópias de segurança das VMs do Azure baseia-se no número de instâncias protegidas. Para uma definição de instância protegida, veja [o que é uma instância protegida](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Para obter um exemplo de como calcular o custo de fazer uma cópia de segurança de uma máquina virtual, veja [Como as instâncias protegidas são calculadas](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances). Consulte a página de Preços do Azure Backup para obter informações sobre [Preços da Cópia de Segurança](https://azure.microsoft.com/pricing/details/backup/).

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).
