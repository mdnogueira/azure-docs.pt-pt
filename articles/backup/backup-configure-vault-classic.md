---
title: "Cópia de segurança do Windows server ou estação de trabalho para o Azure (modelo clássico) | Microsoft Docs"
description: "Cópia de segurança Windows servidores ou clientes para um cofre de cópia de segurança no Azure. Percorra Noções básicas para proteger ficheiros e pastas para um cofre de cópia de segurança utilizando o agente do Backup do Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "cofre da cópia de segurança; cópia de segurança de um servidor do Windows; cópias de segurança;"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 717d1f377e1a074984a0332b978754bea26ce144
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>Fazer cópias de segurança de um Windows server ou estação de trabalho utilizando o portal clássico do Azure
> [!div class="op_single_selector"]
> * [Portal clássico](backup-configure-vault-classic.md)
> * [Portal do Azure](backup-configure-vault.md)
>
>

Este artigo abrange os procedimentos que terá de seguir para preparar o seu ambiente e a cópia de segurança de um servidor do Windows (ou estação de trabalho) para o Azure. Também abrange as considerações para implementar a sua solução de cópia de segurança. Se estiver interessado na tentativa de cópia de segurança do Azure pela primeira vez, este artigo rapidamente explica o processo.

O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

## <a name="before-you-start"></a>Antes de começar
Para fazer uma cópia de segurança de um servidor ou cliente para o Azure, terá de uma conta do Azure. Se não tiver uma, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

## <a name="create-a-backup-vault"></a>Criar um cofre de cópia de segurança
Para fazer uma cópia de segurança de ficheiros e pastas de um servidor ou cliente, terá de criar um cofre de cópia de segurança na região geográfica onde pretende armazenar os dados.

> [!IMPORTANT]
> A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança.
>
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Depois de **30 de Novembro de 2017**, não poderá utilizar o PowerShell para criar cofres de cópia de segurança. <br/> **Até 30 de Novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>


## <a name="download-the-vault-credential-file"></a>Transfira o ficheiro de credenciais do Cofre
A máquina no local tem de ser autenticados com um cofre de cópia de segurança antes-pode fazer cópias de segurança de dados do Azure. A autenticação é efetuada através de *credenciais do cofre*. O ficheiro de credenciais do Cofre é transferido através de um canal seguro do portal clássico. A chave privada do certificado não sejam mantidas no portal do ou o serviço.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>Para transferir o ficheiro de credenciais do cofre para um computador local
1. No painel de navegação esquerdo, clique em **dos serviços de recuperação**e, em seguida, selecione o Cofre de cópia de segurança que criou.

    ![IV concluídos](./media/backup-configure-vault-classic/rs-left-nav.png)
2. Na página de início rápido, clique em **as credenciais do Cofre de transferência**.

   O portal clássico gera uma credencial do cofre, utilizando uma combinação de nome do cofre e a data atual. O ficheiro de credenciais do Cofre é utilizado apenas durante o fluxo de trabalho do registo e expira após 48 horas.

   O ficheiro de credenciais de cofre pode ser transferido a partir do portal.
3. Clique em **guardar** para transferir o ficheiro de credenciais do cofre para a pasta de transferências da conta local. Também pode selecionar **guardar como** do **guardar** menu para especificar uma localização para o ficheiro de credenciais do cofre.

   > [!NOTE]
   > Certifique-se de que o ficheiro de credenciais do Cofre é guardado numa localização que pode ser acedida a partir do seu computador. Se esta está armazenada num bloco de mensagem de servidor ou partilha de ficheiros, certifique-se de que tem as permissões para aceder ao mesmo.
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>Transferir, instalar e registar o agente de cópia de segurança
Depois de criar o Cofre de cópia de segurança e transfira o ficheiro de credenciais do cofre, tem de estar instalado um agente em cada uma das suas máquinas do Windows.

### <a name="to-download-install-and-register-the-agent"></a>Para transferir, instalar e registar o agente
1. Clique em **dos serviços de recuperação**e, em seguida, selecione o Cofre de cópia de segurança que pretende registar um servidor.
2. Na página de início rápido, clique no agente **agente para o Windows Server ou o cliente do System Center Data Protection Manager ou do Windows**. Em seguida, clique em **Guardar**.

    ![Guardar o agente](./media/backup-configure-vault-classic/agent.png)
3. Depois do ficheiro MARSagentinstaller.exe transferiu, clique em **executar** (ou faça duplo clique **MARSAgentInstaller.exe** da localização).
4. Escolha a pasta de instalação e a pasta de cache que são necessários para o agente e, em seguida, clique em **seguinte**. Especificar a localização da cache tem de ter espaço livre igual a, pelo menos, 5 por cento dos dados de cópia de segurança.
5. Pode continuar a aceder à Internet através de definições de proxy predefinido.             Se utilizar um servidor proxy para estabelecer ligação à Internet, na página de configuração de Proxy, selecione o **utilizar definições de proxy personalizado** caixa de verificação e, em seguida, introduza os detalhes do servidor proxy. Se utilizar um proxy autenticado, introduza os detalhes de nome e palavra-passe de utilizador e, em seguida, clique em **seguinte**.
6. Clique em **instalar** para iniciar a instalação do agente. Instala o agente de cópia de segurança do Windows PowerShell e .NET Framework 4.5 (se ainda não está instalado) para concluir a instalação.
7. Depois do agente está instalado, clique em **avançar para o registo** para continuar com o fluxo de trabalho.
8. Na página de identificação do cofre, procure e selecione o ficheiro de credenciais do cofre que transferiu anteriormente.

    O ficheiro de credenciais do Cofre é válido para apenas 48 horas após a transferência do portal. Se ocorrer um erro nesta página (por exemplo, "as credenciais do cofre ficheiro fornecido expirou"), inicie sessão portal e transfira novamente o ficheiro de credenciais do cofre.

    Certifique-se de que o ficheiro de credenciais do Cofre está disponível numa localização que pode ser acedida pela aplicação de configuração. Se ocorrerem erros relacionados com o acesso, copie o ficheiro de credenciais do cofre para uma localização temporária na mesma máquina e repita a operação.

    Se ocorrer um erro de credenciais do cofre, tais como "credenciais de cofre inválidas fornecidas", o ficheiro está danificado ou não tiver as credenciais mais recentes associado o serviço de recuperação. Repita a operação depois de transferir um novo ficheiro de credenciais do cofre a partir do portal. Este erro também pode ocorrer se um utilizador clica o **credenciais do Cofre de transferência** opção várias vezes na sucessivamente rápida. Neste caso, apenas o ficheiro de credenciais de cofre último é válido.
9. Na página de definição de encriptação, pode gerar uma frase de acesso ou fornecer uma frase de acesso (com um mínimo de 16 carateres). Lembre-se guardar o frase de acesso numa localização segura.
10. Clique em **Concluir**. O Assistente de registo do servidor regista o servidor com a cópia de segurança.

    > [!WARNING]
    > Se perder ou se esqueça da frase de acesso, o Microsoft não pode ajudar a recuperar os dados de cópia de segurança. Possui a frase de acesso de encriptação e a Microsoft não têm visibilidade para a frase de acesso que utiliza. Guarde o ficheiro numa localização segura porque será necessário durante uma operação de recuperação.
    >
    >

11. Depois da chave de encriptação está definida, deixe o **iniciar o Microsoft Azure Recovery Services Agent** selecionada de caixa de verificação e, em seguida, clique em **fechar**.

## <a name="complete-the-initial-backup"></a>Concluir a cópia de segurança inicial
A cópia de segurança inicial inclui duas tarefas principais:

* Criar a agenda de cópia de segurança
* Cópia de segurança de ficheiros e pastas pela primeira vez

Quando a política de cópia de segurança estiver concluída a cópia de segurança inicial, cria pontos de cópia de segurança que pode utilizar se precisar de recuperar os dados. A política de cópia de segurança efetua este procedimento com base numa agenda que definir.

### <a name="to-schedule-the-backup"></a>Para agendar a cópia de segurança
1. Abra o Microsoft Azure Backup agent. (Será aberta automaticamente se marcou a **iniciar o Microsoft Azure Recovery Services Agent** caixa de verificação selecionada quando fechar o Assistente de registo do servidor.) Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Iniciar o agente do Backup do Azure](./media/backup-configure-vault-classic/snap-in-search.png)
2. No agente de cópia de segurança, clique em **Agendar cópia de segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. Na página Introdução do Assistente para Agendar Cópia de Segurança, clique em **Seguinte**.
4. Na página Selecionar Itens para Cópia de Segurança, clique em **Adicionar Itens**.
5. Selecione os ficheiros e pastas para a cópia de segurança e, em seguida, clique em **OK**.
6. Clique em **Seguinte**.
7. N página **Especificar Agenda de Cópia de Segurança**, especifique a **agenda da cópia de segurança** e clique em **Seguinte**.

    Pode agendar cópias de segurança diárias (no máximo, três vezes por dia) ou semanais.

    ![Itens para Cópia de Segurança do Windows Server](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > Para obter mais informações sobre como especificar a agenda da cópia de segurança, consulte o artigo [Utilizar o Backup do Azure para substituir a infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. Na página **Selecionar Política de Retenção** página, selecione a **Política de Retenção** para a cópia de segurança.

    A política de retenção especifica o período durante o qual a cópia de segurança será armazenada. Em vez de apenas especificar uma "política simples" para todos os pontos de cópia de segurança, pode especificar políticas de retenção diferentes com base em quando ocorre a cópia de segurança. Pode modificar as políticas de retenção diárias, semanais, mensais e anuais para corresponder às suas necessidades.
9. Na página Escolher Tipo de Cópia de Segurança Inicial, escolha o tipo de cópia de segurança inicial. Deixe a opção **Automaticamente através da rede** selecionada e, em seguida, clique em **Seguinte**.

    Poderá fazer cópias de segurança automaticamente através da rede ou pode efetuar cópias de segurança offline. O resto deste artigo descreve o processo para efetuar uma cópia de segurança automaticamente. Se preferir efetuar uma cópia de segurança offline, consulte o artigo [Fluxo de trabalho da cópia de segurança offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.
10. Na página de Confirmação, reveja as informações e, em seguida, clique em **Concluir**.
11. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### <a name="enable-network-throttling-optional"></a>Ativar a limitação de rede (opcional)
O agente de cópia de segurança fornece a limitação de rede. Limitação controlos como largura de banda de rede é utilizada durante a transferência de dados. Este controlo pode ser útil se precisar de fazer uma cópia de segurança de dados durante as horas de trabalho, mas não pretendem que o processo de cópia de segurança interfira com outro tráfego de Internet. Limitação aplica-se a cópia de segurança e restaurar as atividades.

**Para ativar a limitação de rede**

1. No agente de cópia de segurança, clique em **alterar propriedades**.

    ![Alterar propriedades](./media/backup-configure-vault-classic/change-properties.png)
2. No **limitação** separador, selecione o **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet** caixa de verificação.

    ![Limitação de rede](./media/backup-configure-vault-classic/throttling-dialog.png)
3. Depois de ter ativado a limitação, especificar a largura de banda permitida para transferência de dados de cópia de segurança durante **as horas de trabalho** e **horas de descanso**.

    Os valores de largura de banda começam em 512 quilobits por segundo (Kbps) e podem ir até 1,023 megabytes por segundo (MBps). Também pode designar o início e concluir para **as horas de trabalho**, e os dias da semana são considerados trabalho dias. Horas fora do trabalho designado horas são consideradas descanso horas.
4. Clique em **OK**.

### <a name="to-back-up-now"></a>Para criar cópias de segurança agora
1. No agente de cópia de segurança, clique em **cópia de segurança agora** para concluir a propagação inicial através da rede.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-configure-vault-classic/backup-now.png)
2. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, clique em **Efetuar Cópia de Segurança**.
3. Clique em **Fechar** para fechar o assistente. Se o fizer antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

![IV concluídos](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>Passos seguintes
* Inscrever-se um [conta do Azure gratuita](https://azure.microsoft.com/free/).

Para obter informações adicionais sobre como fazer uma cópia de segurança de VMs ou outras cargas de trabalho, consulte:

* [Fazer uma cópia de segurança de VMs do IaaS](backup-azure-vms-prepare.md)
* [Fazer cópias de segurança de cargas de trabalho para o Azure com o servidor de cópia de segurança do Microsoft Azure](backup-azure-microsoft-azure-backup.md)
* [Fazer cópias de segurança de cargas de trabalho para o Azure com o DPM](backup-azure-dpm-introduction.md)
