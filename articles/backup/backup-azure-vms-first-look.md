---
title: "Primeira Impressão: Fazer uma cópia de segurança de VMs do Azure com um cofre de cópia de segurança | Microsoft Docs"
description: "Utilize o portal clássico para fazer uma cópia de segurança de VMs do Azure para um cofre de Cópia de Segurança. Este tutorial explica todas as fases, incluindo a criação do cofre de Cópia de Segurança, o registo das VMs, a criação da política de cópias de segurança e a execução da tarefa de cópia de segurança inicial."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 722820dc-b65f-425c-a9e5-c1946e896a87
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 3/10/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 8883ff1601c521d05068452b1b58cadaee1a941f
ms.lasthandoff: 03/14/2017


---
# <a name="first-look-backing-up-azure-virtual-machines"></a>Primeiras impressões: efetuar uma cópia de segurança das máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Proteger VMs com um cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md)
> * [Proteger VMs do Azure com um cofre das Cópias de Segurança](backup-azure-vms-first-look.md)
>
>

Este tutorial guia-o através dos passos para criar uma cópia de segurança de uma máquina virtual (VM) do Azure para um cofre de cópia de segurança do Azure. Este artigo descreve o modelo clássico ou o modelo de implementação do Service Manager para fazer uma cópia de segurança das VMs. Os passos seguintes aplicam-se apenas aos cofres de Cópia de Segurança criados no portal clássico. A Microsoft recomenda a utilização do modelo Resource Manager para as novas implementações.

Se estiver interessado em efetuar uma cópia de segurança de uma VM para um cofre dos Serviços de Recuperação que pertence a um Grupo de Recursos, consulte [Primeira impressão: proteger VMs com um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md).

Para concluir com êxito o tutorial seguinte, estes pré-requisitos têm de existir:

* Criou uma VM na sua subscrição do Azure.
* A VM tem conetividade aos endereços IP públicos do Azure. Para obter informações adicionais, consulte o artigo [Conetividade de rede](backup-azure-vms-prepare.md#network-connectivity).


> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este tutorial é para ser utilizado com as máquinas virtuais criadas no portal clássico.
>
>

## <a name="create-a-backup-vault"></a>Criar um cofre de cópia de segurança
Um cofre de cópia de segurança é uma entidade que armazena todas as cópias de segurança e os pontos de recuperação que foram criados ao longo do tempo. O cofre de cópia de segurança também contém as políticas de cópia de segurança que são aplicadas às máquinas virtuais cuja cópia de segurança está a ser criada.

> [!IMPORTANT]
> A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança. Os cofres de Cópia de Segurança existentes continuam a ser suportados e é possível [utilizar o Azure PowerShell para criar cofres de Cópia de Segurança](./backup-client-automation-classic.md#create-a-backup-vault). No entanto, a Microsoft recomenda a criação de cofres de Serviços de Recuperação para todas as implementações, uma vez que os melhoramentos futuros se aplicam apenas a cofres de Serviços de Recuperação.



## <a name="discover-and-register-azure-virtual-machines"></a>Detetar e registar máquinas virtuais do Azure
Antes de registar a VM com um cofre, execute o processo de deteção para identificar VMs novas. Deste modo, devolve uma lista de máquinas virtuais na subscrição, juntamente com informações adicionais, como a região e o nome do serviço em nuvem.

1. Inicie sessão no [Portal Clássico do Azure](http://manage.windowsazure.com/)
2. No portal clássico do Azure, clique em **Serviços de Recuperação** para abrir uma lista de cofres dos Serviços de Recuperação.
    ![Selecionar a carga de trabalho](./media/backup-azure-vms-first-look/recovery-services-icon.png)
3. Na lista de cofres, selecione o cofre para a cópia de segurança da VM.

    Quando seleciona o cofre, é aberto na página **Início Rápido**
4. No menu do cofre, clique em **Itens Registados**.

    ![Selecionar a carga de trabalho](./media/backup-azure-vms-first-look/configure-registered-items.png)
5. No menu **Tipo**, selecione **Máquina Virtual do Azure**.

    ![Selecionar a carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)
6. Clique em **DETETAR** na parte inferior da página.
    ![Botão Detetar](./media/backup-azure-vms/discover-button-only.png)

    O processo de deteção pode demorar alguns minutos enquanto as máquinas virtuais estão a ser apresentadas. Existe uma notificação na parte inferior do ecrã que lhe permite saber que o processo está em execução.

    ![Detetar VMs](./media/backup-azure-vms/discovering-vms.png)

    A notificação muda quando o processo estiver concluído.

    ![Deteção concluída](./media/backup-azure-vms-first-look/discovery-complete.png)
7. Clique em **REGISTAR** na parte inferior da página.
    ![Botão de Registo](./media/backup-azure-vms-first-look/register-icon.png)
8. No menu de atalho **Registar Itens**, selecione as máquinas virtuais que pretender registar.

   > [!TIP]
   > Podem ser registadas várias máquinas virtuais em simultâneo.
   >
   >

    É criada uma tarefa para cada máquina virtual que selecionou.
9. Clique em **Ver Tarefa** na notificação para aceder à página **Tarefas**.

    ![Tarefa de registo](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também aparece na lista de itens registados, juntamente com o estado da operação de registo.

    ![Estado do registo 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o estado muda para refletir o estado *registado*.

    ![Estado do registo 2](./media/backup-azure-vms/register-status02.png)

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalar o Agente da VM na máquina virtual
O Agente da VM do Azure tem de estar instalado na máquina virtual do Azure para que a extensão da Cópia de Segurança funcione. Se a VM foi criada a partir da galeria do Azure, o Agente da VM já se encontra presente na VM; pode avançar para [proteger as suas VMs](backup-azure-vms-first-look.md#create-the-backup-policy).

Se a VM migrou a partir de um datacenter no local, a VM, provavelmente, não tem o Agente da VM instalado. Tem de instalar o Agente da VM na máquina virtual antes de prosseguir para a proteção da VM. Para obter passos detalhados sobre como instalar o Agente da VM, consulte a [secção Agente da VM do artigo VMs de Cópia de Segurança](backup-azure-vms-prepare.md#vm-agent).

## <a name="create-the-backup-policy"></a>Criar a política de cópias de segurança
Antes de acionar a tarefa de cópia de segurança inicial, defina a agenda quando forem tirados instantâneos da cópia de segurança. A agenda quando são tirados instantâneos de cópia de segurança e o período de tempo no qual estes instantâneos são mantidos, são a política de cópia de segurança. As informações de retenção baseiam-se no esquema de rotação de cópia de segurança Avô-pai-filho.

1. Navegue para o cofre de cópia de segurança em **Serviços de Recuperação** no portal Clássico do Azure e clique em **Itens Registados**.
2. Selecione **Máquina Virtual do Azure** no menu pendente.

    ![Selecionar a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)
3. Clique em **PROTEGER** na parte inferior da página.
    ![Clique em Proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    O **assistente Proteger Itens** aparece e lista *apenas* as máquinas virtuais que estão registadas e não protegidas.

    ![Configurar a proteção à escala](./media/backup-azure-vms/protect-at-scale.png)
4. Selecione as máquinas virtuais que pretende proteger.

    Se existirem duas ou mais máquinas virtuais com o mesmo nome, utilize o Serviço em Nuvem para distinguir entre as máquinas virtuais.
5. No menu **Configurar a proteção**, selecione uma política existente ou crie uma nova política para proteger as máquinas virtuais que identificou.

    Os novos cofres de Cópia de Segurança têm uma política predefinida associada ao cofre. Esta política tira um instantâneo diário todas as noites e o instantâneo diário é mantido durante 30 dias. Cada política de cópia de segurança pode ter várias máquinas virtuais associadas à mesma. No entanto, a máquina virtual só pode ser associada a uma política de cada vez.

    ![Proteger com a nova política](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > Uma política de cópia de segurança inclui um esquema de retenção para as cópias de segurança agendadas. Se selecionar uma política de cópia de segurança existente, deixará de poder modificar as opções de retenção no próximo passo.
   >
   >
6. No **Período de Retenção**, defina o âmbito diário, semanal, mensal e anual para os pontos de cópia de segurança específicos.

    ![É efetuada a cópia de segurança da máquina virtual com o ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    A política de retenção especifica o período de tempo para armazenar uma cópia de segurança. Pode especificar diferentes políticas de retenção com base na data em que a cópia de segurança é efetuada.
7. Clique em **Tarefas** para ver a lista de tarefas **Configurar Proteção**.

    ![Tarefa Configurar proteção](./media/backup-azure-vms/protect-configureprotection.png)

    Agora que estabeleceu a política, vá para o passo seguinte e execute a cópia de segurança inicial.

## <a name="initial-backup"></a>Cópia de segurança inicial
Após uma máquina virtual ter sido protegida com uma política, pode ver essa relação no separador **Itens Protegidos**. Enquanto a cópia de segurança inicial não ocorrer, o **Estado de Proteção** é **Protegido - (cópia de segurança inicial pendente)**. Por predefinição, a primeira cópia de segurança agendada é a *cópia de segurança inicial*.

![Cópia de segurança pendente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para iniciar a cópia de segurança inicial agora:

1. Na página **Itens Protegidos**, clique em **Efetuar Cópia de Segurança Agora** na parte inferior da página.
    ![Ícone de Efetuar Cópia de Segurança Agora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    O serviço de Backup do Azure cria uma tarefa de cópia de segurança para a operação de cópia de segurança inicial.
2. Clique no separador **Tarefas** para ver a lista de tarefas.

    ![Cópia de segurança em curso](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Quando a cópia de segurança inicial estiver concluída, o estado da máquina virtual no separador **Itens Protegidos** é *Protegido*.

    ![É efetuada a cópia de segurança da máquina virtual com o ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)

   > [!NOTE]
   > A cópia de segurança das máquinas virtuais é um processo local. Não é possível efetuar uma cópia de segurança de máquinas virtuais de uma região para um cofre de cópia de segurança noutra região. Por isso, para cada região do Azure com VMs cuja cópia de segurança seja necessária, tem de ser criado, pelo menos, um cofre de cópia de segurança nessa região.
   >
   >

## <a name="next-steps"></a>Passos seguintes
Agora que efetuou com êxito a cópia de segurança de uma VM, existem vários passos seguintes que podem ser úteis. O passo mais lógico é familiarizar-se com o restauro de dados para uma VM. No entanto, existem tarefas de gestão que o irão ajudar a compreender como manter os seus dados seguros e minimizar os custos.

* [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md)
* [Monitorizar máquinas virtuais](backup-azure-restore-vms.md)
* [Orientação na resolução de problemas](backup-azure-vms-troubleshoot.md)

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

