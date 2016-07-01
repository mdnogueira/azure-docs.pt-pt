<properties
   pageTitle="Saiba como efetuar cópias de segurança de ficheiros e pastas a partir do Windows para o Azure com o Backup do Azure utilizando o modelo de implementação do Resource Manager | Microsoft Azure"
   description="Saiba como criar cópias de segurança dos dados do Windows Server ao criar um cofre, instalar o agente dos Serviços de Recuperação e efetuar uma cópia de segurança dos ficheiros e das pastas para o Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="how to backup; how to back up"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="05/10/2016"
   ms.author="jimpark;"/>

# Primeiras impressões: efetuar cópias de segurança de ficheiros e pastas a partir do Windows Server ou do cliente Windows para o Azure com oBackup do Azure utilizando o modelo de implementação do Resource Manager

Este artigo explica como criar uma cópia de segurança dos ficheiros e pastas do Windows Server (ou do cliente Windows) para o Azure com o Backup do Azure através do Resource Manager. É um tutorial que se destina a explicar as noções básicas. Se pretender começar a utilizar o Backup do Azure, está no sítio certo.

Se pretender saber mais sobre o Backup do Azure, leia esta [descrição geral](backup-introduction-to-azure-backup.md).

A cópia de segurança de ficheiros e pastas para o Azure requer estas atividades:

![Passo 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) obtenha uma subscrição do Azure (se ainda não tiver uma).<br>
![Passo 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) crie um cofre dos Serviços de Recuperação.<br>
![Passo 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) transfira os ficheiros necessários.<br>
![Passo 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) instale e registe o agente dos Serviços de Recuperação.<br>
![Passo 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) efetue uma cópia de segurança de ficheiros e pastas.

![Como efetuar uma cópia de segurança da máquina Windows com o Backup do Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## Passo 1: obtenha uma subscrição do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que lhe permite aceder a qualquer serviço do Azure.

## Passo 2: crie um cofre dos Serviços de Recuperação

Para efetuar uma cópia de segurança de ficheiros e pastas, tem de criar um cofre dos Serviços de Recuperação na região onde pretende armazenar os dados. Também precisa de determinar como pretende que o seu armazenamento seja replicado.

### Para criar um cofre dos Serviços de Recuperação

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/) através da sua subscrição do Azure.

2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação** e clique em **cofres dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>

3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 5 da Criação do cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre.

5. Clique em **Subscrição** para ver a lista de subscrições disponíveis.

6. Clique em **Grupo de recursos** para ver a lista de Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos.

7. Clique em **Localização** para selecionar a região geográfica do cofre. Esta escolha determina a região geográfica para onde os dados da cópia de segurança são enviados.

8. Clique em **Criar**.

    Se não vir o Cofre listado após ter sido concluído, clique em **Atualizar**. Quando atualiza a lista, clique no nome do cofre.

### Para determinar a redundância de armazenamento
Quando cria pela primeira vez um cofre dos Serviços de Recuperação, determina como o armazenamento é replicado.

1. Clique no novo cofre para abrir o dashboard.

2. No painel **Definições**, que abre automaticamente com o dashboard do cofre, clique em **Infraestrutura de Cópia de Segurança**.

3. No painel Infraestrutura de Cópia de Segurança, clique em **Configuração de Cópia de Segurança** para ver o **Tipo de replicação de armazenamento**.

    ![Passo 5 da Criação do cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)

4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![Listar os cofres dos serviços de recuperação](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Por predefinição, o seu cofre tem um armazenamento georredundante. Se estiver a utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o armazenamento georredundante. Se estiver a utilizar o Azure como um ponto final de armazenamento de cópia de segurança não primário, escolha o armazenamento localmente redundante, que irá reduzir o custo do armazenamento de dados no Azure. Leia mais sobre as opções de armazenamento [georredundante](../storage/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/storage-redundancy.md#locally-redundant-storage) nesta [descrição geral](../storage/storage-redundancy.md).

Agora que criou um cofre, prepare a infraestrutura para efetuar uma cópia de segurança de ficheiros e pastas ao transferir as credenciais do cofre e o agente dos Serviços de Recuperação do Microsoft Azure.

## Passo 3 – Transferir ficheiros

1. Clique em **Definições** no dashboard do cofre dos Serviços de Recuperação.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/settings-button.png)

2. Clique em **Introdução > Cópia de segurança** no painel Definições.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)

3. Clique em **Objetivo de cópia de segurança** no painel Backup.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)

4. Selecione **No local** no menu Onde está a carga de trabalho em execução?.

5. Selecione **Ficheiros e pastas** no menu Pretende efetuar uma cópia de segurança de que itens? e clique em **OK**.

### Transferir o agente dos Serviços de Recuperação

1. Clique em **Transferir Agente para o Windows Server ou um Cliente Windows** no painel **Preparar infraestrutura**.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)

2. Clique em **Guardar** no pop-up de transferência. Por predefinição, o ficheiro **MARSagentinstaller.exe** é guardado na pasta Transferências.

### Transferir as credenciais do cofre

1. Clique em **Transferir > Guardar** no painel Preparar infraestrutura.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## Passo 4 – instalar e registar o agente

>[AZURE.NOTE] A permissão da cópia de segurança através do portal do Azure estará disponível brevemente. Neste momento, pode utilizar o Agente dos Serviços de Recuperação do Microsoft Azure no local para efetuar uma cópia de segurança dos ficheiros e pastas.

1. Localize e faça duplo clique em **MARSagentinstaller.exe** na pasta Transferências (ou noutra localização guardada).

2. Conclua o Assistente de Configuração do Agente dos Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, tem de:

    - Selecionar uma localização para a pasta cache e da instalação.
    - Fornecer as informações do servidor proxy se utilizar um servidor proxy para estabelecer ligação à Internet.
    - Forneça os detalhes do seu nome de utilizador e palavra-passe se utilizar um proxy autenticado.
    - Fornecer as credenciais do cofre transferidas
    - Guarde a frase de acesso de encriptação numa localização segura.

    >[AZURE.NOTE] Se perder ou se esquecer da frase de acesso, a Microsoft não o pode ajudar a recuperar os dados de cópia de segurança. Guarde o ficheiro numa localização segura. É necessário para restaurar uma cópia de segurança.

O agente está agora instalado e a máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## Passo 5: efetue uma cópia de segurança de ficheiros e pastas

A cópia de segurança inicial inclui duas tarefas principais:

- Agendar a cópia de segurança
- Efetuar a cópia de segurança de ficheiros e pastas pela primeira vez

Para concluir a cópia de segurança inicial, pode utilizar o agente dos Serviços de Recuperação do Microsoft Azure.

### Para agendar a cópia de segurança

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Iniciar o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. No agente dos Serviços de Recuperação, clique em **Agendar Cópia de Segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página Introdução do Assistente para Agendar Cópia de Segurança, clique em **Seguinte**.

4. Na página Selecionar Itens para Cópia de Segurança, clique em **Adicionar Itens**.

5. Selecione os ficheiros e pastas para a cópia de segurança e, em seguida, clique em **OK**.

6. Clique em **Seguinte**.

7. N página **Especificar Agenda de Cópia de Segurança**, especifique a **agenda da cópia de segurança** e clique em **Seguinte**.

    Pode agendar cópias de segurança diárias (no máximo, três vezes por dia) ou semanais.

    ![Itens para Cópia de Segurança do Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Para obter mais informações sobre como especificar a agenda da cópia de segurança, consulte o artigo [Utilizar o Backup do Azure para substituir a infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).

8. Na página **Selecionar Política de Retenção** página, selecione a **Política de Retenção** para a cópia de segurança.

    A política de retenção especifica o período durante o qual a cópia de segurança será armazenada. Em vez de apenas especificar uma "política simples" para todos os pontos de cópia de segurança, pode especificar políticas de retenção diferentes com base em quando ocorre a cópia de segurança. Pode modificar as políticas de retenção diárias, semanais, mensais e anuais para corresponder às suas necessidades.

9. Na página Escolher Tipo de Cópia de Segurança Inicial, escolha o tipo de cópia de segurança inicial. Deixe a opção **Automaticamente através da rede** selecionada e, em seguida, clique em **Seguinte**.

    Poderá fazer cópias de segurança automaticamente através da rede ou pode efetuar cópias de segurança offline. O resto deste artigo descreve o processo para efetuar uma cópia de segurança automaticamente. Se preferir efetuar uma cópia de segurança offline, consulte o artigo [Fluxo de trabalho da cópia de segurança offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.

10. Na página de Confirmação, reveja as informações e, em seguida, clique em **Concluir**.

11. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### Para efetuar a cópia de segurança de ficheiros e pastas pela primeira vez

1. No agente dos Serviços de Recuperação, clique em **Efetuar Cópia de Segurança Agora** para concluir a propagação inicial através da rede.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

2. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, clique em **Efetuar Cópia de Segurança**.

3. Clique em **Fechar** para fechar o assistente. Se o fizer antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

![IV concluídos](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## Passos seguintes
- Obtenha mais detalhes sobre como [efetuar a cópia de segurança das máquinas Windows](backup-configure-vault.md).
- Agora que criou uma segurança dos seus ficheiros e pastas, pode [gerir os cofres e os servidores](backup-azure-manage-windows-server.md).
- Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).



<!--HONumber=Jun16_HO2-->


