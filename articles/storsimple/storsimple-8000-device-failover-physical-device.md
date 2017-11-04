---
title: "Ativação pós-falha de StorSimple, recuperação após desastre para um dispositivo físico a série 8000 do StorSimple | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha 8000 do StorSimple série dispositivo físico para outro dispositivo físico."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Ativação pós-falha para um dispositivo físico a série 8000 do StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo físico a série 8000 do StorSimple para o dispositivo físico StorSimple outro se não houver um desastre. StorSimple utiliza a funcionalidade de ativação pós-falha do dispositivo para migrar dados a partir de um dispositivo físico de origem no Centro de dados para outro dispositivo físico. As orientações neste tutorial aplica-se a série 8000 do StorSimple dispositivos físicos com versões do software Update 3 e posterior.

Para saber mais sobre a ativação pós-falha do dispositivo e como são utilizadas para recuperar de desastres, aceda a [ativação pós-falha e recuperação após desastre para os dispositivos de série 8000 do StorSimple](storsimple-8000-device-failover-disaster-recovery.md).

Para efetuar a ativação pós-falha de um dispositivo físico StorSimple para uma aplicação de nuvem do StorSimple, aceda a [efetuar a ativação pós-falha para uma aplicação de nuvem do StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Para efetuar a ativação pós-falha de um dispositivo físico para si próprio, aceda a [falhar ao mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações de ativação pós-falha do dispositivo. Para obter mais informações, aceda a [considerações comuns para ativação pós-falha do dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Tem de ter um dispositivo físico de 8000 do StorSimple série implementado no Centro de dados. O dispositivo tem de executar atualização 3 ou versão posterior do software. Para obter mais informações, aceda a [implementar o dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Passos para efetuar a ativação pós-falha para um dispositivo físico

Execute os seguintes passos para restaurar o seu dispositivo para um dispositivo físico de destino.

1. Certifique-se de que o contentor de volume que pretende efetuar a ativação pós-falha associou instantâneos de nuvem. Para obter mais informações, aceda a [serviço Gestor de dispositivos do StorSimple utilizado para criar cópias de segurança](storsimple-8000-manage-backup-policies-u2.md).
2. Vá para o Gestor de dispositivos do StorSimple e, em seguida, clique em **dispositivos**. No **dispositivos** painel, aceda à lista de dispositivos ligados com o serviço.
    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selecione e clique o dispositivo de origem. O dispositivo de origem tem os contentores de volume que pretende efetuar a ativação pós-falha. Aceda a **definições > contentores de Volume**.
4. Selecione um contentor de volume que pretende efetuar a ativação pós-falha para outro dispositivo. Clique no contentor de volume para apresentar a lista de volumes dentro deste contentor. Selecione um volume, rato e clique em **Colocar Offline** para colocar offline o volume. Repita este processo para todos os volumes no contentor de volume.
5. Repita o passo anterior para todos os contentores de volume que pretende efetuar a ativação pós-falha para outro dispositivo.
6. Volte à **dispositivos** painel. Na barra de comandos, clique em **falhar**.
    ![Clique em ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. No **falhar** painel, execute os seguintes passos:
   
   1. Clique em **origem**. São apresentados os contentores de volume com volumes associados com instantâneos de nuvem. Apenas os contentores apresentados são elegíveis para ativação pós-falha. Na lista de contentores de volume, selecione os contentores de volume que pretende efetuar a ativação pós-falha. **Apenas os contentores de volume com instantâneos de nuvem associados e offline volumes são apresentados.**

       ![Selecione a origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Clique em **destino**. Para os contentores de volume selecionados no passo anterior, selecione um dispositivo de destino na lista pendente de dispositivos disponíveis. Apenas os dispositivos que tem capacidade suficiente para acomodar os contentores de volume de origem são apresentados na lista.

        ![Selecione o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Por fim, consultar todas as definições de ativação pós-falha em **resumo**. Depois de rever as definições, selecione a caixa de verificação que indica que os volumes nos contentores de volume selecionado estão offline. Clique em **OK**.

       ![Reveja as definições de ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple cria uma tarefa de ativação pós-falha. Clique na notificação da tarefa para monitorizar a tarefa de ativação pós-falha através do **tarefas** painel.

    Se o contentor de volume, ativação pós-falha tem volumes locais, em seguida, pode ver as tarefas de restauro individuais para cada volume local (e não para volumes em camadas) no contentor. Estes restauro tarefas poderão demorar bastante algum tempo a concluir. É provável que a tarefa de ativação pós-falha pode ser anterior. Estes volumes terão garantias locais apenas depois das tarefas de restauro estejam concluídas.

    ![Tarefa de ativação pós-falha de monitorização](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Uma vez concluída a ativação pós-falha, vá para o **dispositivos** painel.
   
   1. Selecione o dispositivo que foi utilizado como o dispositivo de destino para o processo de ativação pós-falha.

       ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Vá para o **contentores de Volume** painel. Todos os contentores volume, juntamente com os volumes do dispositivo antigo, deverá estar listados.

       ![Contentores de volume de destino de vista](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Passos seguintes

* Depois de ter de efetuar uma ativação pós-falha, poderá ser necessário [desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para informações sobre como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

