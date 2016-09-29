<properties
    pageTitle="Primeira Impressão: proteger VMs do Azure com o cofre de cópia de segurança | Microsoft Azure"
    description="Proteger VMs do Azure com o cofre de Cópia de segurança. O tutorial explica como criar um cofre, registar VMs, criar uma política e proteger as VMs no Azure."
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/15/2016"
    ms.author="markgal; jimpark"/>



# Primeiras impressões: efetuar uma cópia de segurança das máquinas virtuais do Azure

> [AZURE.SELECTOR]
- [Proteger VMs com um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md)
- [Proteger VMs do Azure com um cofre das Cópias de Segurança](backup-azure-vms-first-look.md)

Este tutorial guia-o através dos passos para criar uma cópia de segurança de uma máquina virtual (VM) do Azure para um cofre de cópia de segurança do Azure. Este artigo descreve o modelo de Clássico ou modelo de implementação do Service Manager, para a cópia de segurança das VMs. Se estiver interessado em efetuar uma cópia de segurança de uma VM para um cofre dos Serviços de Recuperação que pertence a um Grupo de Recursos, consulte [Primeira impressão: proteger VMs com um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md). Para concluir com êxito este tutorial, estes pré-requisitos têm de existir:

- Criou uma VM na sua subscrição do Azure.
- A VM tem conetividade aos endereços IP públicos do Azure. Para obter informações adicionais, consulte o artigo [Conetividade de rede](./backup-azure-vms-prepare.md#network-connectivity).

Para efetuar a cópia de segurança de uma VM, existem cinco passos principais:  

![primeiro passo](./media/backup-azure-vms-first-look/step-one.png) Crie um cofre de cópia de segurança ou identifique um cofre de cópia de segurança existente. <br/>
![segundo passo](./media/backup-azure-vms-first-look/step-two.png) Utilize o portal Clássico do Azure para detetar e registar as máquinas virtuais. <br/>
![terceiro passo](./media/backup-azure-vms-first-look/step-three.png) Instale o Agente da VM. <br/>
![quarto passo](./media/backup-azure-vms-first-look/step-four.png) Crie a política para proteger as máquinas virtuais. <br/>
![quinto passo](./media/backup-azure-vms-first-look/step-five.png) Execute a cópia de segurança.

![Vista de nível elevado do processo de cópia de segurança da VM](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

>[AZURE.NOTE] O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../resource-manager-deployment-model.md). Este tutorial é para ser utilizado com as VMs que podem ser criadas no portal Clássico do Azure. O serviço Azure Backup suporta VMs baseadas no Resource Manager. Para obter detalhes sobre a cópia de segurança de VMs para um cofre de serviços de recuperação, consulte [Primeira Impressão: proteger VMs com um cofre de serviços de recuperação](backup-azure-vms-first-look-arm.md).



## Passo 1 - criar um cofre de cópia de segurança para uma VM

Um cofre de cópia de segurança é uma entidade que armazena todas as cópias de segurança e os pontos de recuperação que foram criados ao longo do tempo. O cofre de cópia de segurança também contém as políticas de cópia de segurança que são aplicadas às máquinas virtuais cuja cópia de segurança está a ser criada.

1. Inicie sessão no [Portal Clássico do Azure](http://manage.windowsazure.com/).

2. No canto inferior esquerdo do portal do Azure, clique em **Novo**

    ![Clique no menu Novo](./media/backup-azure-vms-first-look/new-button.png)

3. No assistente de Criação Rápida, clique em **Serviços de Dados** > **Serviços de Recuperação** > **Cofre do Backup** > **Criação Rápida**.

    ![Criar cofre de cópia de segurança](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    O assistente pede-lhe o **Nome** e **Região**. Se administrar mais do que uma subscrição, é apresentada uma caixa de diálogo para escolher a subscrição.

4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure.

5. Em **Localização**, selecione a região geográfica do cofre. O cofre **tem de** estar na mesma região que as máquinas virtuais que protege.

    Se não souber qual é a região na qual existe a VM, feche este assistente e clique em **Virtual Machines** na lista de serviços do Azure. A coluna Localização fornece o nome da região. Se tiver máquinas virtuais em várias regiões, crie um cofre de cópia de segurança em cada região.

6. Se não existir qualquer caixa de diálogo **Subscrição** no assistente, avance para o passo seguinte. Se trabalha com várias subscrições, selecione uma subscrição para associar ao novo cofre de cópia de segurança.

    ![Criar notificação de alerta do cofre](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

7. Clique em **Criar Cofre**. Pode demorar algum tempo até que o cofre de cópia de segurança seja criado. Monitorize as notificações de estado na parte inferior do portal.

    ![Criar notificação de alerta do cofre](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Uma mensagem confirma que o cofre foi criado com êxito. Está listado na página **Serviços de recuperação** como **Ativo**.

    ![Criar notificação de alerta do cofre](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

8. Na lista de cofres na página **Serviços de Recuperação**, selecione o cofre que criou para iniciar a página **Início Rápido**.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look/active-vault-demo.png)

9. Na página **Início Rápido**, clique em **Configurar** para abrir a opção de replicação de armazenamento.
    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look/configure-storage.png)

10. Na opção **replicação de armazenamento**, escolha a opção de replicação para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Por predefinição, o seu cofre tem um armazenamento georredundante. Escolha armazenamento georredundante se for a sua cópia de segurança primária. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento georredundante e localmente redundante na [descrição geral da replicação do Armazenamento do Azure](../storage/storage-redundancy.md).

Após escolher a opção de armazenamento para o cofre, está pronto para associar a VM com o cofre. Para começar a associação, detete e registe as máquinas virtuais do Azure.

## Passo 2 – detetar e registar máquinas virtuais do Azure
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

    >[AZURE.TIP] Podem ser registadas várias máquinas virtuais em simultâneo.

    É criada uma tarefa para cada máquina virtual que selecionou.

9. Clique em **Ver Tarefa** na notificação para aceder à página **Tarefas**.

    ![Tarefa de registo](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também aparece na lista de itens registados, juntamente com o estado da operação de registo.

    ![Estado do registo 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o estado muda para refletir o estado *registado*.

    ![Estado do registo 2](./media/backup-azure-vms/register-status02.png)

## Passo 3 – instalar o Agente da VM na máquina virtual

O Agente da VM do Azure tem de estar instalado na máquina virtual do Azure para que a extensão da Cópia de Segurança funcione. Se a VM foi criada a partir da galeria do Azure, o Agente da VM já se encontra presente na VM. Pode avançar para [proteger as suas VMs](backup-azure-vms-first-look.md#step-4-protect-azure-virtual-machines).

Se a VM migrou a partir de um datacenter no local, a VM, provavelmente, não tem o Agente da VM instalado. Tem de instalar o Agente da VM na máquina virtual antes de prosseguir para a proteção da VM. Para obter passos detalhados sobre como instalar o Agente da VM, consulte a [secção Agente da VM do artigo VMs de Cópia de Segurança](backup-azure-vms-prepare.md#vm-agent).


## Passo 4 - criar a política de cópia de segurança
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

    >[AZURE.NOTE] Uma política de cópia de segurança inclui um esquema de retenção para as cópias de segurança agendadas. Se selecionar uma política de cópia de segurança existente, deixará de poder modificar as opções de retenção no próximo passo.

6. No **Período de Retenção**, defina o âmbito diário, semanal, mensal e anual para os pontos de cópia de segurança específicos.

    ![É efetuada a cópia de segurança da máquina virtual com o ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    A política de retenção especifica o período de tempo para armazenar uma cópia de segurança. Pode especificar diferentes políticas de retenção com base na data em que a cópia de segurança é efetuada.

7. Clique em **Tarefas** para ver a lista de tarefas **Configurar Proteção**.

    ![Tarefa Configurar proteção](./media/backup-azure-vms/protect-configureprotection.png)

    Agora que estabeleceu a política, vá para o passo seguinte e execute a cópia de segurança inicial.

## Passo 5 - cópia de segurança inicial

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

    >[AZURE.NOTE] A cópia de segurança das máquinas virtuais é um processo local. Não é possível efetuar uma cópia de segurança de máquinas virtuais de uma região para um cofre de cópia de segurança noutra região. Por isso, para cada região do Azure com VMs cuja cópia de segurança seja necessária, tem de ser criado, pelo menos, um cofre de cópia de segurança nessa região.

## Passos seguintes
Agora que efetuou com êxito a cópia de segurança de uma VM, existem vários passos seguintes que podem ser úteis. O passo mais lógico é familiarizar-se com o restauro de dados para uma VM. No entanto, existem tarefas de gestão que o irão ajudar a compreender como manter os seus dados seguros e minimizar os custos.

- [Gerir e monitorizar as máquinas virtuais](backup-azure-manage-vms.md)
- [Monitorizar máquinas virtuais](backup-azure-restore-vms.md)
- [Orientação na resolução de problemas](backup-azure-vms-troubleshoot.md)


## Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).



<!--HONumber=Sep16_HO3-->


