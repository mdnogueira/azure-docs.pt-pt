---
title: "Tarefas de cópia de segurança do Snapshot Manager do StorSimple | Microsoft Docs"
description: "Descreve como utilizar o snap-in MMC de Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança agendadas, atualmente em execução e foi concluídas."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Utilize o Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança

## <a name="overview"></a>Descrição geral
O **tarefas** no nó de **âmbito** painel mostra o **agendada**, **últimas 24 horas**, e **em execução** tarefas iniciadas interativamente ou por uma política configurada de cópia de segurança. 

Este tutorial explica como pode utilizar o **tarefas** nó para apresentar informações sobre tarefas de cópia de segurança agendadas, recentes e em execução. (É apresentada a lista de tarefas e informações correspondentes no **resultados** painel.) Além disso, pode uma tarefa listada com o botão direito e ver um menu de contexto, que apresenta uma lista de ações disponíveis.

## <a name="view-scheduled-jobs"></a>Ver tarefas agendadas
Utilize o procedimento seguinte para ver tarefas de cópia de segurança agendadas.

#### <a name="to-view-scheduled-jobs"></a>Para ver tarefas agendadas
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No **âmbito** painel, expanda o **tarefas** nó e clique em **agendada**. As seguintes informações aparecem no **resultados** painel:
   
   * **Nome** – o nome do instantâneo agendado
   * **Em seguida execute** – a data e hora do próximo instantâneo agendado
   * **Última execução** – a data e hora do instantâneo agendado mais recente
     
     > [!NOTE]
     > Instantâneos apenas uma única, o **executar seguinte** e **executar último** será a mesma.
     
     ![Tarefas de cópia de segurança agendadas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Para efetuar ações adicionais para uma tarefa específica, clique com botão direito no nome da tarefa no **resultados** painel e selecione uma das opções de menu.

## <a name="view-recent-jobs"></a>Vista de tarefas recentes
Utilize o procedimento seguinte para ver a cópia de segurança e restaurar as tarefas que foram concluídas nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para ver tarefas recentes
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **tarefas** nó e clique em **últimas 24 horas**. O **resultados** painel mostra as tarefas de cópia de segurança para as últimas 24 horas (para um máximo de 64 tarefas). As seguintes informações aparecem no **resultados** painel, consoante o **vista** opções especificar:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e hora quando o instantâneo começou.
   * **Parado** – a data e hora quando o instantâneo foi concluída ou terminado.
   * **Decorrido** – a quantidade de tempo entre o **iniciado** e **parado** vezes.
   * **Estado** – o estado da tarefa recentemente completa. **Êxito** indica que a cópia de segurança foi criada com êxito. **Não foi possível** indica que a tarefa não foi executado com êxito.
   * **Informações** – o motivo da falha.
   * **Bytes processado (MB)** – a quantidade de dados do grupo de volume que foi processado (em MB). 
     
     ![Tarefas que tenha sido executada nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Para efetuar ações adicionais para uma tarefa específica, clique com botão direito no nome da tarefa no **resultados** painel e selecione uma das opções de menu.
   
    ![Eliminar uma tarefa](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Vista de tarefas atualmente em execução
Utilize o procedimento seguinte para ver as tarefas atualmente em execução.

#### <a name="to-view-currently-running-jobs"></a>Para ver tarefas atualmente em execução
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, expanda o **tarefas** nó e clique em **executar**. Consoante o **vista** opções especificar, as seguintes informações aparecem no **resultados** painel:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e hora quando o instantâneo começou.
   * **Ponto de verificação** – a ação atual da cópia de segurança.
   * **Estado** – a percentagem de conclusão.
   * **Decorrido** – a quantidade de tempo que foi efectuada com êxito uma vez que iniciou a cópia de segurança. 
   * **Débito médio (MB)** – rácio de total de bytes de dados processados para do total de tempo decorrido para processar (MB).
   * **Bytes processado (MB)** – total de bytes de dados processados (em MB).
   * **Bytes escritos (MB)** – total de bytes de dados escritos (em MB). Inclui os dados, bem como os metadados e, por conseguinte, é normalmente maior do que o processamento de Bytes.
     
     ![Tarefas atualmente em execução](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Para efetuar ações adicionais para uma tarefa específica, clique com botão direito no nome da tarefa no **resultados** painel e selecione uma das opções de menu.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar Snapshot Manager do StorSimple para gerir o catálogo de cópias de segurança](storsimple-snapshot-manager-manage-backup-catalog.md).

