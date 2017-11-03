---
title: Ver e gerir tarefas de matriz Virtual StorSimple | Microsoft Docs
description: "Descreve a página de tarefas de serviço do Gestor de dispositivos do StorSimple e como utilizá-la para controlar as tarefas de recentes e atuais para a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para ver tarefas para a matriz de Virtual StorSimple
## <a name="overview"></a>Descrição geral
O **tarefas** painel fornece um portal central único para ver e gerir tarefas iniciadas no matrizes virtuais que estão ligadas ao seu serviço do Gestor de dispositivos do StorSimple. Pode ver as tarefas em execução, concluídas e falhadas para vários dispositivos virtuais. Os resultados são apresentados em formato tabular.

![Painel de tarefas](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Pode encontrar rapidamente as tarefas que estão interessadas nas filtrando campos, tais como:

* **Intervalo de tempo** – tarefas podem ser filtradas com base no intervalo de data e hora.
* **Dispositivos** – tarefas são iniciadas num dispositivo específico ligado ao seu serviço. As tarefas, em seguida, são apresentadas com base nos seguintes atributos:
  
  * **Nome** – o nome da tarefa pode ser **todos os**, **cópia de segurança**, **Clone**, **falhar**, **transferir as atualizações**, ou **instalar atualizações**.
  * **Estado** – tarefas podem ser **todos os**, **em curso**, **com êxito**, ou **falha**, ou **cancelado**.
  * **Entidade** – as tarefas podem ser associadas um volume, partilha ou dispositivo.
  * **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.
  * **Iniciar** – a hora quando a tarefa foi iniciada.
  * **Duração** – a duração para no qual a tarefa foi executada.
* **Estado** – pode procurar todos os, tarefas em execução, concluídas ou falhadas.
* **Tipo de tarefa** – o tipo de tarefa pode ser all, cópia de segurança, restauro, o ativação pós-falha, transferir as atualizações ou instalar atualizações.

A lista de tarefas é atualizada a cada 30 segundos.

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para visualizar os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes da tarefa
1. No **tarefas** painel, apresenta as tarefas estão interessadas ao executar uma consulta com filtros adequados. Pode procurar tarefas em execução ou foi concluídas.
2. Selecione uma tarefa da tabela lista de tarefas.
   
    ![Painel de tarefas](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Na parte inferior da página, clique em **detalhes**.
4. No **detalhes** caixa de diálogo, pode ver o estado, os detalhes e as estatísticas de tempo. A ilustração seguinte mostra um exemplo do **detalhes da tarefa de cópia de segurança** caixa de diálogo.
   
    ![Detalhes da tarefa](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas de tarefas quando a máquina virtual está em pausa no hipervisor
Quando uma tarefa está em curso na sua matriz de Virtual StorSimple e o dispositivo (máquina virtual aprovisionada no hipervisor) está em pausa para maior do que 15 minutos, a tarefa falha. Isto devido ao seu tempo de matriz Virtual StorSimple se encontra sincronizado com a hora do Microsoft Azure. 

Verá o seguinte erro: "o tempo de dispositivo está dessincronizado com a hora do Microsoft Azure por mais de 15 minutos. Certifique-se de que o hipervisor e o dispositivo horas estão sincronizadas com um NTP servier. Certifique-se de que existem não existem problemas de conectividade. Para resolver problemas de conectividade, executar testes de diagnóstico da web local da IU do seu dispositivo virtual."

Estas falhas aplicam-se às tarefas de cópia de segurança, restauro, atualização e ativação pós-falha. Se a máquina virtual é aprovisionada no Hyper-V, a máquina, eventualmente, sincroniza-se tempo com o hipervisor. Quando isto acontecer, pode reiniciar a tarefa.

## <a name="next-steps"></a>Passos seguintes
[Saiba como utilizar a IU da web local para administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

