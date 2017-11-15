---
title: "Gerir servidores e os cofres dos serviços de recuperação do Azure | Microsoft Docs"
description: "Utilize este tutorial para saber como gerir os cofres dos serviços de recuperação do Azure e servidores."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: markgal
ms.openlocfilehash: 58080d0e045f1825e89287fc421b7e84db36331e
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Monitorizar e gerir cofres e servidores dos serviços de recuperação do Azure para computadores Windows

Este artigo contém uma descrição geral de tarefas monitorização e gestão de cópia de segurança, disponíveis através do portal do Azure e o agente de cópia de segurança do Microsoft Azure. Este artigo pressupõe que já tem uma subscrição do Azure e tiver criado, pelo menos, um cofre dos serviços de recuperação.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Abra um cofre dos serviços de recuperação

O dashboard do cofre dos serviços de recuperação mostra os detalhes ou atributos de um cofre dos serviços de recuperação.

1. Iniciar sessão para o [Portal do Azure](https://portal.azure.com/) através da sua subscrição do Azure.
2. No Hub menu, clique em **mais serviços**.

    ![Abrir lista de cofres de serviços de recuperação passo 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

3. Pretende abrir um cofre dos serviços de recuperação. Na caixa de diálogo, comece a escrever **dos serviços de recuperação**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **cofres dos serviços de recuperação** para apresentar a lista de cofres dos serviços de recuperação na sua subscrição.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-windows-server/browse-to-rs-vaults-2.png) <br/>

    A lista de cofres dos serviços de recuperação abre.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Na lista de cofres, selecione o nome do cofre dos serviços de recuperação que pretende abrir. Abre o menu de dashboard do Cofre de serviços de recuperação.

    ![dashboard do Cofre de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Agora que tem de abrir o Cofre dos serviços de recuperação, tente qualquer uma das tarefas de gestão ou de monitorização.

## <a name="monitor-backup-jobs-and-alerts"></a>Alertas e tarefas de cópia de segurança do monitor

Monitorizar tarefas e alertas a partir do dashboard do cofre dos serviços de recuperação, em que verá:

* Detalhes de alertas de cópias de segurança
* Ficheiros e pastas, bem como máquinas virtuais do Azure protegidas na nuvem
* Armazenamento total consumido no Azure
* Estado da tarefa de cópia de segurança

![Tarefas de cópia de segurança de dashboard](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Ao clicar nas informações em cada um destes mosaicos abre o menu associado onde irá gerir tarefas relacionadas.

Na parte superior do Dashboard:

* Definições - fornece acesso tarefas de cópia de segurança disponíveis.
* Cópia de segurança - ajuda a efetuar uma cópia de segurança de novos ficheiros e pastas (ou VMs do Azure) para o Cofre dos serviços de recuperação.
* Elimine - se de uma recuperação cofre dos serviços já não está a ser utilizado, eliminá-la para libertar espaço de armazenamento. DELETE só é ativada após todos os servidores protegidos tem sido eliminados do cofre.

![Tarefas de cópia de segurança de dashboard](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Alertas de cópias de segurança utilizando o agente de cópia de segurança do Azure:
| Nível de alerta | Alertas enviados |
| --- | --- |
| Crítico |Falha de cópia de segurança, falha de recuperação |
| Aviso |Cópia de segurança foi concluída com avisos (quando < 100 ficheiros não são guardados em cópia de segurança devido a problemas de danos e > 1,000,000 ficheiros com êxito são uma cópia de segurança) |
| Informativo |Nenhuma |

## <a name="manage-backup-alerts"></a>Gerir alertas de cópia de segurança
Clique em de **alertas de cópia de segurança** mosaico para abrir o **alertas de cópia de segurança** menu e gerir alertas.

![Alertas de cópias de segurança](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

O mosaico alertas de cópia de segurança mostra o número de:

* alertas críticos não pode estar resolvidos nas últimas 24 horas
* alertas de aviso não pode estar resolvidas nas últimas 24 horas

Clique na ligação para ver o **alertas de cópia de segurança** menu, com uma vista filtrada destes alertas (crítico ou de aviso).

No menu de alertas de cópia de segurança, poderá:

* Escolha as informações adequadas para incluir os alertas.

    ![Escolher colunas](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtrar alertas para a gravidade, estado e tempos de início/fim.

    ![Filtrar alertas](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Configurar notificações para a gravidade, a frequência e os destinatários, bem como ativar ou desativar a alertas.

    ![Filtrar alertas](./media/backup-azure-manage-windows-server/configure-notifications.png)

Se **pelo alerta** é selecionado como o **notificar** frequência, sem agrupamento ou redução nos e-mails ocorre. Os resultados de cada alerta numa notificação (a predefinição) e é enviado um e-mail de resolução imediatamente.

Se **resumida horária** é selecionado como o **notificar** frequência, é enviado um e-mail para o utilizador explicar não resolvidos os alertas foram gerados na última hora. É enviado um e-mail de resolução no fim da hora.

Alertas podem ser enviados para os seguintes níveis de gravidade:

* Crítico
* aviso
* Informações

Desativar o alerta com o **desativar** botão no menu de detalhes da tarefa. Ao clicar em desativar, pode fornecer as notas de resolução.

Escolha as colunas que pretende ver apresentado como parte do alerta com o **escolher colunas** botão.

> [!NOTE]
> Do **definições** menu, gerir alertas cópias de segurança, selecionando **monitorização e relatórios > alertas e eventos > alertas cópias de segurança** e, em seguida, clicando em **filtro** ou  **Configurar notificações**.
>
>

## <a name="manage-backup-items"></a>Gerir itens de cópia de segurança
Gerir cópias de segurança no local está agora disponível no portal de gestão. Na secção de cópia de segurança do dashboard, o **itens de cópia de segurança** mosaico mostra o número de itens de cópia de segurança protegido para o cofre.

Clique em **ficheiro pastas** nos itens de cópia de segurança do mosaico.

![Mosaico de itens de cópia de segurança](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Abre o menu de itens de cópia de segurança com o filtro de definir a pasta de ficheiros em que verá cada item listada de cópia de segurança específica.

![Itens de cópia de segurança](./media/backup-azure-manage-windows-server/backup-item-list.png)

Se selecionar um item de cópia de segurança específico da lista, consulte os detalhes essenciais para que o item.

> [!NOTE]
> Do **definições** menu, gerir ficheiros e pastas selecionando **itens protegidos > itens de cópia de segurança** e, em seguida, selecionar **ficheiro pastas** no menu pendente.
>
>

![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Gerir tarefas de cópia de segurança
Tarefas de cópia de segurança no local (quando o servidor no local é fazer cópias de segurança do Azure) e as cópias de segurança do Azure são visíveis no dashboard.

Na secção de cópia de segurança do dashboard, o mosaico da tarefa de cópia de segurança mostra o número de tarefas:

* em curso
* Falha nas últimas 24 horas.

Para gerir as tarefas de cópia de segurança, clique em de **as tarefas de cópia de segurança** mosaico, que abre o menu de tarefas de cópia de segurança.

![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/backup-jobs.png)

Modificar as informações disponíveis no menu tarefas de cópia de segurança com o **escolher colunas** botão na parte superior da página.

Utilize o **filtro** botão para selecionar entre os ficheiros e pastas e cópia de segurança da máquina virtual do Azure.

Se não vir os seus ficheiros de cópia de segurança e pastas, clique em **filtro** botão na parte superior da página e selecione **ficheiros e pastas** no menu de tipo de Item.

> [!NOTE]
> Do **definições** menu, gerir as tarefas de cópia de segurança, selecionando **monitorização e relatórios > tarefas > as tarefas de cópia de segurança** e, em seguida, selecionar **ficheiro pastas** na lista pendente menu.
>
>

## <a name="monitor-backup-usage"></a>Monitorizar a utilização da cópia de segurança
Na secção de cópia de segurança do dashboard, o mosaico de utilização de cópia de segurança mostra o armazenamento consumido no Azure. Utilização do armazenamento é fornecida para:

* Utilização de armazenamento na nuvem LRS associada ao Cofre
* Utilização de armazenamento na nuvem GRS associada ao Cofre

## <a name="manage-your-production-servers"></a>Gerir os servidores de produção
Para gerir os servidores de produção, clique em **definições**.

Em Gerir clique **infraestrutura de cópia de segurança > servidores de produção**.

As listas de menu de servidores de produção de todos os servidores de produção disponíveis. Clique num servidor na lista para abrir os detalhes do servidor.

![Itens protegidos](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Abra o agente do Backup do Azure
Abra o **agente de cópia de segurança do Microsoft Azure** (encontrá-lo ao pesquisar na máquina de *cópia de segurança do Microsoft Azure*).

![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Do **ações** disponível no lado direito da consola do agente de cópia de segurança efetua as seguintes tarefas de gestão:

* Registar servidor
* Agendar cópia de segurança
* Criar cópias de segurança agora
* Alterar propriedades

![Ações de consola do agente de cópia de segurança do Microsoft Azure](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Para **recuperar dados**, consulte [restaurar os ficheiros para uma máquina de cliente do Windows ou do Windows server](backup-azure-restore-windows-server.md).
>
>

## <a name="modify-the-backup-schedule"></a>Modificar a agenda da cópia de segurança
1. No agente de cópia de segurança do Microsoft Azure, clique em **Agendar cópia de segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. No **Assistente de cópia de segurança de agenda** deixe o **efetuar alterações aos itens de cópia de segurança ou vezes** opção selecionada e clique em **seguinte**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Se pretender adicionar ou alterar itens, no **selecionar itens para cópia de segurança** ecrã clique **adicionar itens**.

    Também pode definir **definições de exclusão** nesta página do assistente. Se pretende excluir ficheiros ou tipos de ficheiro Leia o procedimento para adicionar [definições de exclusão](#manage-exclusion-settings).
4. Selecione os ficheiros e pastas que pretende criar cópias de segurança e clique em **OK**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Especifique o **agenda de cópia de segurança** e clique em **seguinte**.

    Pode agendar diárias (no máximo, 3 vezes por dia) ou cópias de segurança semanais.

    ![Itens para Cópia de Segurança do Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Especificar a agenda de cópia de segurança é explicado detalhadamente deste [artigo](backup-azure-backup-cloud-as-tape.md).
   >

6. Selecione o **política de retenção** para a cópia de segurança e clique em **seguinte**.

    ![Itens para Cópia de Segurança do Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. No **confirmação** ecrã reveja as informações e clique em **concluir**.
8. Depois de concluído o Assistente de criação de **agenda de cópia de segurança**, clique em **fechar**.

    Depois de modificar proteção, pode confirmar que as cópias de segurança estão a acionar corretamente acedendo ao **tarefas** separador e confirmar que as alterações são refletidas das tarefas de cópia de segurança.

## <a name="enable-network-throttling"></a>Ativar a limitação de rede

O agente de cópia de segurança do Azure fornece um separador de limitação que lhe permite controlar como a largura de banda de rede é utilizada durante a transferência de dados. Este controlo pode ser útil se precisar de fazer uma cópia de segurança de dados durante as horas de trabalho, mas não pretendem que o processo de cópia de segurança interfira com outro tráfego de internet. Limitação de dados transferência aplica-se a cópia de segurança e restaurar as atividades.  

Para ativar a limitação:

1. No **agente de cópia de segurança**, clique em **alterar propriedades**.
2. No * * limitação separador, selecione **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet**.

    ![Limitação de rede](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Assim que tiver ativado a limitação, especificar a largura de banda permitida para transferência de dados de cópia de segurança durante **as horas de trabalho** e **horas de descanso**.

    Os valores de largura de banda começam em 512 quilobytes por segundo (Kbps) e podem ir até 1023 megabytes por segundo (Mbps). Também pode designar o início e concluir para **as horas de trabalho**, e os dias da semana são considerados trabalho dias. A hora fora das horas de trabalho designadas é considerada horas de descanso.
3. Clique em **OK**.

## <a name="manage-exclusion-settings"></a>Gerir definições de exclusão
1. Abra o **agente de cópia de segurança do Microsoft Azure** (pode encontrá-lo ao pesquisar na máquina de *cópia de segurança do Microsoft Azure*).

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. No agente de cópia de segurança do Microsoft Azure, clique em **Agendar cópia de segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. No deixar Assistente de cópia de segurança para agendar a **efetuar alterações aos itens de cópia de segurança ou vezes** opção selecionada e clique em **seguinte**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Clique em **exclusões definições**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Clique em **Adicionar exclusão**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Selecione a localização e, em seguida, clique em **OK**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Adicionar a extensão de ficheiro no **tipo de ficheiro** campo.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Adicionar uma extensão MP3;.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Para adicionar outra extensão, clique em **Adicionar exclusão** e introduza outra extensão de tipo de ficheiro (adicionar uma extensão de JPEG).

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Quando adicionar todas as extensões, clique em **OK**.
9. Continuar com o Assistente de cópia de segurança de agenda clicando **seguinte** até que o **página de confirmação**, em seguida, clique em **concluir**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Q1. O estado da tarefa de cópia de segurança mostra como concluído no agente de cópia de segurança do Azure, por que motivo não-obter se refletir imediatamente no portal?**

R1. Não existe está em atraso máximo de 15 min entre o estado da tarefa de cópia de segurança será refletido no agente de cópia de segurança do Azure e o portal do Azure.

**Q.2 quando ocorre uma falha de uma tarefa de cópia de segurança, quanto tempo demora para emitir um alerta?**

É desencadeado um alerta A.2 dentro de 20 minutos da falha de cópia de segurança do Azure.

**Q3. Existe um caso onde não será enviado um e-mail se estiverem configuradas notificações?**

R3. Abaixo são os casos, quando a notificação não será enviada para reduzir o ruído de alertas:

* Se as notificações estão configuradas por hora e um alerta é gerado e resolvido dentro da hora
* Tarefa foi cancelada.
* Segunda tarefa de cópia de segurança falhou porque está em progresso da tarefa de cópia de segurança original.

## <a name="troubleshooting-monitoring-issues"></a>Resolução de problemas de monitorização
**Problema:** tarefas e/ou os alertas do Azure Backup agent não aparecem no portal.

**Passos de resolução de problemas:** o processo, ```OBRecoveryServicesManagementAgent```, envia os dados de tarefa e alerta para o serviço de cópia de segurança do Azure. Ocasionalmente, este processo pode ficar bloqueado ou encerramento.

1. Para verificar o processo não está em execução, abra **Gestor de tarefas** e verifique se o ```OBRecoveryServicesManagementAgent``` processo está em execução.
2. Partindo do princípio de que o processo de mensagens em fila não está em execução, abra **painel de controlo** e navegar na lista de serviços. Iniciar ou reinicie **o agente de gestão do Microsoft Azure Recovery Services**.

    Para obter mais informações, procure os registos em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passos seguintes
* [Restaurar o Windows Server ou o cliente do Windows a partir do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Backup do Azure, consulte [descrição geral de cópia de segurança do Azure](backup-introduction-to-azure-backup.md)
* Visite o [fórum de cópia de segurança do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
