---
title: "Gerir cofres de cópia de segurança do Azure e os servidores do Azure utilizando o modelo de implementação clássica | Microsoft Docs"
description: "Utilize este tutorial para saber como gerir cofres de cópia de segurança do Azure e os servidores."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: markgal;
ms.openlocfilehash: ecc2eb996546810dad169dc25175ab10ebb164aa
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Gerir cofres de segurança e servidores do Azure Backup com o modelo de implementação clássica
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-manage-windows-server.md)
> * [Clássico](backup-azure-manage-windows-server-classic.md)
>
>

Neste artigo, encontrará uma descrição geral das cópia de segurança das tarefas de gestão disponíveis através do portal clássico do Azure e o agente de cópia de segurança do Microsoft Azure.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

> [!IMPORTANT]
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 30 de Novembro de 2017, não poderá utilizar o PowerShell para criar cofres de cópia de segurança. **Até 30 de Novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>

## <a name="management-portal-tasks"></a>Tarefas de portais de gestão
1. Iniciar sessão para o [Portal de gestão](https://manage.windowsazure.com).
2. Clique em **dos serviços de recuperação**, em seguida, clique no nome do Cofre de cópias de segurança para ver a página início rápido.

    ![Serviços de recuperação](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Ao selecionar as opções na parte superior da página início rápido, pode ver as tarefas de gestão disponíveis.

![Gerir separadores](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Dashboard
Selecione **Dashboard** para ver a descrição geral da utilização para o servidor. O **descrição geral da utilização** inclui:

* O número de servidores de Windows registado na nuvem
* O número de máquinas virtuais do Azure protegido na nuvem
* O armazenamento total consumido no Azure
* O estado de tarefas recentes

Na parte inferior do Dashboard pode efetuar as seguintes tarefas:

* **Gerir certificados** - se de um certificado que foi utilizado para registar o servidor, em seguida, utilize esta opção para atualizar o certificado. Se estiver a utilizar as credenciais do cofre, não utilize **gerir certificado**.
* **Eliminar** -elimina o Cofre de cópia de segurança atual. Se já não está a ser utilizado um cofre de cópia de segurança, pode eliminá-lo para libertar espaço de armazenamento. **Eliminar** só é ativada após todos os servidores registados tem sido eliminados do cofre.

![Tarefas de cópia de segurança de dashboard](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Itens registados
Selecione **itens registados** para ver os nomes dos servidores que estão registados neste cofre.

![Itens registados](./media/backup-azure-manage-windows-server-classic/registered-items.png)

O **tipo** filtrar as predefinições para máquinas de virtuais do Azure. Para ver os nomes dos servidores que estão registados neste cofre, selecione **do Windows server** no menu pendente.

Aqui pode efetuar as seguintes tarefas:

* **Permitir rerregisto** - quando esta opção está selecionada para um servidor pode utilizar o **Assistente de registo** no agente de cópia de segurança do Microsoft Azure no local para registar o servidor com a cópia de segurança do cofre uma segunda vez. Poderá ter de voltar a registar devido a um erro no certificado ou se um servidor que tiveram de ser reconstruídos.
* **Eliminar** -elimina um servidor a partir do Cofre de cópia de segurança. Todos os dados armazenados associados ao servidor são eliminados imediatamente.

    ![Tarefas de itens registados](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Itens protegidos
Selecione **itens protegidos** para ver os itens que foram efetuadas cópias de de servidores.

![Itens protegidos](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Configurar
Do **configurar** separador, pode selecionar a opção de redundância de armazenamento adequado. O melhor momento para selecionar a opção de redundância do armazenamento é mais adequado depois de criar um cofre e antes de quaisquer máquinas estão registadas.

> [!WARNING]
> Depois de um item ter sido registado no cofre, a opção de redundância de armazenamento está bloqueada e não pode ser modificada.
>
>

![Configurar](./media/backup-azure-manage-windows-server-classic/configure.png)

Consulte este artigo para obter mais informações [redundância do armazenamento](../storage/common/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Tarefas de agente de cópia de segurança do Microsoft Azure
### <a name="console"></a>Consola
Abra o **agente de cópia de segurança do Microsoft Azure** (pode encontrá-lo ao pesquisar na máquina de *cópia de segurança do Microsoft Azure*).

![Agente de cópia de segurança](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Do **ações** disponível no lado direito da consola do agente de cópia de segurança pode realizar as seguintes tarefas de gestão:

* Registar servidor
* Agendar cópia de segurança
* Criar cópias de segurança agora
* Alterar propriedades

![Ações do agente de consola](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> Para **recuperar dados**, consulte [restaurar os ficheiros para uma máquina de cliente do Windows ou do Windows server](backup-azure-restore-windows-server.md).
>
>

### <a name="modify-an-existing-backup"></a>Modificar uma cópia de segurança existente
1. No agente de cópia de segurança do Microsoft Azure, clique em **Agendar cópia de segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. No **Assistente de cópia de segurança de agenda** deixe o **efetuar alterações aos itens de cópia de segurança ou vezes** opção selecionada e clique em **seguinte**.

    ![Modificar uma cópia de segurança agendada](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Se pretender adicionar ou alterar itens, no **selecionar itens para cópia de segurança** ecrã clique **adicionar itens**.

    Também pode definir **definições de exclusão** nesta página do assistente. Se pretende excluir ficheiros ou tipos de ficheiro Leia o procedimento para adicionar [definições de exclusão](#exclusion-settings).
4. Selecione os ficheiros e pastas que pretende criar cópias de segurança e clique em **OK**.

    ![Adicionar itens](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Especifique o **agenda de cópia de segurança** e clique em **seguinte**.

    Pode agendar diárias (no máximo, 3 vezes por dia) ou cópias de segurança semanais.

    ![Especifique a agenda de cópia de segurança](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Especificar a agenda de cópia de segurança é explicado detalhadamente deste [artigo](backup-azure-backup-cloud-as-tape.md).
   >
   >
6. Selecione o **política de retenção** para a cópia de segurança e clique em **seguinte**.

    ![Selecione a política de retenção](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. No **confirmação** ecrã reveja as informações e clique em **concluir**.
8. Depois de concluído o Assistente de criação de **agenda de cópia de segurança**, clique em **fechar**.

    Depois de modificar proteção, pode confirmar que as cópias de segurança estão a acionar corretamente acedendo ao **tarefas** separador e confirmar que as alterações são refletidas das tarefas de cópia de segurança.

### <a name="enable-network-throttling"></a>Ativar a limitação de rede
O agente de cópia de segurança do Azure fornece um separador de limitação que lhe permite controlar como a largura de banda de rede é utilizada durante a transferência de dados. Este controlo pode ser útil se precisar de fazer uma cópia de segurança de dados durante as horas de trabalho, mas não pretendem que o processo de cópia de segurança interfira com outro tráfego de internet. Limitação de dados transferência aplica-se a cópia de segurança e restaurar as atividades.  

Para ativar a limitação:

1. No **agente de cópia de segurança**, clique em **alterar propriedades**.
2. Selecione o **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet** caixa de verificação.

    ![Limitação de rede](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. Assim que tiver ativado a limitação, especificar a largura de banda permitida para transferência de dados de cópia de segurança durante **as horas de trabalho** e **horas de descanso**.

    Os valores de largura de banda começam em 512 quilobytes por segundo (Kbps) e podem ir até 1023 megabytes por segundo (Mbps). Também pode designar o início e concluir para **as horas de trabalho**, e os dias da semana são considerados trabalho dias. A hora fora das horas de trabalho designadas é considerada horas de descanso.
4. Clique em **OK**.

## <a name="exclusion-settings"></a>Definições de exclusão
1. Abra o **agente de cópia de segurança do Microsoft Azure** (pode encontrá-lo ao pesquisar na máquina de *cópia de segurança do Microsoft Azure*).

    ![Agente de cópia de segurança aberta](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. No agente de cópia de segurança do Microsoft Azure, clique em **Agendar cópia de segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. No deixar Assistente de cópia de segurança para agendar a **efetuar alterações aos itens de cópia de segurança ou vezes** opção selecionada e clique em **seguinte**.

    ![Modificar uma agenda](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Clique em **exclusões definições**.

    ![Selecionar itens a serem excluídos](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Clique em **Adicionar exclusão**.

    ![Adicionar exclusões](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Selecione a localização e, em seguida, clique em **OK**.

    ![Selecione uma localização para exclusão](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Adicionar a extensão de ficheiro no **tipo de ficheiro** campo.

    ![Excluir por tipo de ficheiro](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Adicionar uma extensão MP3;.

    ![Exemplo de tipo de ficheiro](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Para adicionar outra extensão, clique em **Adicionar exclusão** e introduza outra extensão de tipo de ficheiro (adicionar uma extensão de JPEG).

    ![Outro exemplo de tipo de ficheiro](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. Quando adicionar todas as extensões, clique em **OK**.
9. Continuar com o Assistente de cópia de segurança de agenda clicando **seguinte** até que o **página de confirmação**, em seguida, clique em **concluir**.

    ![Confirmação de exclusão](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Passos seguintes
* [Restaurar o Windows Server ou o cliente do Windows a partir do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Backup do Azure, consulte [descrição geral de cópia de segurança do Azure](backup-introduction-to-azure-backup.md)
* Visite o [fórum de cópia de segurança do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
