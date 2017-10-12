---
title: "Realize cópias de segurança de ficheiros e pastas do Windows para o Azure (Resource Manager) | Microsoft Docs"
description: "Aprenda a realizar cópias de segurança de ficheiros do Windows e pastas para o Azure numa implementação do Gestor de Recursos."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "como realizar uma cópia de segurança; como realizar uma cópia de segurança; cópia de segurança de ficheiros e pastas"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 8/15/2017
ms.author: markgal;
ms.openlocfilehash: 7a016ed92c68ce624aeb09d766adbc6fc8ba2b42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="first-look-back-up-files-and-folders-in-resource-manager-deployment"></a>Primeiras impressões: efetuar cópias de segurança de ficheiros e pastas na implementação do Resource Manager
Este artigo explica como criar uma cópia de segurança dos ficheiros e pastas do Windows Server (ou computador Windows) para o Azure através de uma implementação do Resource Manager. É um tutorial que se destina a explicar as noções básicas. Se pretender começar a utilizar o Backup do Azure, está no sítio certo.

Se pretender saber mais sobre o Backup do Azure, leia esta [descrição geral](backup-introduction-to-azure-backup.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que lhe permite aceder a qualquer serviço do Azure.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Para efetuar uma cópia de segurança de ficheiros e pastas, tem de criar um cofre dos Serviços de Recuperação na região onde pretende armazenar os dados. Também precisa de determinar como pretende que o seu armazenamento seja replicado.

### <a name="to-create-a-recovery-services-vault"></a>Para criar um cofre dos Serviços de Recuperação
1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/) através da sua subscrição do Azure.
2. No menu Hub, clique em **Mais Serviços** e, na lista de recursos, escreva **Serviços de Recuperação** e clique em **cofres dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Se existirem cofres de serviços de recuperação na subscrição, os cofres estão listados.
3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 3 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.

5. Na secção **Subscrição**, utilize o menu pendente para escolher a subscrição do Azure. Se utilizar apenas uma subscrição, é apresentada essa subscrição e pode avançar para o passo seguinte. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Terá várias escolhas apenas se a sua conta organizacional estiver associada a várias subscrições do Azure.

6. Na secção **Grupo de recursos**:

    * selecione **Criar novo** se pretender criar um novo Grupo de recursos.
    Ou
    * selecione **Utilizar existente** e clique no menu pendente para ver a lista de Grupos de recursos disponíveis.

  Para mais informações mais completas sobre os grupos de Recursos, veja a [Azure Resource Manager overview (Descrição geral do Azure Resource Manager)](../azure-resource-manager/resource-group-overview.md).

7. Clique em **Localização** para selecionar a região geográfica do cofre. Esta escolha determina a região geográfica para onde os dados da cópia de segurança são enviados.

8. Na parte inferior do painel do cofre dos Serviços de Recuperação, clique em **Criar**.

    Pode demorar vários minutos até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita do portal. Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação. Se depois de vários minutos não vir o cofre, clique em **Atualizar**.

    ![Clique no botão Atualizar](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Quando vir o cofre na lista de cofres dos Serviços de Recuperação, está pronto para definir a redundância de armazenamento.

### <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento do cofre
Ao criar um cofre dos Serviços de Recuperação, certifique-se de que a redundância de armazenamento está configurada conforme pretende.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre.

    ![Selecione o novo cofre da lista de cofres dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Ao selecionar o cofre, o painel **cofre dos Serviços de Recuperação** estreita e o painel Definições (*que tem o nome do cofre na parte superior*), e o painel de detalhes do cofre abre.

    ![Ver a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. No painel de Definições do novo cofre, utilize o diapositivo vertical para deslocar para baixo para a secção Gerir e clique em **Infraestrutura de Cópia de Segurança**.
    É aberto o painel Infraestrutura de Cópia de Segurança.
3. No painel Infraestrutura de Cópia de Segurança, clique em **Configuração de Cópia de Segurança** para abrir o painel **Configuração de Cópia de Segurança**.

    ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![opções de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Por predefinição, o seu cofre tem um armazenamento georredundante. Se utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o **Georredundante**. Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) nesta [Descrição geral de redundância de armazenamento](../storage/common/storage-redundancy.md).

Agora que criou um cofre, configure-o para cópia de segurança de ficheiros e pastas.

## <a name="configure-the-vault"></a>Configurar o cofre
1. Nos painel do cofre dos Serviços de Recuperação (do cofre que acabou de criar), na secção Introdução, clique em **Cópia de Segurança** e, em seguida, no painel **Introdução à Cópia de Segurança**, selecione **Objetivo de cópia de segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    É aberto o painel **Objetivo de Cópia de Segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu pendente **Onde está a carga de trabalho em execução?**, selecione **No local**.

    Seleciona **No local** porque o seu Windows Server ou computador Windows é um computador físico que não está no Azure.

3. No menu **Pretende efetuar uma cópia de segurança de que itens?**, selecione **Ficheiros e pastas** e clique em **OK**.

    ![Configurar ficheiros e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

    Depois de clicar em OK, aparece uma marca de verificação junto a **Objetivo de cópia de segurança** e é aberto o painel **Preparar infraestrutura**.

    ![Objetivo de cópia de segurança configurado, em seguida, preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. No painel **Preparar infraestrutura**, clique em **Transferir Agente para o Windows Server ou um Cliente Windows**.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se estiver a utilizar o Windows Server Essential, opte por transferir o agente para o Windows Server Essential. Um menu de pop-up pede-lhe para executar ou guardar MARSAgentInstaller.exe.

    ![Caixa de diálogo MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. No menu de pop-up de transferência, clique em **Guardar**.

    Por predefinição, o ficheiro **MARSagentinstaller.exe** é guardado na pasta Transferências. Quando o instalador concluir, verá um pop-up a perguntar se quer executar o instalador ou abrir a pasta.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Ainda não tem de instalar o agente. Pode instalar o agente depois de transferir as credenciais do cofre.

6. No painel **Preparar infraestrutura**, clique em **Transferir**.

    ![transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    As credenciais do cofre são transferidas para a pasta Transferências. Depois de as credenciais do cofre serem transferidas, verá um pop-up a perguntar se quer abrir ou guardar as credenciais. Clique em **Guardar**. Se clicar acidentalmente em **Abrir**, deixe a caixa de diálogo que tenta abrir as credenciais do cofre falhar. Não é possível abrir as credenciais do cofre. Avance para o passo seguinte. As credenciais do cofre estão na pasta Transferências.   

    ![as credenciais do cofre terminaram de ser transferidas](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

> [!NOTE]
> A permissão da cópia de segurança através do portal do Azure ainda não está disponível. Utilize o Agente dos Serviços de Recuperação do Microsoft Azure para efetuar uma cópia de segurança dos ficheiros e pastas.
>

1. Localize e faça duplo clique em **MARSagentinstaller.exe** na pasta Transferências (ou noutra localização guardada).

    O instalador fornece uma série de mensagens à medida que extrai, instala e regista o agente dos Serviços de Recuperação.

    ![executar as credenciais de instalador do agente dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Conclua o Assistente de Configuração do Agente dos Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, tem de:

   * Selecionar uma localização para a pasta cache e da instalação.
   * Fornecer as informações do servidor proxy se utilizar um servidor proxy para estabelecer ligação à Internet.
   * Forneça os detalhes do seu nome de utilizador e palavra-passe se utilizar um proxy autenticado.
   * Fornecer as credenciais do cofre transferidas
   * Guarde a frase de acesso de encriptação numa localização segura.

     > [!NOTE]
     > Se perder ou se esquecer da frase de acesso, a Microsoft não o pode ajudar a recuperar os dados de cópia de segurança. Guarde o ficheiro numa localização segura. É necessário para restaurar uma cópia de segurança.
     >
     >

O agente está agora instalado e a máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="network-and-connectivity-requirements"></a>Requisitos de Rede e Conectividade

Se o computador/proxy tiver limitado o acesso à Internet, certifique-se de que as definições da firewall no computador/proxy estão configuradas para permitir os seguintes URLs: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net

## <a name="back-up-your-files-and-folders"></a>Efetuar uma cópia de segurança de ficheiros e pastas
A cópia de segurança inicial inclui duas tarefas principais:

* Agendar a cópia de segurança
* Efetuar a cópia de segurança de ficheiros e pastas pela primeira vez

Para concluir a cópia de segurança inicial, utilize o agente dos Serviços de Recuperação do Microsoft Azure.

### <a name="to-schedule-the-backup-job"></a>Para agendar a tarefa de cópia de segurança
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

   > [!NOTE]
   > Para obter mais informações sobre como especificar a agenda da cópia de segurança, consulte o artigo [Utilizar o Backup do Azure para substituir a infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).
   >

8. Na página **Selecionar Política de Retenção** página, selecione a **Política de Retenção** para a cópia de segurança.

    A política de retenção especifica o tempo que os dados de cópia de segurança são armazenados. Em vez de especificar uma "política simples" para todos os pontos de cópia de segurança, pode especificar políticas de retenção diferentes com base em quando ocorre a cópia de segurança. Pode modificar as políticas de retenção diárias, semanais, mensais e anuais para corresponder às suas necessidades.
9. Na página Escolher Tipo de Cópia de Segurança Inicial, escolha o tipo de cópia de segurança inicial. Deixe a opção **Automaticamente através da rede** selecionada e, em seguida, clique em **Seguinte**.

    Poderá fazer cópias de segurança automaticamente através da rede ou pode efetuar cópias de segurança offline. O resto deste artigo descreve o processo para efetuar uma cópia de segurança automaticamente. Se preferir efetuar uma cópia de segurança offline, consulte o artigo [Fluxo de trabalho da cópia de segurança offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.
10. Na página de Confirmação, reveja as informações e, em seguida, clique em **Concluir**.
11. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Para efetuar a cópia de segurança de ficheiros e pastas pela primeira vez
1. No agente dos Serviços de Recuperação, clique em **Efetuar Cópia de Segurança Agora** para concluir a propagação inicial através da rede.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, clique em **Efetuar Cópia de Segurança**.
3. Clique em **Fechar** para fechar o assistente. Se fechar este assistente antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

![IV concluídos](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passos seguintes
* Obtenha mais detalhes sobre como [efetuar a cópia de segurança das máquinas Windows](backup-configure-vault.md).
* Agora que criou uma segurança dos seus ficheiros e pastas, pode [gerir os cofres e os servidores](backup-azure-manage-windows-server.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
