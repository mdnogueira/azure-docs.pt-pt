---
title: "Ver e gerir tarefas de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve o painel de tarefas de serviço do Gestor de dispositivos do StorSimple e como utilizá-la para controlar as tarefas de cópia de segurança agendadas, recentes e atuais."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para ver e gerir tarefas (Update 3 e posterior)

## <a name="overview"></a>Descrição geral
O **tarefas** painel fornece um único portal central para visualização e gestão de tarefas que foram iniciadas em dispositivos ligada ao seu serviço do Gestor de dispositivos do StorSimple. Pode ver as tarefas agendadas, em execução, concluídas, canceladas e falhadas para vários dispositivos. Os resultados são apresentados em formato tabular.

![Painel de tarefas](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Pode encontrar rapidamente as tarefas que estão interessadas nas filtrando campos, tais como:

* **Estado** – tarefas podem estar em curso, foi concluída com êxito, cancelados, com falha, cancelamento ou criada com êxito com erros.
* **Intervalo de tempo** – tarefas podem ser filtradas com base no intervalo de data e hora. Os intervalos são decorridos desde 1 hora, após 24 horas, últimos 7 dias, após 30 dias, ano passado ou data personalizada.
* **Tipo** – o tipo de tarefa pode ser a cópia de segurança agendada, a cópia de segurança manual, a cópia de segurança de restauro, clonar volume, ativação pós-falha contentores de volume, Criar volume localmente afixado, modificar o volume, instalar atualizações, recolher registos de suporte e criar a aplicação de nuvem.
* **Dispositivos** – tarefas são iniciadas num determinado dispositivo ligada ao seu serviço.
  
As tarefas, em seguida, são apresentadas on the basis of os seguintes atributos:
  
* **Nome** – cópia de segurança agendada, manual de cópia de segurança, efetuar a cópia de segurança do restauro, clone volume, ativação pós-falha de contentores de volume, volume localmente afixado de criar, modificar o volume, instalar atualizações, recolher registos de suporte ou criar a aplicação de nuvem.
* **Estado** – em execução, concluída, cancelada, com falha, cancelamento ou foi concluída com erros.
* **Entidade** – as tarefas podem ser associadas um volume, uma política de cópia de segurança ou um dispositivo. Por exemplo, uma tarefa de clone está associada um volume, enquanto uma tarefa de cópia de segurança agendada é associada a uma política de cópia de segurança. Como resultado de uma recuperação após desastre (DR) ou uma operação de restauro, é criada uma tarefa de dispositivo.
* **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.
* **Iniciar** – a hora quando a tarefa foi iniciada.
* **Duração** – o tempo necessário para concluir a tarefa.

A lista de tarefas é atualizada a cada 30 segundos.

Pode efetuar as seguintes ações relacionadas com a tarefa nesta página:

* Ver detalhes da tarefa
* Cancelar uma tarefa

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para visualizar os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes da tarefa
1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple e, em seguida, clique em **tarefas**.

2. No **tarefas** painel, apresenta as tarefas estão interessadas ao executar uma consulta com filtros adequados. Pode pesquisar para concluir, em execução ou cancelar tarefas.

    ![Painel de tarefas](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecione e clique numa tarefa.

    ![Painel de tarefas](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. No painel de detalhes da tarefa, pode ver o estado, obter detalhes, as estatísticas de tempo e estatísticas de dados.
   
    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Cancelar uma tarefa
Execute os seguintes passos para cancelar uma tarefa em execução.

> [!NOTE]
> Algumas tarefas, tais como modificar um volume para alterar o tipo de volume ou expandir um volume, não podem ser canceladas.


### <a name="to-cancel-a-job"></a>Para cancelar uma tarefa
1. No **tarefas** página, apresentar as tarefas em execução que pretende cancelar ao executar uma consulta com filtros adequados. Selecione a tarefa.

2. Faça duplo clique na tarefa selecionada para invocar o menu de contexto e clique em **Cancelar**.

    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**. Esta tarefa agora, será cancelada.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [gerir as políticas de cópia de segurança do StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

