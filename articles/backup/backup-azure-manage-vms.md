---
title: "Gerir cópias de segurança de máquinas de virtuais implementadas no Resource Manager | Microsoft Docs"
description: "Saiba como gerir e monitorizar as cópias de segurança de máquinas de virtuais implementadas no Resource Manager"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: f3050283-d60f-472d-b464-cb844e70d67e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk;markgal
ms.openlocfilehash: 35a21cb99ca4bad124a9f764cef9da453e1fe47f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-virtual-machine-backups"></a>Gerir cópias de segurança de máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Gerir cópias de segurança de VM do Azure](backup-azure-manage-vms.md)
> * [Gerir cópias de segurança de VMS clássicas](backup-azure-manage-vms-classic.md)
>
>

Este artigo fornece orientações para a gestão de cópias de segurança da VM e explica as informações de alertas de cópias de segurança disponíveis no dashboard do portal. As orientações neste artigo aplica-se a utilização de VMs com os cofres dos serviços de recuperação. Este artigo não abrange a criação de máquinas virtuais, nem explica como proteger máquinas virtuais. Para um manual sobre a proteção de VMs implementadas no Azure Resource Manager no Azure com um cofre dos serviços de recuperação, consulte [primeiras impressões: cópia de segurança VMs para um cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Gerir cofres e as máquinas virtuais protegidas
No portal do Azure, o dashboard do cofre dos serviços de recuperação fornece acesso a informações sobre o cofre, incluindo:

* o instantâneo de cópia de segurança mais recente, que também é o ponto de restauro mais recente < br\>
* a política de cópia de segurança < br\>
* tamanho total de todos os instantâneos de cópia de segurança < br\>
* número de máquinas virtuais que estão protegidas com o Cofre < br\>

Várias tarefas de gestão com uma cópia de segurança da máquina virtual começam por abrir o Cofre no dashboard. No entanto, porque cofres podem ser utilizados para proteger vários itens (ou várias VMs), para ver detalhes sobre uma VM específica, abra o dashboard do cofre item. O procedimento seguinte mostra como abrir o *dashboard do cofre* e, em seguida, avance para o *dashboard do cofre item*. Existem "sugestões" em ambos os procedimentos de ponto sobre como adicionar o Cofre e utilizando o Pin para o comando de dashboard do cofre item para o dashboard do Azure. Afixar ao dashboard é uma forma de criar um atalho para o cofre ou item. Também pode executar comandos comuns do atalho.

> [!TIP]
> Se tiver vários dashboards e abrir painéis, utilize o controlo de deslize escuros blue na parte inferior da janela para deslize anterior e descritos o dashboard do Azure.
>
>

![Visão completa com controlo de deslize](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Abra um cofre dos serviços de recuperação no dashboard:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    É apresentada a lista dos cofres dos Serviços de Recuperação.

    ![Lista de serviços de recuperação cofres dos ](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

   > [!TIP]
   > Se fixar um cofre para o Dashboard do Azure, que Cofre é imediatamente acessível quando abrir o portal do Azure. Para afixar um cofre para o dashboard, na lista de cofre, faça duplo clique no cofre e selecione **afixar ao dashboard**.
   >
   >
3. Na lista de cofres, selecione o cofre para abrir o dashboard. Quando seleciona o cofre, o dashboard do cofre e o **definições** painel aberta. Na imagem seguinte, o **Contoso cofre** dashboard é realçado.

    ![Abra o dashboard do cofre e o painel de definições](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Abra um dashboard do cofre item
No procedimento anterior abrir o dashboard do cofre. Para abrir o dashboard do cofre item:

1. No dashboard do cofre, no **itens de cópia de segurança** mosaico, clique em **Virtual Machines do Azure**.

    ![Mosaico de abrir itens de cópia de segurança](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    O **itens de cópia de segurança** painel lista a última tarefa de cópia de segurança para cada item. Neste exemplo, não há uma máquina virtual, demovm-markgal, protegida por neste cofre.  

    ![Mosaico de itens de cópia de segurança](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Para facilidade de acesso, pode afixar um item de cofre para o Dashboard do Azure. Para afixar um item de cofre, na lista de itens do cofre, o item com o botão direito e selecione **afixar ao dashboard**.
   >
   >
2. No **itens de cópia de segurança** painel, clique no item para abrir o dashboard do cofre item.

    ![Mosaico de itens de cópia de segurança](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    O dashboard do cofre item e a respetiva **definições** painel aberta.

    ![Dashboard de itens de cópia de segurança com o painel de definições](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    A partir do dashboard de item cofre, pode realizar muitas tarefas de gestão de chaves, tais como:

   * alterar políticas ou criar uma nova política de cópia de segurança < br\>
   * ver os pontos de restauro e ver o respetivo estado de consistência < br\>
   * cópia de segurança a pedido de uma máquina virtual < br\>
   * parar a proteção de máquinas virtuais < br\>
   * retomar a proteção de uma máquina virtual < br\>
   * eliminar uma cópia de segurança dados (ou ponto de recuperação) < br\>
   * [restaurar a cópia de segurança discos](backup-azure-arm-restore-vms.md#restore-backed-up-disks) < br\>

Para obter os procedimentos seguintes, o ponto de partida é o item de dashboard do cofre.

## <a name="manage-backup-policies"></a>Gerir políticas de cópia de segurança
1. No [dashboard do cofre item](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **todas as definições** para abrir o **definições** painel.

    ![Painel política de cópia de segurança](./media/backup-azure-manage-vms/all-settings-button.png)
2. No **definições** painel, clique em **política de cópia de segurança** para abrir o mesmo.

    No painel de detalhes de intervalo de frequência e de retenção de cópias de segurança são apresentados.

    ![Painel política de cópia de segurança](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Do **escolher a política de cópia de segurança** menu:

   * Para alterar as políticas, selecione uma política diferente e clique em **guardar**. A nova política é imediatamente aplicada ao cofre. < br\>
   * Para criar uma política, selecione **criar novo**.

     ![Cópia de segurança da máquina virtual](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Para obter instruções sobre como criar uma política de cópia de segurança, consulte [definir uma política de cópia de segurança](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Ao gerir políticas de cópia de segurança, certifique-se a seguir a [melhores práticas](backup-azure-vms-introduction.md#best-practices) para um desempenho ideal cópia de segurança
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Cópia de segurança a pedido de uma máquina virtual
Pode demorar um pedido cópia de segurança de uma máquina virtual, assim que estiver configurado para proteção. Se a cópia de segurança inicial está pendente, a cópia de segurança a pedido cria uma cópia completa da máquina virtual no cofre dos serviços de recuperação. Se a cópia de segurança inicial estiver concluída, uma cópia de segurança a pedido só irá enviar as alterações do instantâneo anterior, para o Cofre dos serviços de recuperação. Ou seja, cópias de segurança subsequentes são sempre incrementais.

> [!NOTE]
> O intervalo de retenção para uma cópia de segurança a pedido é o valor de retenção especificado para o ponto de cópia de segurança diário na política. Não se for selecionado nenhum ponto de cópia de segurança diário, em seguida, é utilizado o ponto de cópia de segurança semanal.
>
>

Para acionar uma cópia de segurança a pedido de uma máquina virtual:

* No [dashboard do cofre item](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **cópia de segurança agora**.

    ![Cópia de segurança agora botão](./media/backup-azure-manage-vms/backup-now-button.png)

    O portal certifica-se de que pretende iniciar uma tarefa de cópia de segurança a pedido. Clique em **Sim** para iniciar a tarefa de cópia de segurança.

    ![Cópia de segurança agora botão](./media/backup-azure-manage-vms/backup-now-check.png)

    A tarefa de cópia de segurança cria um ponto de recuperação. O período de retenção dos pontos de recuperação é igual ao período de retenção especificado na política de associado à máquina virtual. Para acompanhar o progresso da tarefa, no dashboard do cofre, clique em de **tarefas de cópia de segurança** mosaico.  

## <a name="stop-protecting-virtual-machines"></a>Parar a proteção de máquinas virtuais
Se optar por deixar de proteger uma máquina virtual, é-lhe perguntado se pretende manter os pontos de recuperação. Existem duas formas de parar a proteção de máquinas virtuais:

* todas as tarefas de cópia de segurança futuras de parar e eliminar todos os pontos de recuperação, ou
* parar todas as tarefas de cópia de segurança futuras, mas deixe os pontos de recuperação <br/>

Não há um custo associado deixando os pontos de recuperação no armazenamento. No entanto, a vantagem de abandonar o fileparser os pontos de recuperação é que possível restaurar a máquina virtual mais tarde, se assim o desejar. Para informações sobre o custo de abandonar o fileparser os pontos de recuperação, consulte o [detalhes de preços](https://azure.microsoft.com/pricing/details/backup/). Se optar por eliminar todos os pontos de recuperação, não é possível restaurar a máquina virtual.

Para parar a proteção para uma máquina virtual:

1. No [dashboard do cofre item](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **cópia de segurança de paragem**.

    ![Parar o botão de cópia de segurança](./media/backup-azure-manage-vms/stop-backup-button.png)

    É aberto o painel de parar a cópia de segurança.

    ![Parar painel cópia de segurança](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. No **parar a cópia de segurança** painel, escolha se pretende manter ou eliminar os dados de cópia de segurança. A caixa de informações fornece detalhes sobre a sua escolha.

    ![Interrompa a proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Se tiver optado por manter os dados de cópia de segurança, avance para o passo 4. Se optar por eliminar dados de cópia de segurança, confirme que pretende parar as tarefas de cópia de segurança e eliminar os pontos de recuperação - escreva o nome do item.

    ![Parar de verificação](./media/backup-azure-manage-vms/item-verification-box.png)

    Se não tem a certeza de que o nome do item, coloque o cursor sobre o ponto de exclamação para ver o nome. Além disso, o nome do item está sob **parar a cópia de segurança** na parte superior do painel.
4. Opcionalmente, fornecer um **razão** ou **comentário**.
5. Para parar a tarefa de cópia de segurança para o item atual, clique em ![botão Parar de cópia de segurança](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Uma mensagem de notificação permite-lhe saber que as tarefas de cópia de segurança foram paradas.

    ![Confirme a interrupção de proteção](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Retomar a proteção de uma máquina virtual
Se o **reter dados de cópia de segurança** opção foi escolhida quando a proteção da máquina virtual foi parada, em seguida, é possível retomar a proteção. Se o **eliminar dados de cópia de segurança** foi escolhida a opção, em seguida, não é possível retomar a proteção da máquina virtual.

Para retomar a proteção da máquina virtual

1. No [dashboard do cofre item](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **retomar cópias de segurança**.

    ![Retomar a proteção](./media/backup-azure-manage-vms/resume-backup-button.png)

    É aberto o painel de política de cópia de segurança.

   > [!NOTE]
   > Quando voltar a proteger a máquina virtual, pode escolher uma política diferente do que a política com o qual proteger máquina virtual foi inicialmente.
   >
   >
2. Siga os passos no [gerir políticas de cópia de segurança](backup-azure-manage-vms.md#manage-backup-policies) para atribuir a política para a máquina virtual.

    Assim que é aplicada a política de cópia de segurança para a máquina virtual, consulte a mensagem seguinte.

    ![VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança
Pode eliminar os dados de cópia de segurança associados uma máquina virtual durante o **cópia de segurança de paragem** tarefa, ou em qualquer altura após a cópia de segurança tarefa foi concluída. Mesmo pode ser vantajoso a aguardar dias ou semanas antes de eliminar os pontos de recuperação. Ao contrário de restaurar os pontos de recuperação, aquando da eliminação de dados de cópia de segurança, não é possível escolher pontos de recuperação específico para eliminar. Se optar por eliminar os dados de cópia de segurança, tem de eliminar todos os pontos de recuperação associados ao item.

O procedimento seguinte assume que a tarefa de cópia de segurança para a máquina virtual foi parada ou desativada. Depois da tarefa de cópia de segurança estiver desativada, o **retomar cópias de segurança** e **Eliminar cópia de segurança** opções estão disponíveis no dashboard do item do cofre.

![Retomar e eliminar botões](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Para eliminar dados de cópia de segurança numa máquina virtual com o *desativado de cópia de segurança*:

1. No [dashboard do cofre item](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Eliminar cópia de segurança**.

    ![Tipo VM](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    O **eliminar dados de cópia de segurança** abre o painel.

    ![Tipo VM](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Escreva o nome do item para confirmar que pretende eliminar os pontos de recuperação.

    ![Parar de verificação](./media/backup-azure-manage-vms/item-verification-box.png)

    Se não tem a certeza de que o nome do item, coloque o cursor sobre o ponto de exclamação para ver o nome. Além disso, o nome do item está sob **eliminar dados de cópia de segurança** na parte superior do painel.
3. Opcionalmente, fornecer um **razão** ou **comentário**.
4. Para eliminar os dados de cópia de segurança para o item atual, clique em ![botão Parar de cópia de segurança](./media/backup-azure-manage-vms/delete-button.png)

    Uma mensagem de notificação permite-lhe saber que os dados de cópia de segurança foi eliminados.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre como voltar a criar uma máquina virtual a partir de um ponto de recuperação, consulte [restaurar VMs do Azure](backup-azure-restore-vms.md). Se precisar de informações sobre a proteção de máquinas virtuais, consulte o artigo [primeiras impressões: cópia de segurança VMs para um cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md). Para obter informações sobre eventos de monitorização, consulte [monitorizar alertas de cópias de segurança da máquina virtual do Azure](backup-azure-monitor-vms.md).
