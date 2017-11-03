---
title: "Cópia de segurança do Azure máquinas virtuais à escala | Microsoft Docs"
description: "Simultaneamente fazer cópias de segurança várias máquinas virtuais do Azure"
services: backup
keywords: "cópia de segurança da máquina virtual; máquina virtual criar cópias de segurança; fazer uma cópia de segurança de vm; cópia de segurança vm; cópia de segurança de vm do Azure; cópia de segurança e recuperação após desastre"
author: markgalioto
ms.author: markgal
ms.date: 09/16/2017
ms.topic: article
ms.service: backup
ms.openlocfilehash: f144db921a8b2d01dbfe883a48574c4fabdae6f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Utilize o portal do Azure para cópia de segurança de várias máquinas virtuais

Quando a cópia de segurança de dados no Azure, armazenar os dados num recurso do Azure chamado um cofre dos serviços de recuperação. O recurso do Cofre de serviços de recuperação está disponível no menu de definições de serviços mais do Azure. A vantagem de ter o Cofre de serviços de recuperação integrado no menu Definições serviços do Azure mais torna muito mais fácil fazer uma cópia de segurança de dados. No entanto, individualmente trabalhar com cada base de dados ou a máquina virtual na sua empresa é tedious. E se pretender fazer uma cópia dos dados para todas as máquinas virtuais de um departamento ou numa única localização? É fácil fazer cópias de segurança de várias máquinas virtuais ao criar uma política de cópia de segurança e aplicar essa política para as máquinas virtuais pretendidas. Este tutorial explica como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Definir uma política de cópia de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Acionar uma tarefa de cópia de segurança a pedido para máquinas virtuais protegidas

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

O Cofre dos serviços de recuperação contém os dados de cópia de segurança e a política de cópia de segurança aplicada às máquinas virtuais protegidas. A cópia de segurança das máquinas virtuais é um processo local. Não é possível criar cópias de segurança uma máquina virtual de uma localização para um cofre dos serviços de recuperação noutra localização. Por isso, para cada localização do Azure com máquinas virtuais a cópia de segurança, deve existir, pelo menos, um cofre de serviços de recuperação nessa localização.

1. No menu da esquerda, selecione **mais serviços** e na lista de serviços, escreva *dos serviços de recuperação*. À medida que escreve, a lista de recursos filtra. Quando vir os cofres dos serviços de recuperação na lista, selecione-o para abrir o menu de cofres dos serviços de recuperação.

    ![Abrir menu do Cofre de serviços de recuperação](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. No **cofres dos serviços de recuperação** menu, clique em **adicionar** para abrir o menu do Cofre de serviços de recuperação.

    ![Menu abrir Cofre](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. Nos serviços de recuperação cofre menu, 

    - Tipo *myRecoveryServicesVault* no **nome**,
    - A subscrição atual ID aparece no **subscrição**. Se tiver subscrições adicionais, poderia escolher outra subscrição para o novo cofre.
    - Para **grupo de recursos** selecione **utilizar existente** e escolha *myResourceGroup*. Se *myResourceGroup* não existe, selecione **criar nova** e tipo *myResourceGroup*.
    - Do **localização** menu pendente, escolha *Europa Ocidental*.
    - Clique em **criar** para criar o seu Cofre de serviços de recuperação.

Um cofre dos serviços de recuperação tem de ser na mesma localização que as máquinas virtuais a ser protegido. Se tiver máquinas virtuais em várias regiões, crie um cofre de serviços de recuperação em cada região. Este tutorial cria um cofre dos serviços de recuperação *Europa Ocidental* uma vez que é onde *myVM* (a máquina virtual criada com o guia de introdução) foi criada.

Pode demorar vários minutos até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita do portal. Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação.

Quando criar um cofre dos serviços de recuperação, por predefinição o Cofre tem um armazenamento georredundante. Para fornecer resiliência de dados, armazenamento georredundante replica os dados de várias vezes em duas regiões do Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Definir a política de cópia de segurança para proteger as VMs

Depois de criar o Cofre dos serviços de recuperação, o próximo passo é configurar o cofre para o tipo de dados e definir a política de cópia de segurança. Política de cópia de segurança é a agenda da frequência e quando são tirados pontos de recuperação. A política também inclui o período de retenção para os pontos de recuperação. Para este tutorial vamos supor que sua empresa é uma desporto complexas com um átrios, stadium e restaurants e concessions e estiver a proteger os dados nas máquinas virtuais. Os seguintes passos criar uma política de cópia de segurança para os dados financeiros.

1. Na lista de cofres dos serviços de recuperação, selecione **myRecoveryServicesVault** para abrir o dashboard.

   ![Abra o menu do cenário](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. No menu do dashboard do cofre, clique em **cópia de segurança** para abrir o menu de cópia de segurança.

3. No menu do objetivo de cópia de segurança, no **onde está a carga de trabalho em execução** menu pendente, escolha *Azure*. Do **que itens pretende cópia de segurança** pendente, escolha *Máquina Virtual*e clique em **cópia de segurança**.

    Estas ações preparar o Cofre dos serviços de recuperação para interagir com uma máquina virtual. Os cofres dos serviços de recuperação tem uma política predefinida que cria um ponto de restauro por dia e mantém os pontos de restauro durante 30 dias.

    ![Abra o menu do cenário](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Para criar uma nova política, no menu de política de cópia de segurança, a partir de **escolher a política de cópia de segurança** menu pendente, selecione *criar novo*.

    ![Selecionar a carga de trabalho](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. No **política de cópia de segurança** menu, para **nome da política** tipo *financeiro*. Introduza as seguintes alterações para a política de cópia de segurança: 
    - Para **frequência de cópia de segurança** definir o fuso horário para *Hora Central*. Uma vez que o desporto complexas na Texas, o proprietário pretende a temporização ser locais. Deixe a frequência de cópia de segurança definido para diariamente às 3:30 AM.
    - Para **retenção do ponto de cópia de segurança diária**, defina o período de 90 dias.
    - Para **retenção do ponto de cópia de segurança semanal**, utilize o *segunda-feira* ponto de restauro e manter-52 semanas.
    - Para **retenção do ponto de cópia de segurança mensal**, utilizar o ponto de restauro de Domingo primeiro do mês e retidos durante meses 36.
    - Anular seleção de **retenção do ponto de cópia de segurança anual** opção. Leader de finanças não pretende manter os dados mais de 36 meses.
    - Clique em **OK** para criar a política de cópias de segurança.

    ![Selecionar a carga de trabalho](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    Depois de criar a política de cópia de segurança, associe a política com as máquinas virtuais.

6. No **selecionar máquinas virtuais** caixa de diálogo Selecionar *myVM* e clique em **OK** para implementar a política de cópia de segurança para as máquinas virtuais. 

    Todas as máquinas virtuais que estão na mesma localização e já não estão associados uma política de cópia, são apresentados. *myVMH1* e *myVMR1* selecionados para ser associado a *financeiro* política.

    ![Selecionar a carga de trabalho](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    Quando a implementação estiver concluída, receberá uma notificação dessa implementação concluída com êxito.

## <a name="initial-backup"></a>Cópia de segurança inicial

Ativou a cópia de segurança para os cofres dos serviços de recuperação, mas não tiver sido criada uma cópia de segurança inicial. É uma prática para recuperação de desastre para acionar a primeira cópia de segurança, para que os dados estão protegidos. 

Para executar uma tarefa de cópia de segurança a pedido:

1. No dashboard do cofre, clique em **3** em **itens de cópia de segurança**, para abrir o menu de itens de cópia de segurança.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    O **itens de cópia de segurança** é aberto o menu.

2. No **itens de cópia de segurança** menu, clique em **Máquina Virtual do Azure** para abrir a lista de máquinas virtuais associadas com o cofre.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    É aberta a lista **Itens de Cópia de Segurança**.

    ![Tarefa de cópia de segurança acionada](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Na lista **Itens de Cópia de Segurança**, clique nas reticências **...** para abrir o menu Contexto.

4. No menu de contexto, selecione **cópia de segurança agora**.

    ![Menu Contexto](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Abre o menu de cópia de segurança agora.

5. No menu agora a cópia de segurança, introduza o último dia para manter o ponto de recuperação e clique em **cópia de segurança**.

    ![definir o último dia em que o ponto de recuperação de Criar Cópia de Segurança Agora é mantido](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    As notificações de implementação permitem-lhe saber se a tarefa de cópia de segurança foi acionada e que pode acompanhar o progresso da tarefa na página Tarefas de cópias de segurança. Dependendo do tamanho da máquina virtual, criar a cópia de segurança inicial pode demorar algum tempo.

    Quando a tarefa de cópia de segurança inicial estiver concluída, pode ver o respetivo estado no menu de tarefa de cópia de segurança. A tarefa de cópia de segurança a pedido criado o ponto de restauro inicial para *myVM*. Se pretender criar cópias de segurança outras máquinas virtuais, repita estes passos para cada máquina virtual. 

    ![Mosaico Tarefas de cópia de segurança](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>Limpar recursos

Se pretender continuar com a trabalhar com tutoriais subsequentes, não limpeza até os recursos que criou neste tutorial. Se não pretender continuar, utilize os seguintes passos para eliminar todos os recursos criados por este tutorial no portal do Azure.

1. No **myRecoveryServicesVault** dashboard, clique em **3** em **itens de cópia de segurança**, para abrir o menu de itens de cópia de segurança.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. No **itens de cópia de segurança** menu, clique em **Máquina Virtual do Azure** para abrir a lista de máquinas virtuais associadas com o cofre.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    É aberta a lista **Itens de Cópia de Segurança**.

3. No **itens de cópia de segurança** menu, clique no botão de reticências para abrir o menu de contexto.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. No menu de contexto, selecione **cópia de segurança de paragem** para abrir o menu de parar a cópia de segurança. 

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. No **parar a cópia de segurança** menu, selecione o menu pendente superior e escolha **eliminar dados de cópia de segurança**.

6. No **escreva o nome do item de cópia de segurança** caixa de diálogo, escreva *myVM*.
 
7. Depois do item de cópia de segurança é verificado (aparece uma marca de verificação), **cópia de segurança de paragem** botão está ativado. Clique em **parar a cópia de segurança** para a política de parar e eliminar os pontos de restauro. 

    ![Clique em Parar a cópia de segurança para eliminar o Cofre](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png).

8. No **myRecoveryServicesVault** menu, clique em **eliminar**.

    ![Clique em Parar a cópia de segurança para eliminar o Cofre](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Depois de eliminar o cofre, regressar à lista de cofres dos serviços de recuperação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial que utilizou o portal do Azure para:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Definir o cofre para proteger máquinas virtuais
> * Criar uma política personalizada de cópia de segurança e retenção
> * Atribuir a política para proteger várias máquinas virtuais
> * Acionar uma pedido para o máquinas virtuais

Continue para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco. 

> [!div class="nextstepaction"]
> [Restaurar VMs com a CLI](./tutorial-restore-disk.md)
