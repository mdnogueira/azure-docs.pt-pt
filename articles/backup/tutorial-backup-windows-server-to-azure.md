---
title: "Cópia de segurança do Windows Server para o Azure | Microsoft Docs"
description: "Este tutorial detalhes cópia de segurança de servidores do Windows no local para um cofre dos serviços de recuperação."
services: back up
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "servidor do Windows efetuar cópias de segurança; criar cópias de segurança do windows server; cópia de segurança e recuperação após desastre"
ms.assetid: 
ms.service: back up
ms.workload: storage-back up-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: saurabhsensharma;markgal;
ms.custom: 
ms.openlocfilehash: f81f23862e783de07b5ec5aebad7f0a781168bd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-server-to-azure"></a>Faça uma Cópia de Segurança do Windows Server para o Azure


Pode utilizar o Backup do Azure para proteger o servidor do Windows de danos, ataques e desastres inesperados. Cópia de segurança do Azure fornece uma ferramenta simples conhecida como o agente de serviços de recuperação do Azure (MARS) da Microsoft. O agente MARS está instalado no servidor do Windows para proteger ficheiros e pastas e informações de configuração do servidor através do Estado do sistema do Windows Server. Este tutorial explica como pode utilizar o agente MARS fazer cópias de segurança do Windows Server para o Azure. Neste tutorial, ficará a saber como: 


> [!div class="checklist"]
> * Transferir e configurar o agente MARS
> * Configurar cópia de segurança vezes e a agenda de retenção para cópias de segurança do servidor
> * Efetuar a cópia de segurança ad-hoc


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Antes de fazer uma cópia de segurança do Windows Server, tem de criar um local para as cópias de segurança ou restaurar pontos, para ser armazenado. A [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor do Azure que armazena as cópias de segurança do seu servidor do Windows. Siga os passos abaixo para criar um cofre dos serviços de recuperação no portal do Azure. 

1. No menu da esquerda, selecione **mais serviços** e na lista de serviços, escreva **dos serviços de recuperação**. Clique em **cofres dos serviços de recuperação**.

   ![Abra o Cofre dos serviços de recuperação](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault.png)

2.  No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

   ![fornecer informações para o Cofre de](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  No **cofre dos serviços de recuperação** menu,

    - Tipo *myRecoveryServicesVault* no **nome**.
    - A subscrição atual ID aparece no **subscrição**.
    - Para **grupo de recursos**, selecione **utilizar existente** e escolha *myResourceGroup*. Se *myResourceGroup* não existe, selecione **criar novo** e tipo *myResourceGroup*. 
    - Do **localização** menu pendente, escolha *Europa Ocidental*.
    - Clique em **criar** para criar o seu Cofre de serviços de recuperação.
 
Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação.

## <a name="download-recovery-services-agent"></a>Transferir o agente dos serviços de recuperação

O agente de serviços de recuperação do Azure (MARS) da Microsoft cria uma associação entre o Windows Server e o seu Cofre de serviços de recuperação. O procedimento seguinte explica como transferir o agente ao servidor.

1.  Na lista de cofres dos serviços de recuperação, selecione **myRecoveryServicesVault** para abrir o dashboard.

   ![fornecer informações para o Cofre de](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  No menu do dashboard do cofre, clique em **cópia de segurança**.

3.  No **objetivo de cópia de segurança** menu:

    - para **onde está a carga de trabalho em execução?**, selecione**no local**, 
    - para **que itens pretende cópia de segurança?**, selecione **ficheiros e pastas** e **estado do sistema** 

    ![fornecer informações para o Cofre de](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  Clique em **preparar infraestrutura** para abrir o **preparar a infraestrutura** menu.
5.  No **preparar a infraestrutura** menu, clique em **Transferir agente para o Windows Server ou o cliente Windows** para transferir o *MARSAgentInstaller.exe*. 

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    O instalador abre um browser separado e transfere **MARSAgentInstaller.exe**.
 
6.  Antes de executar o ficheiro transferido, clique em de **transferir** botão no painel preparar infraestrutura download e grave o **as credenciais do cofre** ficheiro. Este ficheiro é necessário para ligar o agente MARS com o Cofre de serviços de recuperação.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Localize e faça duplo clique de transferido **MARSagentinstaller.exe**.
2. O **Assistente de configuração do agente de serviços do Microsoft Azure Recovery** aparece. À medida que avança o assistente, forneça as seguintes informações quando lhe for pedido e clique em **registar**.
    - Localização da pasta de instalação e a cache.
    - Informações do servidor proxy se utilizar um servidor proxy para estabelecer ligação à internet.
    - Os nome e palavra-passe detalhes de utilizador se utilizar um proxy autenticado.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. No final do assistente, clique em **avançar para o registo** e forneça o **as credenciais do cofre** ficheiro transferido no procedimento anterior.
 
4. Quando solicitado, forneça uma frase de acesso de encriptação para encriptar as cópias de segurança do Windows Server. Guarde a frase de acesso numa localização segura, como a Microsoft não é possível recuperar o frase de acesso se se perder.

5. Clique em **Concluir**. 

## <a name="configure-backup-and-retention"></a>Configurar a cópia de segurança e retenção

Utilize o agente dos serviços de recuperação do Microsoft Azure para agendar quando ocorrerem cópias de segurança para o Azure, no Windows Server. Execute os seguintes passos no servidor onde transferiu o agente.

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

2.  Na consola do agente dos serviços de recuperação, clique em **Agendar cópia de segurança** sob o **painel ações**.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Clique em **seguinte** para navegar para o **selecionar itens para cópia segurança** página.

4. Clique em **adicionar itens** e na caixa de diálogo que se abre selecione **estado do sistema** e ficheiros ou pastas que pretende criar cópias de segurança. Em seguida, clique em **OK**.

5. Clique em **Seguinte**.

6. No **Especificar agenda de cópia de segurança** página, especifique as horas do dia ou quando as cópias de segurança tem de ser acionado para ficheiros e pastas de semana. Agenda de cópia de segurança do Estado do sistema é automaticamente configurada. 

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)
 


7.  No **selecionar política de retenção** página, selecione a política de retenção para a cópia de segurança de ficheiros e pastas. O período de retenção de cópias de segurança do Estado do sistema é automaticamente definido para 60 dias.
8.  No **escolha inicial cópia de segurança tipo** página, deixe a opção **automaticamente através da rede** selecionada e, em seguida, clique em **seguinte**.
9.  No **confirmação** página, reveja as informações e, em seguida, clique em **concluir**.
10. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

## <a name="perform-an-ad-hoc-back-up"></a>Efetuar a cópia de segurança ad-hoc


Tem de estabelecer a agenda quando executar tarefas de cópia de segurança. No entanto, não efetuou cópia de segurança do servidor. É uma prática de melhor de recuperação após desastre para executar uma cópia de segurança a pedido para se certificar de resiliência de dados para o servidor.

1.  Na consola do agente dos serviços de recuperação do Microsoft Azure, clique em **cópia de segurança agora**.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

2.  No **confirmação** , reveja as definições que o **cópia de segurança agora** utiliza o Assistente para fazer uma cópia de segurança do servidor. Em seguida, clique em **Efetuar Cópia de Segurança**.
3.  Clique em **Fechar** para fechar o assistente. Se fechar o assistente antes do cópia de segurança de processo de conclusão, o assistente continua em execução em segundo plano.
4.  Uma vez concluída a cópia de segurança inicial, **tarefa foi concluída** estado é apresentado no **tarefas** painel da consola de agente MARS.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial que utilizou o portal do Azure para: 
 
> [!div class="checklist"] 
> * Criar um cofre dos Serviços de Recuperação  
> * Transferir o agente dos serviços de recuperação do Microsoft Azure 
> * Instalar o agente 
> * Configurar a cópia de segurança para o Windows Server 
> * Efetuar uma cópia de segurança a pedido 

Continue para o próximo tutorial recuperar ficheiros a partir do Azure para o Windows Server

> [!div class="nextstepaction"] 
> [Restaurar ficheiros a partir do Azure para o Windows Server](./tutorial-backup-restore-files-windows-server.md) 

