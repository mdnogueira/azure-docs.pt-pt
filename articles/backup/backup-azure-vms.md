---
title: "Cópia de segurança implementadas clássico virtual machines do Azure para um cofre de cópia de segurança | Microsoft Docs"
description: "Detetar, registar e cópia de segurança de máquinas virtuais com estes procedimentos para cópia de segurança da máquina virtual do Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "cópia de segurança da máquina virtual; fazer uma cópia de segurança de máquina virtual; cópia de segurança e recuperação após desastre; cópia de segurança de VM"
ms.assetid: c0ab5469-65fd-4af5-ae9b-f5d183f82ce8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: e1da8bce96078a43c656f84005cefc8bbe81c9e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-classic-portal"></a>Cópia de segurança de máquinas virtuais do Azure (portal clássico)
> [!div class="op_single_selector"]
> * [Fazer cópias de segurança de VMs para o Cofre dos serviços de recuperação](backup-azure-arm-vms.md)
> * [Cópia de segurança de VMs para o Cofre de cópia de segurança](backup-azure-vms.md)
>
>

Este artigo fornece os procedimentos para criar cópias de segurança implementadas clássico do Azure máquina virtual (VM) para um cofre de cópia de segurança. Existem algumas tarefas, que terá de asseguramos antes que pode fazer uma cópia de segurança de uma máquina virtual do Azure. Se ainda não o fez, conclua o [pré-requisitos](backup-azure-vms-prepare.md) para preparar o ambiente para fazer cópias de segurança as suas VMs.

Para obter mais informações, consulte os artigos no [planeamento da sua infraestrutura de cópia de segurança de VM no Azure](backup-azure-vms-introduction.md) e [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Um cofre de cópia de segurança só pode proteger as VMs implementadas clássico. Não é possível proteger as VMs implementadas no Resource Manager com um cofre de cópia de segurança. Consulte [cópia de segurança VMs para o Cofre dos serviços de recuperação](backup-azure-arm-vms.md) para obter detalhes sobre como trabalhar com os serviços de recuperação cofres dos.
>
>

Cópia de segurança de máquinas virtuais do Azure envolve três passos principais:

![Três passos necessários para efetuar cópias de segurança de uma VM do IaaS do Azure](./media/backup-azure-vms/3-steps-for-backup.png)

> [!NOTE]
> A cópia de segurança das máquinas virtuais é um processo local. Não é possível criar cópias de segurança máquinas virtuais numa região para um cofre de cópia de segurança noutra região. Portanto, tem de criar um cofre de cópia de segurança em cada região do Azure, onde existem VMs que serão efetuadas cópias de segurança.
>
> [!IMPORTANT]
> A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança.
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 15 de outubro de 2017, não pode utilizar o PowerShell para criar cofres de Cópia de segurança. **Até 1 de novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>

## <a name="step-1---discover-azure-virtual-machines"></a>Passo 1 – detetar máquinas virtuais do Azure
Para garantir que as novas máquinas de virtuais (VMs) adicionadas à subscrição são identificadas antes de registar, execute o processo de deteção. O processo consulta o Azure para obter a lista de máquinas virtuais na subscrição, juntamente com informações adicionais, tais como a região e o nome do serviço em nuvem.

1. Iniciar sessão para o [portal clássico](http://manage.windowsazure.com/)
2. Na lista de serviços do Azure, clique em **dos serviços de recuperação** para abrir a lista de cofres de cópia de segurança e recuperação de sites.
    ![Lista de cofre aberta](./media/backup-azure-vms/choose-vault-list.png)
3. Na lista de cofres de cópia de segurança, selecione o Cofre de cópia de segurança de uma VM.

    Se se tratar de um novo cofre o portal abre o **início rápido** página.

    ![Abra o menu de itens de registada](./media/backup-azure-vms/vault-quick-start.png)

    Se o Cofre tenha sido configurado anteriormente, o portal abre-se ao menu utilizado recentemente.
4. No menu cofre (na parte superior da página), clique em **itens registados**.

    ![Abra o menu de itens de registada](./media/backup-azure-vms/vault-menu.png)
5. No menu **Tipo**, selecione **Máquina Virtual do Azure**.

    ![Selecionar a carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)
6. Clique em **DETETAR** na parte inferior da página.
    ![Botão Detetar](./media/backup-azure-vms/discover-button-only.png)

    O processo de deteção pode demorar alguns minutos enquanto as máquinas virtuais estão a ser apresentadas. Existe uma notificação na parte inferior do ecrã que lhe permite saber que o processo está em execução.

    ![Detetar VMs](./media/backup-azure-vms/discovering-vms.png)

    A notificação muda quando o processo estiver concluído. Se o processo de deteção não foi possível localizar as máquinas virtuais, certifique-se primeiro que as VMs existem. Se as VMs, certifique-se as VMs na mesma região que o Cofre de cópia de segurança. Se as VMs existem e estão na mesma região, certifique-se de que as VMs já não estão registadas para um cofre de cópia de segurança. Se uma VM for atribuída a um cofre de cópia de segurança não está disponível para ser atribuídas a outros cofres de cópia de segurança.

    ![Deteção concluída](./media/backup-azure-vms/discovery-complete.png)

    Depois de ter detetado os novos itens, vá para o passo 2 e registe as suas VMs.

## <a name="step-2---register-azure-virtual-machines"></a>Passo 2 - registar máquinas virtuais do Azure
Registar uma máquina virtual do Azure para o associar o serviço de cópia de segurança do Azure. Isto é normalmente uma única atividade.

1. Navegue para o Cofre de cópia de segurança em **dos serviços de recuperação** no portal do Azure e, em seguida, clique em **itens registados**.
2. Selecione **Máquina Virtual do Azure** no menu pendente.

    ![Selecionar a carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)
3. Clique em **REGISTAR** na parte inferior da página.
    ![Botão de Registo](./media/backup-azure-vms/register-button-only.png)
4. No menu de atalho **Registar Itens**, selecione as máquinas virtuais que pretender registar. Se existirem dois ou mais máquinas virtuais com o mesmo nome, utilize o serviço em nuvem para distinguir entre eles.

   > [!TIP]
   > Podem ser registadas várias máquinas virtuais em simultâneo.
   >
   >

    É criada uma tarefa para cada máquina virtual que selecionou.
5. Clique em **Ver Tarefa** na notificação para aceder à página **Tarefas**.

    ![Tarefa de registo](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também aparece na lista de itens registados, juntamente com o estado da operação de registo.

    ![Estado do registo 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o estado muda para refletir o estado *registado*.

    ![Estado do registo 2](./media/backup-azure-vms/register-status02.png)

## <a name="step-3---protect-azure-virtual-machines"></a>Passo 3 – proteger máquinas virtuais do Azure
Agora pode configurar uma política de cópia de segurança e retenção para a máquina virtual. Várias máquinas virtuais podem ser protegidas utilizando uma única ação de proteger.

Cofres de cópia de segurança do Azure criadas após Maio de 2015 vêm com uma política predefinida incorporada no cofre. Esta política predefinida é fornecido com uma retenção predefinido de 30 dias e uma agenda de cópia de segurança diária de uma vez.

1. Navegue para o Cofre de cópia de segurança em **dos serviços de recuperação** no portal do Azure e, em seguida, clique em **itens registados**.
2. Selecione **Máquina Virtual do Azure** no menu pendente.

    ![Selecionar a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)
3. Clique em **PROTEGER** na parte inferior da página.

    O **assistente proteger itens** aparece. O assistente só apresenta as máquinas virtuais que são registadas e não protegidas. Selecione as máquinas virtuais que pretende proteger.

    Se existirem dois ou mais máquinas virtuais com o mesmo nome, utilize o serviço em nuvem para distinguir entre as máquinas virtuais.

   > [!TIP]
   > Pode proteger várias máquinas virtuais em simultâneo.
   >
   >

    ![Configurar a proteção à escala](./media/backup-azure-vms/protect-at-scale.png)

4. Escolha um **agenda de cópia de segurança** fazer cópias de segurança de máquinas virtuais que selecionou. Pode escolher a partir de um conjunto existente de políticas ou definir um novo.

    Cada política de cópia de segurança pode ter várias máquinas virtuais associadas à mesma. No entanto, a máquina virtual pode apenas ser associada com uma política a qualquer momento de determinado no tempo.

    ![Proteger com a nova política](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > Uma política de cópia de segurança inclui um esquema de retenção para as cópias de segurança agendadas. Se selecionar uma política de cópia de segurança existente, não é possível modificar as opções de retenção no próximo passo.
   >
   >

5. Escolha um **período de retenção** para associar as cópias de segurança.

    ![Proteger com retenção flexível](./media/backup-azure-vms/policy-retention.png)

    A política de retenção especifica o período de tempo para armazenar uma cópia de segurança. Pode especificar diferentes políticas de retenção com base na data em que a cópia de segurança é efetuada. Por exemplo, um ponto de cópia de segurança efetuado diariamente (o que funciona como um ponto de recuperação operacional) pode ser mantido durante 90 dias. Em comparação, um ponto de cópia de segurança efetuado no final de cada trimestre (para fins de auditoria) poderá ter de ser preservados para o número de meses ou anos.

    ![É efetuada a cópia de segurança da máquina virtual com o ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    Nesta imagem de exemplo:

   * **Política de retenção diárias**: cópias de segurança criadas diariamente são armazenadas durante 30 dias.
   * **Política de retenção semanais**: cópias de segurança criadas todas as semanas no Domingo são preservadas para semanas 104.
   * **Política de retenção mensais**: cópias de segurança criadas na última Domingo de cada mês são preservadas durante meses 120.
   * **Política de retenção anuais**: cópias de segurança criadas no primeiro Domingo dessa cada Janeiro são preservadas para 99 anos.

     É criada uma tarefa para configurar a política de proteção e associar as máquinas virtuais para essa política para cada máquina virtual que selecionou.
6. Para ver a lista de **configurar proteção** tarefas, no menu de cofres, clique em **tarefas** e selecione **configurar proteção** do **operação**filtro.

    ![Tarefa Configurar proteção](./media/backup-azure-vms/protect-configureprotection.png)

## <a name="initial-backup"></a>Cópia de segurança inicial
Assim que a máquina virtual está protegida com uma política, aparece sob o **itens protegidos** separador com o estado *protegido - (pendentes de cópia de segurança inicial)*. Por predefinição, a primeira cópia de segurança agendada é a *cópia de segurança inicial*.

Para acionar a cópia de segurança inicial imediatamente após a configuração de proteção:

1. Na parte inferior do **itens protegidos** página, clique em **cópia de segurança agora**.

    O serviço de Backup do Azure cria uma tarefa de cópia de segurança para a operação de cópia de segurança inicial.
2. Clique no separador **Tarefas** para ver a lista de tarefas.

    ![Cópia de segurança em curso](./media/backup-azure-vms/protect-inprogress.png)

> [!NOTE]
> Durante a operação de cópia de segurança, o serviço de cópia de segurança do Azure emite um comando para a extensão de cópia de segurança em cada máquina virtual para esvaziar todas as tarefas de escrita e tirar um instantâneo consistente.
>
>

Quando a cópia de segurança inicial estiver concluído, o estado da máquina virtual no **itens protegidos** separador é *protegidos*.

![É efetuada a cópia de segurança da máquina virtual com o ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)

## <a name="viewing-backup-status-and-details"></a>Visualizar o estado de cópia de segurança e os detalhes
Depois de protegido, também aumenta a contagem de máquinas virtuais no **Dashboard** página de resumo. O **Dashboard** página também mostra o número de tarefas das últimas 24 horas que foram *bem-sucedida*, ter *falha*e são *em curso* . No **tarefas** página, utilize o **estado**, **operação**, ou **de** e **para** menus para filtrar as tarefas.

![Estado da cópia de segurança na página do Dashboard](./media/backup-azure-vms/dashboard-protectedvms.png)

Os valores no dashboard são atualizados a cada 24 horas.

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros
Caso se depare com problemas ao efetuar uma cópia de segurança da máquina virtual, observe o [artigo de resolução de problemas de VM](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Passos seguintes
* [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md)
* [Monitorizar máquinas virtuais](backup-azure-restore-vms.md)
