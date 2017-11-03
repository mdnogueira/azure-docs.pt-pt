---
title: "Cópia de segurança VMs do Azure | Microsoft Docs"
description: "Detetar, registar e cópia de segurança de máquinas virtuais do Azure para um cofre dos serviços de recuperação."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "cópia de segurança da máquina virtual; fazer uma cópia de segurança de máquina virtual; cópia de segurança e recuperação após desastre; cópia de segurança de vm de Arm"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40983a3de104238d09b976b5fcf2419da42c1bba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Fazer cópias de segurança de máquinas virtuais do Azure para um cofre dos Serviços de Recuperação
> [!div class="op_single_selector"]
> * [Fazer cópias de segurança de VMs para o Cofre dos serviços de recuperação](backup-azure-arm-vms.md)
> * [Cópia de segurança de VMs para o Cofre de cópia de segurança](backup-azure-vms.md)
>
>

Este artigo fornece detalhes sobre como fazer cópias de segurança de VMs do Azure (implementadas no Resource Manager e clássico implementadas) para um cofre dos serviços de recuperação. A maioria do trabalho para fazer uma cópia de segurança de VMs é a preparação. Antes de poder efetuar cópias de segurança ou proteger uma VM, tem de concluir o [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger as suas VMs. Depois de concluir os pré-requisitos, pode iniciar a operação de cópia de segurança para criar instantâneos de VM.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Para obter mais informações, consulte os artigos no [planeamento da sua infraestrutura de cópia de segurança de VM no Azure](backup-azure-vms-introduction.md) e [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Acionar a tarefa de cópia de segurança
A política de cópia de segurança associada ao Cofre de serviços de recuperação define a frequência e quando a operação de cópia de segurança é executada. Por predefinição, a primeira cópia de segurança agendada é a cópia de segurança inicial. Enquanto a cópia de segurança inicial não ocorrer, o Estado da Última Cópia de Segurança no painel **Tarefas de Cópia de Segurança** é apresentado como **Aviso (cópia de segurança inicial pendente)**.

![Cópia de segurança pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que a cópia de segurança inicial esteja prestes a ser executada, é recomendado que execute **Fazer Cópia de Segurança Agora**. O procedimento seguinte começa a partir do dashboard do cofre. Este procedimento serve para executar a tarefa de cópia de segurança inicial depois de concluir todos os pré-requisitos. Se já tiver sido executada a tarefa de cópia de segurança inicial, este procedimento não está disponível. A política de cópia de segurança associadas determina a próxima tarefa de cópia de segurança.  

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

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros
Caso se depare com problemas ao efetuar uma cópia de segurança da máquina virtual, consulte o [artigo de resolução de problemas de VM](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Passos seguintes
Agora que protegeram a VM, consulte os artigos seguintes para saber mais sobre as tarefas de gestão de VM e como restaurar VMs.

* [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md)
* [Monitorizar máquinas virtuais](backup-azure-arm-restore-vms.md)
