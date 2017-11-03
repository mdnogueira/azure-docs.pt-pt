---
title: "Ativação pós-falha de StorSimple, recuperação após desastre para os dispositivos de 8000 série | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha do dispositivo StorSimple ao mesmo dispositivo."
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Efetuar a ativação pós-falha do dispositivo físico StorSimple ao mesmo dispositivo

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo físico a série 8000 do StorSimple para si próprio se ocorrer um desastre. StorSimple utiliza a funcionalidade de ativação pós-falha do dispositivo para migrar dados a partir de um dispositivo físico de origem no Centro de dados para outro dispositivo físico. As orientações neste tutorial aplica-se a série 8000 do StorSimple dispositivos físicos com versões do software Update 3 e posterior.

Para saber mais sobre a ativação pós-falha do dispositivo e como são utilizadas para recuperar de desastres, aceda a [ativação pós-falha e recuperação após desastre para os dispositivos de série 8000 do StorSimple](storsimple-8000-device-failover-disaster-recovery.md).

Para efetuar a ativação pós-falha de um dispositivo físico para outro dispositivo físico, aceda a [falhar ao mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para efetuar a ativação pós-falha de um dispositivo físico StorSimple para uma aplicação de nuvem do StorSimple, aceda a [efetuar a ativação pós-falha para uma aplicação de nuvem do StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações de ativação pós-falha do dispositivo. Para obter mais informações, aceda a [considerações comuns para ativação pós-falha do dispositivo](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Passos para efetuar a ativação pós-falha para o mesmo dispositivo

Se precisar de efetuar a ativação pós-falha para o mesmo dispositivo, execute os seguintes passos.

1. Criar instantâneos de nuvem de todos os volumes no seu dispositivo. Para obter mais informações, aceda a [serviço Gestor de dispositivos do StorSimple utilizado para criar cópias de segurança](storsimple-8000-manage-backup-policies-u2.md).
2. Repor o seu dispositivo para as predefinições de fábrica. Siga as instruções detalhadas no [como repor um dispositivo StorSimple para as predefinições de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Vá para o serviço do Gestor de dispositivos do StorSimple e, em seguida, selecione **dispositivos**. No **dispositivos** painel, o dispositivo antigo deve mostrar como **Offline**.

    ![Dispositivo de origem offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configurar o dispositivo e registá-lo com o serviço do Gestor de dispositivos do StorSimple. O dispositivo recém-registado deve mostrar como **preparado para configurar**. O nome do dispositivo para o novo dispositivo é o mesmo que o dispositivo antigo, mas tem anexado um numeral para indicar que o dispositivo foi reposto para predefinição de fábrica e registado novamente.

    ![Dispositivo recentemente registado preparado para configurar](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Para o novo dispositivo, conclua a configuração do dispositivo. Para obter mais informações, aceda a [passo 4: concluir a configuração mínima do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). No **dispositivos** painel, o estado do dispositivo é alterado para **Online**.

   > [!IMPORTANT]
   > **Concluir a configuração mínima pela primeira vez, ou a DR poderão falhar.**

    ![Dispositivo recentemente registado online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecione o dispositivo antigo (estado offline) e na barra de comandos, clique em **falhar**. No **falhar** painel, selecione o dispositivo antigo como origem e especifique o dispositivo de destino como o dispositivo recentemente registado.

    ![Resumo de ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Para obter instruções detalhadas, consulte [ativação pós-falha para outro dispositivo físico](#fail-over-to-another-physical-device).

7. É criada uma tarefa de restauro do dispositivo, que pode monitorizar a partir do **tarefas** painel.

8. Depois da tarefa foi concluída com êxito, o novo dispositivo de acesso e navegue para o **contentores de Volume** painel. Certifique-se de que todos os contentores de volume do dispositivo antigo foram migradas para o novo dispositivo.

   ![Os contentores de volume migrado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Depois de concluída a ativação pós-falha, pode desativar e eliminar o dispositivo antigo do portal. Selecione o contexto de (offline), do dispositivo antigo e, em seguida, selecione **desativar**. Depois do dispositivo está desativado, o estado do dispositivo é atualizado.

     ![Dispositivo de origem desativado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecione o dispositivo desativado, rato e, em seguida, selecione **eliminar**. Isto elimina o dispositivo da lista de dispositivos.

    ![Dispositivo de origem eliminado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Passos seguintes

* Depois de ter de efetuar uma ativação pós-falha, poderá ser necessário [desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para informações sobre como utilizar o serviço do Gestor de dispositivos do StorSimple, aceda a [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

