---
title: "Agente de cópia de segurança do Azure de utilização para fazer uma cópia de segurança de ficheiros e pastas | Microsoft Docs"
description: "Utilize o Microsoft Azure Backup agent para fazer cópias de segurança de pastas e ficheiros do Windows Azure. Criar um cofre dos serviços de recuperação, instale o agente de cópia de segurança, definir a política de cópia de segurança e executar a cópia de segurança inicial em ficheiros e pastas."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "cofre da cópia de segurança; cópia de segurança de um servidor do Windows; cópias de segurança;"
ms.assetid: 7f5b1943-b3c1-4ddb-8fb7-3560533c68d5
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: b95dc0a83d8e5618effb573353f419e1837d30c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Fazer cópias de segurança de um servidor ou cliente Windows para o Azure com o modelo de implementação Resource Manager
> [!div class="op_single_selector"]
> * [Portal do Azure](backup-configure-vault.md)
> * [Portal clássico](backup-configure-vault-classic.md)
>
>

Este artigo explica como fazer cópias de segurança do Windows Server (ou cliente Windows) ficheiros e pastas para o Azure com cópia de segurança do Azure utilizando o modelo de implementação Resource Manager.

[!INCLUDE [learn-about-deployment-models](../../includes/backup-deployment-models.md)]

![Passos do processo de cópia de segurança](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar
Para fazer uma cópia de segurança de um servidor ou cliente para o Azure, terá de uma conta do Azure. Se não tiver uma, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 
Um cofre dos serviços de recuperação é uma entidade que armazena todas as cópias de segurança e pontos de recuperação que criar ao longo do tempo. O Cofre de serviços de recuperação também contém a política de cópia de segurança aplicada para os ficheiros e pastas protegidos. Quando criar um cofre dos serviços de recuperação, deve também selecionar a opção de redundância de armazenamento adequado.

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


### <a name="set-storage-redundancy"></a>Redundância de armazenamento do conjunto
Quando cria pela primeira vez um cofre dos Serviços de Recuperação, determina como o armazenamento é replicado.

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

Agora que criou um cofre, prepare a infraestrutura para fazer uma cópia de segurança de ficheiros e pastas a transferir e instalar o agente dos serviços de recuperação do Microsoft Azure, transferir as credenciais do cofre e, em seguida, utilizar essas credenciais ao registar o agente com o cofre.

## <a name="configure-the-vault"></a>Configurar o cofre

1. Nos painel do cofre dos Serviços de Recuperação (do cofre que acabou de criar), na secção Introdução, clique em **Cópia de Segurança** e, em seguida, no painel **Introdução à Cópia de Segurança**, selecione **Objetivo de cópia de segurança**.

  ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  É aberto o painel **Objetivo de Cópia de Segurança**. Se o Cofre dos serviços de recuperação tiver sido configurado anteriormente, em seguida, a **objetivo de cópia de segurança** painéis abre-se ao clicar em **cópia de segurança** nos serviços de recuperação painel do cofre.

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

Se o computador/proxy limitou o acesso à internet, certifique-se de que as definições da firewall no computador/proxy estão configuradas para permitir que os seguintes URLs: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.ne


## <a name="create-the-backup-policy"></a>Criar a política de cópias de segurança
A política de cópia de segurança é a agenda quando são criados pontos de recuperação e o período de tempo que são mantidos os pontos de recuperação. Utilize o agente de cópia de segurança do Microsoft Azure para criar a política de cópia de ficheiros e pastas.

### <a name="to-create-a-backup-schedule"></a>Para criar uma agenda de cópia de segurança
1. Abra o Microsoft Azure Backup agent. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Iniciar o agente do Backup do Azure](./media/backup-configure-vault/snap-in-search.png)
2. No agente de cópia de segurança **ações** painel, clique em **Agendar cópia de segurança** para iniciar o Assistente de cópia de segurança de agenda.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. No **introdução** página do Assistente de cópia de segurança agenda, clique em **seguinte**.
4. No **selecionar itens para cópia de segurança** página, clique em **adicionar itens**.

  Abre a caixa de diálogo selecionar itens.

5. Selecione os ficheiros e pastas que pretende proteger e, em seguida, clique em **OK**.
6. No **selecionar itens para cópia de segurança** página, clique em **seguinte**.
7. No **Especificar agenda de cópia de segurança** página, especifique a agenda de cópia de segurança e clique em **seguinte**.

    Pode agendar cópias de segurança diárias (no máximo, três vezes por dia) ou semanais.

    ![Itens para Cópia de Segurança do Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > Para obter mais informações sobre como especificar a agenda da cópia de segurança, consulte o artigo [Utilizar o Backup do Azure para substituir a infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. No **selecionar política de retenção** página, escolha as políticas de retenção específico a para a cópia de segurança e clique em **seguinte**.

    A política de retenção Especifica a duração que está armazenada a cópia de segurança. Em vez de apenas especificar uma "política simples" para todos os pontos de cópia de segurança, pode especificar políticas de retenção diferentes com base em quando ocorre a cópia de segurança. Pode modificar as políticas de retenção diárias, semanais, mensais e anuais para corresponder às suas necessidades.
9. Na página Escolher Tipo de Cópia de Segurança Inicial, escolha o tipo de cópia de segurança inicial. Deixe a opção **Automaticamente através da rede** selecionada e, em seguida, clique em **Seguinte**.

    Poderá fazer cópias de segurança automaticamente através da rede ou pode efetuar cópias de segurança offline. O resto deste artigo descreve o processo para efetuar uma cópia de segurança automaticamente. Se preferir efetuar uma cópia de segurança offline, consulte o artigo [Fluxo de trabalho da cópia de segurança offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.
10. Na página de Confirmação, reveja as informações e, em seguida, clique em **Concluir**.
11. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### <a name="enable-network-throttling"></a>Ativar a limitação de rede
O agente de cópia de segurança do Microsoft Azure fornece a limitação de rede. Limitação controlos como largura de banda de rede é utilizada durante a transferência de dados. Este controlo pode ser útil se precisar de fazer uma cópia de segurança de dados durante as horas de trabalho, mas não pretendem que o processo de cópia de segurança interfira com outro tráfego de Internet. Limitação aplica-se a cópia de segurança e restaurar as atividades.

> [!NOTE]
> Limitação de rede não está disponível no Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ou Windows 7 (com os service packs). A funcionalidade de limitação de rede de cópia de segurança do Azure envolva Quality of Service (QoS) no sistema operativo local. Apesar de cópia de segurança do Azure pode proteger estes sistemas operativos, a versão do QoS disponível nas plataformas seguintes não funciona com a limitação de rede de cópia de segurança do Azure. Limitação de rede pode ser utilizada em todos os outros [sistemas operativos suportados](backup-azure-backup-faq.md).
>
>

**Para ativar a limitação de rede**

1. No agente de cópia de segurança do Microsoft Azure, clique em **alterar propriedades**.

    ![Alterar propriedades](./media/backup-configure-vault/change-properties.png)
2. No **limitação** separador, selecione o **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet** caixa de verificação.

    ![Limitação de rede](./media/backup-configure-vault/throttling-dialog.png)
3. Depois de ter ativado a limitação, especificar a largura de banda permitida para transferência de dados de cópia de segurança durante **as horas de trabalho** e **horas de descanso**.

    Os valores de largura de banda começam em 512 quilobits por segundo (Kbps) e podem ir até 1,023 megabytes por segundo (MBps). Também pode designar o início e concluir para **as horas de trabalho**, e os dias da semana são considerados trabalho dias. Horas fora do trabalho designado horas são consideradas descanso horas.
4. Clique em **OK**.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>Para efetuar a cópia de segurança de ficheiros e pastas pela primeira vez
1. No agente de cópia de segurança, clique em **cópia de segurança agora** para concluir a propagação inicial através da rede.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-configure-vault/backup-now.png)
2. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, clique em **Efetuar Cópia de Segurança**.
3. Clique em **Fechar** para fechar o assistente. Se o fizer antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

![IV concluídos](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passos seguintes
Para obter informações adicionais sobre como fazer uma cópia de segurança de VMs ou outras cargas de trabalho, consulte:

* Agora que criou uma segurança dos seus ficheiros e pastas, pode [gerir os cofres e os servidores](backup-azure-manage-windows-server.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
