---
title: Ver e gerir tarefas do StorSimple | Microsoft Docs
description: "Descreve a página de tarefas do serviço StorSimple Manager e como utilizá-la para controlar as tarefas de cópia de segurança agendadas, recentes e atuais."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 55922cd0-d490-48eb-938a-012a67c1c09e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 7d9377bb8f3cb8c587823c2d71d61dfb9b50f52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Utilizar o serviço StorSimple Manager para ver e gerir tarefas do StorSimple
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Descrição geral
O **tarefas** página fornece um único portal central para visualização e gestão de tarefas que foram iniciadas em dispositivos ligados ao serviço StorSimple Manager. Pode ver tarefas agendadas, em execução, concluídas e com falhas para vários dispositivos. Os resultados são apresentados em formato tabular. 

![Página das tarefas](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Pode encontrar rapidamente as tarefas que estão interessadas nas filtrando campos, tais como:

* **Estado** – tarefas podem estar em execução agendada, falhada, concluída, cancelamento ou cancelada.
* **Tipo** – tarefas podem ser criadas devido a um agendada ou uma cópia de segurança a pedido (**efetuar cópia de segurança**), a clonagem, um restauro de dispositivo ou uma operação de atualização.
* **Dispositivos** – tarefas são iniciadas num determinado dispositivo ligada ao seu serviço.
* **A partir de e para** – tarefas podem ser filtradas com base no intervalo de data e hora.

As tarefas, em seguida, são apresentadas on the basis of os seguintes atributos:

* **Tipo** – cópia de segurança, clone, restauro, ativação pós-falha ou atualização.
* **Estado** – em execução, agendada, falha, concluída, cancelamento ou cancelada.
* **Entidade** – as tarefas podem ser associadas um volume, uma política de cópia de segurança ou um dispositivo. Uma tarefa de clone está associada um volume, enquanto uma tarefa de cópia de segurança agendada é associada a uma política de cópia de segurança. Como resultado de uma recuperação após desastre (DR) ou uma operação de restauro, é criada uma tarefa de dispositivo.
* **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.
* **Foi iniciada no** – a hora quando a tarefa foi iniciada.
* **Progresso** – a percentagem de conclusão de uma tarefa em execução. Para uma tarefa concluída, isto deve ser sempre 100%.

A lista de tarefas é atualizada a cada 30 segundos.

Pode efetuar as seguintes ações relacionadas com a tarefa nesta página:

* Ver detalhes da tarefa
* Cancelar uma tarefa

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para visualizar os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes da tarefa
1. No **tarefas** página, apresentar as tarefas estão interessadas ao executar uma consulta com filtros adequados. Pode pesquisar para concluir, em execução ou cancelar tarefas.
2. Selecione uma tarefa.
3. Na parte inferior da página, clique em **detalhes**.
4. No **detalhes da tarefa de cópia de segurança** caixa de diálogo, pode ver o estado, obter detalhes, as estatísticas de tempo e estatísticas de dados.

## <a name="cancel-a-job"></a>Cancelar uma tarefa
Execute os seguintes passos para cancelar uma tarefa em execução.

### <a name="to-cancel-a-job"></a>Para cancelar uma tarefa
1. No **tarefas** página, apresentar as tarefas em execução que pretende cancelar ao executar uma consulta com filtros adequados.
2. Selecione a tarefa.
3. Na parte inferior da página, clique em **Cancelar**.
4. Quando lhe for pedida a confirmação, clique em **Sim**.

Esta tarefa agora, será cancelada.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [gerir as políticas de cópia de segurança do StorSimple](storsimple-manage-backup-policies.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

