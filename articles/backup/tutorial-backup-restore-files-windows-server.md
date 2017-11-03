---
title: Recuperar ficheiros a partir do Azure para um servidor do Windows | Microsoft Docs
description: "Este tutorial fornece detalhes sobre itens de recuperação a partir do Azure para um servidor do Windows."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "criar cópias de segurança do servidor do Windows; restaurar ficheiros windows servidor; cópia de segurança e recuperação após desastre"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: saurabhsensharma;markgal;
ms.custom: 
ms.openlocfilehash: 28e0bc1414b0fea614f217dc3adf1484c1374018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Recuperar ficheiros a partir do Azure para um servidor do Windows

Cópia de segurança do Azure permite a recuperação dos itens individuais de cópias de segurança do seu Windows Server. Recuperação de ficheiros individuais é útil se rapidamente deve restaurar os ficheiros que são eliminados acidentalmente. Este tutorial abrange como pode utilizar o agente do Microsoft Azure Recovery Services Agent (MARS) para recuperar os itens de cópias de segurança que já tiver efetuado no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Iniciar a recuperação dos itens individuais 
> * Selecione um ponto de recuperação 
> * Restaurar itens a partir de um ponto de recuperação

Este tutorial assume que já executou os passos para [cópia de segurança um Windows Server para o Azure](backup-configure-vault.md) e ter, pelo menos, uma cópia de segurança dos ficheiros do Windows Server no Azure.

## <a name="initiate-recovery-of-individual-items"></a>Iniciar a recuperação dos itens individuais

Um Assistente de interface de utilizador útil com o nome de cópia de segurança do Microsoft Azure está instalado com o agente de serviços de recuperação do Azure (MARS) da Microsoft. O Assistente de cópia de segurança do Microsoft Azure funciona com o agente de serviços de recuperação do Azure (MARS) da Microsoft para obter dados de cópia de segurança a partir dos pontos de recuperação armazenados no Azure. Utilize o Assistente de cópia de segurança do Microsoft Azure para identificar os ficheiros ou pastas que pretende restaurar para o Windows Server. 

1. Abra o **cópia de segurança do Microsoft Azure** snap-in. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. No assistente, clique em **recuperar dados** no **painel ações** da consola do agente para iniciar o **recuperar dados** assistente.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. No **introdução** página, selecione **neste servidor (nome do servidor)** e clique em **seguinte**.

4. No **selecionar modo de recuperação** página, selecione **ficheiros e pastas individuais** e, em seguida, clique em **seguinte** para iniciar o processo de seleção de ponto de recuperação.
 
5. No **data e selecione o Volume** página, selecione o volume que contém os ficheiros ou pastas que pretende restaurar e clique em **montar**. Selecione uma data e selecione uma hora no menu pendente que corresponde a um ponto de recuperação. As datas em **negrito** indicar a disponibilidade de, pelo menos, um ponto de recuperação nesse dia.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Ao clicar em **montar**, cópia de segurança do Azure faz com que o ponto de recuperação disponível como um disco. Procurar e recuperar ficheiros a partir do disco.

## <a name="restore-items-from-a-recovery-point"></a>Restaurar itens a partir de um ponto de recuperação

1. Assim que o volume de recuperação se encontra instalado, clique em **procurar** para abrir o Explorador do Windows e localizar os ficheiros e pastas que pretende recuperar. 

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Pode abrir os ficheiros diretamente a partir do volume de recuperação e certifique-se os ficheiros.

2. No Explorador do Windows, copie os ficheiros de e/ou pastas que pretende restaurar e cole-os para qualquer localização pretendida no servidor.

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Quando tiver terminado de restauro de ficheiros e pastas, no **procurar e ficheiros de recuperação** página do **recuperar dados** assistente, clique em **Unmount**. 

    ![Cópia de segurança pendente](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Clique em **Sim** para confirmar que pretende que o desmontar o volume.

    Depois do instantâneo é desmontado, **tarefa concluída** aparece no **tarefas** painel na consola do agente.

## <a name="next-steps"></a>Passos seguintes

Isto conclui os tutoriais na cópia de segurança e restaurar dados do Windows Server para o Azure. Para saber mais sobre o Backup do Azure, consulte o exemplo do PowerShell para efetuar cópia de segurança encriptado das máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar cópias de segurança encriptado VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
